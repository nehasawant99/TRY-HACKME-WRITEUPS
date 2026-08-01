# Room 404 - Writeup

**Room:** https://tryhackme.com/room/hh-room404-804573bf

<img width="1447" height="418" alt="Screenshot 2026-08-01 at 12 25 12 PM" src="https://github.com/user-attachments/assets/dceaac53-d256-44f4-bdb7-8d2c6db71010" />


# Objective

Find the hidden Git repository, inspect its source code and commit history, and retrieve the hidden flag.

---

# Skills Practised

* Directory Enumeration
* Gobuster
* Git
* Source Code Review

---

# Investigation Steps

## Step 1 – Read the Challenge

The challenge hinted that something hidden was available on the web server.

The goal was to:

* Find hidden files or directories.
* Dump the source code.
* Find the flag.

<img width="704" height="450" alt="Screenshot 2026-08-01 at 12 21 45 PM" src="https://github.com/user-attachments/assets/d7866e0a-7af6-4d9d-9bef-2f3f85e0beed" />

---

## Step 2 – Scan the Website

I used **Gobuster** to look for hidden directories.

```bash
gobuster dir \
-u http://MACHINE_IP:8080 \
-w /usr/share/wordlists/dirb/common.txt
```

Result:

```text
/.git/HEAD
```
<img width="704" height="450" alt="Screenshot 2026-08-01 at 12 22 56 PM" src="https://github.com/user-attachments/assets/c5ecde7b-12d8-4fdd-bfd3-807a67fb9dfb" />

This showed that the website exposed its **Git repository**.

---

## Step 3 – View the Git Repository

I confirmed that the Git repository was accessible.

```bash
curl http://MACHINE_IP:8080/.git/HEAD
```

This indicated that the application's source code could be recovered.

<img width="704" height="197" alt="Screenshot 2026-08-01 at 12 23 16 PM" src="https://github.com/user-attachments/assets/b2e03a03-ffde-4b98-a7e2-ebb5b63060ad" />

Cloning Gitools

<img width="743" height="158" alt="Screenshot 2026-08-01 at 12 23 35 PM" src="https://github.com/user-attachments/assets/65efbce3-93b3-4173-9617-dd0ab30d135e" />

---

## Step 4 – Check Git History
Change to cd room404 and run the command ls -ls and grep -R "THM{". or grep -Ri "flag". Got nothing.
After dumping the Git repository, I viewed its commit history.

```bash
git log
```

Then I inspected the commit.

```bash
git show
```

The developer had accidentally left sensitive information inside a previous commit.

The flag was stored in the Git history.

<img width="743" height="532" alt="Screenshot 2026-08-01 at 12 24 23 PM" src="https://github.com/user-attachments/assets/aa8c62dc-695a-44a9-8c9c-16fdafe40afa" />

---

# What I Learned

* Hidden directories can be found using directory enumeration.
* A public `.git` directory exposes the application's source code.
* Even deleted information can remain inside Git commit history.
* Git history should always be reviewed during web assessments.

---

# Tools Used

* Gobuster
* Git
* cURL

---

# Key Takeaways

* Use Gobuster to discover hidden files and directories.
* Never expose the `.git` directory on a public web server.
* Review both the current source code and previous commits.
* Sensitive information should never be committed to Git repositories.

