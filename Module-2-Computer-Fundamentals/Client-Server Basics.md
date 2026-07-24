# Client-Server Basics


<img width="1461" height="397" alt="Screenshot 2026-07-24 at 8 26 00 PM" src="https://github.com/user-attachments/assets/36ea70cf-345f-41e6-8ae1-01329eb03006" />


## Objective
Understand how clients and servers communicate over a network and how websites and online services work.

---

# What is a Client?

A **client** is a device or application that requests information or services from another computer.

### Examples

* Web Browser (Chrome, Firefox)
* Mobile App
* Email Client
* Game Client

When you open a website, your browser acts as the client.

---

# What is a Server?

A **server** is a computer that receives requests from clients, processes them, and sends back the requested data.

Servers are designed to run continuously and serve multiple users at the same time.

### Common Types of Servers

* Web Server
* Database Server
* File Server
* Mail Server
* DNS Server

---

# How Client-Server Communication Works

1. The client sends a request.
2. The request travels through the network.
3. The server receives and processes the request.
4. The server sends a response.
5. The client displays the result.

Example:

```text
Browser
   │
HTTP Request
   │
Web Server
   │
Processes Request
   │
HTTP Response
   │
Browser Displays Website
```

---

# Request and Response

### Request

Sent by the client.

Examples:

* Open a webpage
* Download a file
* Log in to an account

### Response

Sent by the server.

Examples:

* HTML page
* Image
* Video
* Error message
* Login success

---

# IP Address

Every device connected to a network has an IP address.

It allows devices to identify and communicate with each other.

Example:

```
192.168.1.10
```

---

# Domain Name

A domain name is the human-readable name of a website.

Instead of remembering an IP address, we use a domain name.

Example:

```
example.com
```

DNS translates the domain name into an IP address.

---

# Ports

A port identifies which service on a server should receive the request.

---

# Common Protocols

* HTTP – Transfers web pages
* HTTPS – Secure version of HTTP


---

# Real-World Example

When you visit a website:

* Your browser sends an HTTP/HTTPS request.
* DNS finds the website's IP address.
* The server processes the request.
* The server sends the webpage back.
* The browser displays it.

---

# Why This Matters in Cybersecurity

Understanding client-server communication helps security professionals:

* Analyse network traffic.
* Detect suspicious requests.
* Investigate attacks.
* Secure web applications.
* Understand how hackers target servers.

---

# Key Terms

* Client
* Server
* Request
* Response
* IP Address
* Domain Name
* DNS
* Port
* Protocol

---


## Interactive Hands-on Labs
Performed hands-on labs to see how client requests and server responses work behind the scenes, giving a practical understanding of client-server communication.


<img width="1461" height="800" alt="Screenshot 2026-07-24 at 8 07 47 PM" src="https://github.com/user-attachments/assets/4a62d618-c7f7-47b9-89e1-801603923214" />


<img width="1461" height="800" alt="Screenshot 2026-07-24 at 8 19 36 PM" src="https://github.com/user-attachments/assets/d8ecdfb9-f17a-4132-815e-8773c137eb5c" />


---


# What I Learned

* Clients request services, while servers provide them.
* Every website follows the request-response model.
* DNS converts domain names into IP addresses.
* Ports help identify different network services.
* Understanding client-server communication is essential for networking and cybersecurity.

---

# Reflection

In this lab, I understood how a client and server communicate to make websites and online services work. I learned how requests and responses travel through a network, the purpose of IP addresses, domain names, ports, and common protocols. This gave me a better understanding of how systems communicate and why these basics are important before learning networking and cybersecurity.
