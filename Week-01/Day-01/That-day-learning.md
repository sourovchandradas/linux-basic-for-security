# 🐧 Day 01: Linux Basics & Essential Terms for Hackers

Welcome to Day 01 of my Linux Security learning journey. This document serves as a professional summary of the foundational concepts required to understand and operate in a Linux environment efficiently.

---

## 🎯 Key Points & Core Concepts

### 1. 🔤 Case Sensitivity
Unlike Windows, the Linux filesystem is strictly **case-sensitive**. 
* `Desktop`, `desktop`, and `DeskTop` are treated as three completely different entities.
* *Troubleshooting Tip:* If you receive a `"file or directory not found"` error while you are sure it exists, always double-check the character casing.

### 2. 👑 The `root` Account
* **Definition:** The `root` account is the absolute administrator or superuser in Linux.
* **Privileges:** It has unrestricted power to reconfigure the system, add users, modify system files, and change network settings.
* **Hacker's Perspective:** As a pentester or hacker, many security tools require `root` privileges to interact directly with hardware or network sockets.

### 3. 📂 Directory Structure & Home
* In Linux, folders are referred to as **directories** and are organized hierarchically.
* Each user is allocated a personal directory located under `/home` (e.g., `/home/username`). By default, newly created personal files are saved here.

### 4. ⚙️ Binaries & Executables
* **Binaries** are files containing compiled code that can be executed as programs (similar to `.exe` files in Windows).
* They typically reside in directories like `/usr/bin` or `/usr/sbin`.

### 5. 💻 Shell vs. Terminal
* **Terminal:** The Command Line Interface (CLI) application where users type commands.
* **Shell:** The environment and command interpreter that runs behind the terminal. It translates user commands into instructions the Operating System understands. The most common shell is **bash** (*Bourne-again shell*).

### 6. 📜 Scripts
* A script is a sequential file containing multiple commands executed line-by-line in an interpretive environment. 
* **Python** is currently the most preferred scripting language among cybersecurity professionals and hackers.

### 7. 🐉 Kali Linux
* A specialized Linux distribution built entirely for digital forensics, penetration testing, and security auditing. It comes pre-packaged with hundreds of specialized security utilities.

---

## 🛠️ Utilities & Tool Reference

| Category | Component/Tool | Description |
| :--- | :--- | :--- |
| **Common Binaries** | `ps`, `cat`, `ls`, `ifconfig` | Standard system utilities for process viewing, reading files, listing directories, and configuring network interfaces. |
| **Security Tools** | `aircrack-ng`, `Snort` | Specialized tools used for wireless hacking and network Intrusion Detection (IDS) respectively. |
| **Default Shell** | `bash` | The standard text-based interface environment used across most Linux configurations. |

### 📝 Example of Case Sensitivity:
```bash
# Creating two different files due to case sensitivity
touch Secret.txt
touch secret.txt

# Attempting to access the file with wrong case will return:
cat SECRET.txt
# Output: cat: SECRET.txt: No such file or directory
