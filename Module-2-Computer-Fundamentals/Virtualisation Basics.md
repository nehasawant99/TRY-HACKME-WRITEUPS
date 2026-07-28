# Virtualization Basics


<img width="1470" height="296" alt="Screenshot 2026-07-28 at 7 51 10 PM" src="https://github.com/user-attachments/assets/a759e0c8-6f84-44f2-b21f-c2e0126ab7ab" />



## Objective

Learn what virtualization is, how virtual machines work, and why they are widely used in IT and cybersecurity.

---

# What is Virtualization?

Virtualisation is the process of creating a virtual machine inside another physical computer.

A virtual machine (VM) behaves like a real computer with its own operating system, storage, memory, and applications.

---

# What is a Virtual Machine (VM)?

A Virtual Machine is a software-based computer that runs inside a host computer.

Each VM is isolated from the host and other virtual machines, allowing multiple operating systems to run on a single physical machine.

---

# Host vs Guest

### Host

The physical computer that provides hardware resources.

Example:

* MacBook
* Windows PC
* Linux Computer

### Guest

The operating system running inside the virtual machine.

Example:

* Kali Linux
* Windows 11
* Ubuntu

---

# Hypervisor

A hypervisor is software that creates and manages virtual machines.

Its responsibilities include:

* Allocating CPU and RAM
* Managing storage
* Isolating virtual machines
* Starting and stopping VMs

Examples:

* VirtualBox
* VMware Workstation
* Hyper-V
* UTM

---

# Benefits of Virtualization

* Run multiple operating systems on one computer.
* Safely test software and configurations.
* Isolate environments from the host system.
* Save hardware costs.
* Quickly create or remove virtual machines.

---

# Virtualization in Cybersecurity

Virtual machines are commonly used for:

* Penetration testing
* Malware analysis
* Digital forensics
* Security labs
* Testing suspicious files
* Building isolated environments

Running these tasks inside a VM reduces the risk of affecting the host operating system.

---

# Real-World Example

A security analyst runs Kali Linux inside a virtual machine on their laptop to perform security testing without modifying the main operating system.

---

# Key Terminology

- Virtualization: Enables a single physical computer to act like multiple separate computers.
- Hypervisor: The “manager” software that makes and runs the virtual computers.
- Lab Machine (VM): A whole virtual computer inside the real one, with its own system.
- Container: A small, isolated box for one app that shares the same system as the host.
- Container Images: A pre-packed recipe/template used to create containers.
- Network Ports: Special numbered entry points that apps use to talk over the network.


---

# What I Learned

* A virtual machine is a software-based computer.
* The host provides hardware resources, while the guest is the operating system running inside the VM.
* A hypervisor manages virtual machines.
* Virtualization provides a safe environment for learning, testing, and cybersecurity activities.

---

## Interactive Hand-ons-Lab
Performed hands-on tasks to investigate a VM in an error state, restart the service, create a new virtual machine, and monitor physical host resources. This lab provided practical experience in basic virtualization management and troubleshooting.


<img width="1470" height="802" alt="Screenshot 2026-07-28 at 4 39 39 PM" src="https://github.com/user-attachments/assets/6f07c90e-0acb-48cb-893c-478223178588" />

<img width="1470" height="802" alt="Screenshot 2026-07-28 at 4 41 48 PM" src="https://github.com/user-attachments/assets/af912230-b9f5-406f-abed-1f2f693faedf" />

