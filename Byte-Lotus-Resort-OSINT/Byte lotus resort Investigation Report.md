# Byte Lotus Resort - OSINT Writeup


<img width="1461" height="417" alt="image" src="https://github.com/user-attachments/assets/4e67f9b0-f72a-45bf-aaef-dc5aeb539979" />


## Objective

Investigate the provided promotional brochure using basic OSINT techniques, follow the hidden clues, locate the concealed social media account, and recover the final flag.

---

## Skills Practised

- OSINT Investigation
- Social Media Reconnaissance
- Base64 Decoding
- Clue Analysis
- Information Gathering

---

## Investigation Steps

### Step 1 – Analyse the Brochure

Initial clues identified:

- Byte Lotus Resorts
- Concierge: Vera
- "Find us on Instagram or not."

These hints suggested searching for the resort on Instagram.



<img width="726" height="934" alt="thebrochure" src="https://github.com/user-attachments/assets/a8d4a391-89bb-42e5-9e51-3d2a1ac1004b" />


---

### Step 2 – Locate Instagram Account

Found:


<img width="797" height="799" alt="image" src="https://github.com/user-attachments/assets/f0f2e75b-f8e8-46a1-beef-c71420fa2ec2" />

```
@thebytelotusresort
```

The brochure also referenced **Vera**, leading to another account.

Found:


<img width="720" height="1600" alt="image" src="https://github.com/user-attachments/assets/989d6473-50cc-461b-b387-267f299e1657" />

```
@veratheconcierge
```

---

### Step 3 – Inspect Vera's Profile

The profile contained three posts.

Each post contained a fragment of a Base64-encoded string.

Part 1

<img width="720" height="1600" alt="WhatsApp Image 2026-07-25 at 11 36 20 AM" src="https://github.com/user-attachments/assets/89aa65c7-8e30-40f5-8df6-12c62e946458" />

```
VEhNe1YzckBzX2FD
```

Part 2

<img width="720" height="1600" alt="WhatsApp Image 2026-07-25 at 11 36 20 AM (1)" src="https://github.com/user-attachments/assets/bc487cb2-cfe4-47bd-8c9c-2dac412c4f77" />

```
QzB1bnRfaDRzX2Iz
```

Part 3

<img width="720" height="1600" alt="WhatsApp Image 2026-07-25 at 11 36 19 AM" src="https://github.com/user-attachments/assets/5b8329f5-5271-4fdb-b059-c8d0d7c70bcb" />

```
M25fZjB1bmQhfQ==
```

---

### Step 4 – Reconstruct the Message

Combined:

```
VEhNe1YzckBzX2FDQzB1bnRfaDRzX2IzM25fZjB1bmQhfQ==
```

Decoded using Base64.

---

## Tools Used

- Google Search
- Instagram
- Base64 Decoder

---

## Key Takeaways

- Follow every clue carefully.
- Social media can reveal hidden information.
- Multiple small clues often combine into the final solution.
- Base64 is a common encoding used in beginner OSINT challenges.

---
