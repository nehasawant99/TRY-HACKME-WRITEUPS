# Linux CLI Basics

<img width="1459" height="357" alt="Screenshot 2026-09-10 at 3 25 27 PM" src="https://github.com/user-attachments/assets/7eb5da58-8078-4c68-94a2-be87935fd28d" />

## Objective

Learn the basics of using the Linux command line and understand how to navigate directories, find files, and read file contents using common CLI commands.

## What I Learned

* Learned how to work with the Linux terminal.
* Understood how directories and files are organised in Linux.
* Learned how to navigate between directories using `cd`.
* Practised listing files and folders using `ls`.
* Learned how to locate files using `find`.
* Used `cat` to read the contents of text files.
* Understood the difference between absolute and relative paths.
* Got familiar with basic Linux commands used for everyday system navigation.

---

## Hands-on Lab

In the lab, I practised navigating the Linux filesystem using the command line. I used commands such as `ls` and `cd` to move around directories and `find` to locate a specific file.

For the mini challenge, I searched for `day1_report.txt` inside my home directory, navigated to the directory where it was located, and used `cat` to read the file contents.

<img width="1470" height="795" alt="Screenshot 2026-09-09 at 7 38 41 PM" src="https://github.com/user-attachments/assets/e9380c78-b1c1-40d4-81cb-ebb059a6fe7d" />


Example commands used:

```bash
ls
cd /home/ubuntu
find /home/ubuntu -name "day1_report.txt"
cd /home/ubuntu/.logs/archive
cat day1_report.txt
```

---

## Reflection

This lab helped me become more comfortable with the Linux terminal and basic filesystem navigation. Commands like `find`, `cd`, and `cat` are simple but useful for working with Linux systems and are also important when investigating files and systems in cybersecurity.
