# Byte Lotus Resort - OSINT Writeup

## Objective

Investigate the provided promotional brochure using basic OSINT techniques, follow the hidden clues, locate the concealed social media account, and recover the final flag.

---

## Skills Practiced

- OSINT Investigation
- Social Media Reconnaissance
- Base64 Decoding
- Clue Analysis
- Information Gathering

---

## Investigation Steps

### Step 1 – Analyze the Brochure

Initial clues identified:

- Byte Lotus Resorts
- Concierge: Vera
- "Find us on Instagram or not."

These hints suggested searching for the resort on Instagram.

---

### Step 2 – Locate Instagram Account

Found:

```
@thebytelotusresort
```

The brochure also referenced **Vera**, leading to another account.

Found:

```
@veratheconcierge
```

---

### Step 3 – Inspect Vera's Profile

The profile contained three posts.

Each post contained a fragment of a Base64 encoded string.

Part 1

```
VEhNe1YzckBzX2FD
```

Part 2

```
QzB1bnRfaDRzX2Iz
```

Part 3

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

## Flag

```
THM{V3r@s_aCC0unt_h4s_b33n_f0und!}
```

---

## Tools Used

- Google Search
- Instagram
- CyberChef / Base64 Decoder

---

## Key Takeaways

- Follow every clue carefully.
- Social media can reveal hidden information.
- Multiple small clues often combine into the final solution.
- Base64 is a common encoding used in beginner OSINT challenges.

---
