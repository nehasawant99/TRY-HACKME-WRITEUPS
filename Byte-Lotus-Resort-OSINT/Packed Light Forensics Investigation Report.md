# Packed Light - Forensics Writeup

**Room:** https://tryhackme.com/room/hh-packedlight-02e5330c

<img width="1446" height="375" alt="image" src="https://github.com/user-attachments/assets/bcd82efd-f179-4b62-93f9-2441125ee84c" />

## Objective
Analyse a network capture (pcap) from the guest network, find the covert channel hiding data inside normal-looking traffic, reassemble the leaked bytes, decode them, and recover the flag.

---

## Skills Practised
- Network Forensics
- PCAP Analysis (Wireshark / tshark)
- Data Exfiltration Detection
- Base64 Decoding
- XOR Decryption

---

## Investigation Steps

### Step 1 – Load the Capture
Opened the provided `traffic.pcapng` file and checked the protocol breakdown. Traffic included ARP, TCP, HTTP, TLS, DNS, SSDP and QUIC.

### Step 2 – Spot the Suspicious Beacon
Filtered for HTTP requests and noticed one host repeatedly calling:

```
GET / HTTP/1.1
Host: byte-lotus-hotel.thm:8080
User-Agent: ...ByteLotusClient/1.1
```

This request repeated roughly **every second**, dozens of times, with a fake-looking User-Agent — matching the room's hint about a device "pinging some random :8080 address every second like clockwork."

### Step 3 – Inspect the Headers
Looked at the full headers of one request and found:

```
Cookie: hotel_sess_state=HA==
```

A tiny Base64-looking cookie value is not normal for a real session cookie — this stood out as the hidden channel.

### Step 4 – Extract All Cookie Values
Pulled the `hotel_sess_state` cookie from every repeated request. Each request had a **different** value — 30 in total:

```
HA==, AA==, BQ==, Mw==, Hg==, ew==, Og==, fA==, Fw==, eQ== ...
```

### Step 5 – Decode Each Cookie
Base64-decoded every cookie value. Each one decoded to exactly **one raw byte** — meaning the attacker was leaking the secret data **1 byte per request**.

### Step 6 – Reassemble the Bytes
Combined all 30 decoded bytes in order into a single blob:

```
1c 00 05 33 1e 7b 3a 7c 17 79 3b 17 3f 7c 3c 2b 20 79 26 2f 17 78 3e 2d 1a 17 31 78 3d 35
```

### Step 7 – Crack the XOR Key
The bytes were XOR-"encrypted" with a single repeating key. Since flags always start with `THM{`, XORed the first known bytes against `T`, `H`, `M`, `{` to solve for the key:

```
key = 0x48
```

### Step 8 – Decrypt the Full Message
XORed the entire byte blob with `0x48`:

```
THM{...}
```

---

## Tools Used
- tshark / Wireshark
- Python (`base64`, XOR scripting)

---

## Key Takeaways
- Repeated, regular-interval requests to odd ports are a red flag for beaconing malware.
- Cookies and other headers can be abused to smuggle data out a little at a time.
- Base64 is often just the first layer — always check if the decoded output still looks encrypted.
- Known-plaintext (like `THM{`) is a fast way to crack simple single-byte XOR encryption.
