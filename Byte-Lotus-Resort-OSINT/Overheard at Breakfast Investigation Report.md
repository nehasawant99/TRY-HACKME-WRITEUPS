# Overheard at Breakfast - OSINT Writeup

**Room:** https://tryhackme.com/room/hh-overheardatbreakfast-6f01793c

<img width="1446" height="358" alt="image" src="https://github.com/user-attachments/assets/4b24c635-8c5e-46df-a2e2-ae8960e4ef9d" />

## Objective
Analyse a leaked screenshot of a private conversation, extract the identifying details dropped within it, locate the hidden account being referenced, and recover the final flag.

---

## Skills Practised
- OSINT Investigation
- Hash Identification (MD5 / SHA256)
- Gravatar Reconnaissance
- Base64 Decoding
- Clue Analysis

---

## Investigation Steps

### Step 1 – Analyse the Conversation
The provided screenshot showed a Slack-style DM between two guests, **Ponzi - Influencer** and **Lambo!**, at the Byte Lotus Hotel.

Initial clues identified:
- Guest handle: **Lambo!**
- Mention of a free tool used to "upload profile and link other media accounts"
- The tool's name **"Started with a G"**
- An email address dropped mid-conversation: `lambobytelotushotel@gmail.com`

These hints pointed toward **Gravatar** (Globally Recognized Avatar) — a free service that ties a public profile, avatar, and linked social accounts to a hashed email address.

<img width="1175" height="781" alt="conversation" src="https://github.com/user-attachments/assets/fd41acb8-c219-46da-99e7-1478faf7524b" />

---

### Step 2 – Hash the Email
Gravatar profiles are looked up via a hash of the account's email address (MD5 historically, SHA256 in the current system).

Hashed `lambobytelotushotel@gmail.com`:

```
MD5:    d4a5fc5d3128890778667e24617d7cc0
SHA256: d43faafe9d7f056793bd037b8d6e321acad985c222d83775b10d6539e301e931
```

---

### Step 3 – Locate the Gravatar Profile
Visiting:

```
https://gravatar.com/d43faafe9d7f056793bd037b8d6e321acad985c222d83775b10d6539e301e931
```

Resolved to **Lambo's** public Gravatar profile — the "hidden account nobody was supposed to find" referenced in the room briefing.


---

### Step 4 – Recover the Flag
The Gravatar profile contained a Base64-encoded string:

```
VEhNe1MzY3JlVF9QcjBmaWwzX0g0c19iMzNuX0lkZW50MWZpM2R9
```
<img width="1460" height="674" alt="Screenshot 2026-08-03 at 8 00 52 PM" src="https://github.com/user-attachments/assets/a1742a62-0a20-4917-b50a-f034dfc784fe" />

---

### Step 5 – Decode the Message
Decoded using Base64:

```
THM{....}
```

---

## Tools Used
- Web Search
- Gravatar
- MD5 / SHA256 Hashing (Python `hashlib` / `md5sum`)
- Base64 Decoder

---

## Key Takeaways
- Casual details dropped in conversation (an email, a tool name) can be enough to unmask an account.
- Gravatar profiles are keyed off hashed emails — knowing an email is often enough to find someone's public footprint.
- Always try both MD5 and SHA256 when hash-hunting, since Gravatar supports both.
- Base64 remains a common lightweight encoding for flags in beginner OSINT rooms.
