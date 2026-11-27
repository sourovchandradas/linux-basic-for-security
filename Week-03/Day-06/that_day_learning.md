# 🐧 Day 20 : Bash Scripting (Part 02)

Welcome to Day 20 of my Linux Security learning journey. This document details automated network reconnaissance using Nmap inside Bash scripts, real-world port exploitation case studies, building static and interactive dynamic scanners, and reference commands for built-in Bash operations.

---

## 🎯 Key Points & Core Concepts

### 1. 🌐 Introduction to Port Scanning & Nmap Basics

* Description: Port scanning is the process of probing target network interfaces to identify open TCP/UDP ports and active services.
* Network Mapper (Nmap): The standard network scanner pre-installed on Kali Linux for host discovery, port auditing, and service footprinting.
* Fundamental Syntax:
* `-sT`: Directs Nmap to perform a full TCP Connect Scan via the standard 3-way handshake (`SYN` $\rightarrow$ `SYN-ACK` $\rightarrow$ `ACK`).
* `-p`: Specifies the target port number (e.g., `-p 53` for DNS, `-p 3306` for MySQL, or `-p 5505` for Aloha POS).



Example — Executing a TCP connect scan against a target IP address:

```bash
nmap -sT 192.168.0.108

```
#### 🖼️ Terminal Commend
![nmap with IP address command](Screenshot/nmap-sT-ip-address-command.png)

#### 🖼️ Terminal Output
![nmap with IP address output](Screenshot/nmap-sT-ip-address-outptut.png)

Example — Executing a TCP connect scan against a target port:

```bash
nmap -sT 192.168.0.108 -p 53

```
#### 🖼️ Terminal Commend
![nmap with IP address & port command](Screenshot/nmap-sT-ip-address-p-port-command.png)

#### 🖼️ Terminal Output
![nmap with IP address & port output](Screenshot/nmap-sT-ip-address-p-port-outptut.png)

---

### 2. 🚨 Case Study: The Max Butler Aloha POS Hack

* Background: Security research identified an unauthenticated technical support backdoor listening on Port 5505 within Aloha Point of Sale (POS) restaurant systems.
* Attack Execution: Hacker Max Butler (Max Vision) scripted automated scanners targeting millions of public IP addresses specifically searching for open Port 5505, allowing unauthorized root-level system takeovers.
* Takeaway: Combining automated shell scripts with network scanners amplifies reconnaissance capabilities, allowing rapid evaluation across whole subnet blocks.

---

### 3. 📜 Static LAN Scanner (`dnsServer.sh`)

* Purpose: Automated scanning of an entire local subnet (e.g., `10.0.2.0/24`) for default active DNS services running on Port 53.
* Operational Flags & Syntax:
* `>/dev/null`: Discards standard output streams to maintain a clean terminal view.
* `-oG`: Generates "Grepable" Nmap output structured for single-line text parsing via `grep`.



Example — Script implementation (`dnsServer.sh`):
```bash
mousepad dnsServer.sh

```
#### 🖼️ Terminal Command
![mousepad dnsServer](Screenshot/mousepad-dnsServer.sh-command.png)
#### 🖼️ Terminal Ouput
![mousepad interface](Screenshot/mousepad-interface.png)

```bash
#!/bin/bash

```
#### 🖼️ Mousepad Script

![write shebang command](Screenshot/write-shebang-command.png)

```bash
# This script is designed to find hosts with dns installed

```
#### 🖼️ Mousepad Script

![comment command](Screenshot/write-comment-command.png)

```bash
nmap -sT 10.0.2.0/24 -p 53 >/dev/null -oG dnsServer

```
#### 🖼️ Mousepad Script

![namap command](Screenshot/nmap-command.png)

```bash
cat dnsServer | grep open > dnsServer2

```
#### 🖼️ Mousepad Script

![cat & grep](Screenshot/cat-and-grep-open.png)

```bash
cat dnsServer2

```
#### 🖼️ Mousepad Script

![Final Command](Screenshot/final-cat-command.png)

#### 🖼️ Mousepad Script(Save Script)

![Save Script](Screenshot/script-save-method.png)

#### 🖼️ Mousepad Script(After Saving)

![After save](Screenshot/mousepad-interface-after-save.png)

#### 🖼️ Mousepad Script(After Quite mousepad)

![Quit mousepad](Screenshot/after-quite-mousepad.png)

Example — Granting execution rights and running the static scanner:

```bash
chmod 755 dnsServer.sh

```
#### 🖼️ Terminal Command

![Change permissions command](Screenshot/change-permissions-command.png)

#### 🖼️ Terminal Output

![Change permissions output](Screenshot/change-permissions-output.png)

Example — Script running command:

```bash
./dnsServer.sh

```
#### 🖼️ Terminal Command

![Script run command](Screenshot/script-run-command.png)

#### 🖼️ Terminal Output

![Script output](Screenshot/script-output.png)

---

### 4. 🎛️ Building a Dynamic Interactive Port Scanner (`dnsScript`)

* Purpose: Hardcoded subnets and ports restrict script usability. Prompting for interactive inputs using `read` turns a fixed script into a flexible, multi-target scanner.
* Range Expansion: Syntax like `$FirstIP-$LastOctetIP` expands dynamically (e.g., `10.0.2.1-254`) for flexible scanning.

Example — Script implementation (`dnsScript`):
```bash
mousepad dnsScript

```
#### 🖼️ Terminal Command
![mousepad command](Screesnshot/mousepad-command-for-dnsscript.png)

#### 🖼️ Mousepad Terminal
![Mousepad Interface](Screenshot/mousepad-interface.png)

```bash
#!/bin/bash

```
#### 🖼️ Mousepad Script
![Scripting Command-01](Screenshot/scripting-command-01.png)

```bash
echo "Enter the starting IP address : "

```
#### 🖼️ Mousepad Script
![Scripting Command-02](Screenshot/scripting-command-02.png)

```bash
read FirstIP

```
#### 🖼️ Mousepad Script
![Scripting Command-03](Screenshot/scripting-command-03.png)

```bash
echo "Enter the last octet of the last IP address : "

```
#### 🖼️ Mousepad Script
![Scripting Command-04](Screenshot/scripting-command-04.png)

```bash
read LastOctetIP

```
#### 🖼️ Mousepad Script
![Scripting Command-05](Screenshot/scripting-command-05.png)

```bash
echo "Enter the port number you want to scan for : "

```
#### 🖼️ Mousepad Script
![Scripting Command-06](Screenshot/scripting-command-06.png)

```bash
read port

```
#### 🖼️ Mousepad Script
![Scripting Command-07](Screenshot/scripting-command-07.png)

```bash
nmap -sT $FirstIP-$LastOctetIP -p $port >/dev/null -oG dnsScript

```
#### 🖼️ Mousepad Script
![Scripting Command-08](Screenshot/scripting-command-08.png)

```bash
cat dnsScript | grep open > dnsScript

```
#### 🖼️ Mousepad Script
![Scripting Command-09](Screenshot/scripting-command-09.png)

```bash
cat dnsScript

```
#### 🖼️ Mousepad Script
![Scripting Command-10](Screenshot/scripting-command-10.png)

#### 🖼️ Terminal Interface(After Quite)
![After quite mousepad](Screenshot/after-quite-mousepad.png)

Example — Interactively executing the dynamic port scanner:
```bash
chmod 755 dnsScript

```
#### 🖼️ Terminal Command
![Change Permissions Command](Screenshot/change-permissions-script-command.png)

#### 🖼️ Terminal Output
![Change Permissions Output](Screenshot/change-permissions-script-output.png)

```bash
./dnsScript

```
#### 🖼️ Terminal Command
![Script Command](Screenshot/script-command.png)

```bash
Enter the starting IP address :

```
#### 🖼️ Terminal Command
![User defind input-01](Screenshot/user-defind-input-01.png)

```bash 
10.0.2.0

```
#### 🖼️ Terminal Command
![User defind input-02](Screenshot/user-defind-input-02.png)

```bash
Enter the last octet of the last IP address : 

```
#### 🖼️ Terminal Command
![User defind input-03](Screenshot/user-defind-input-03.png)

```bash
254

```
#### 🖼️ Terminal Command
![User defind input-04](Screenshot/user-defind-input-04.png)

```bash
Enter the port number you want to scan for : 

```
#### 🖼️ Terminal Command
![User defind input-05](Screenshot/user-defind-input-05.png)

```bash
53

```
#### 🖼️ Terminal Command
![User defind input-06](Screenshot/user-defind-input-06.png)

#### 🖼️ Terminal Output
![Final output](Screenshot/script-final-output.png)

---

## 🛠️ Built-in Bash Scripting Reference

| Command / Symbol | Function & Purpose Description |
| --- | --- |
| `:` | Null command; returns a standard exit status of 0 (true). |
| `.` | Executes a shell script directly within the current environment context. |
| `bg` / `fg` | Shifts jobs between background (`bg`) and foreground (`fg`) states. |
| `break` | Terminates and exits an active `for`, `while`, or `until` loop structure. |
| `continue` | Skips the remainder of the current loop iteration and moves to the next. |
| `exec` | Replaces the active shell process with the called command without creating subshells. |
| `export` | Marks variables or functions to be passed down to child subshells. |
| `getopts` | Parses positional command-line flags and arguments inside scripts. |
| `read` | Captures input from standard input (`stdin`) into specified variables. |
| `readonly` | Locks variable values as immutable/read-only. |
| `shift` | Shifts positional parameters leftward, discarding `$1`. |
| `trap` | Intercepts system signals (e.g., `SIGINT`) and executes cleanup routines. |
| `type` | Identifies command classification (builtin, alias, binary file). |
| `unset` | Removes variable or function definitions from environment memory. |

---

## 🔑 Key Takeaways for Revision

1. **Output Management:** Using `>/dev/null` suppresses raw standard output, keeping the terminal clean during automated tasks.
2. **Parsing Efficiency:** Always output scans using `-oG` (Grepable format) when results need to be parsed with text-processing utilities like `grep`, `awk`, or `sed`.
3. **Interactive Scripting Pipeline:**

$$\text{User Input } (\texttt{read}) \longrightarrow \text{Variable Assignment} \longrightarrow \text{Dynamic Execution } (\texttt{nmap \$Range})$$


4. **Permissions Check:** Remember to grant execution privileges (`chmod 755 script.sh`) before trying to run `./script.sh`.

---

*⚡ End of Week 03 • Day 05 Notes • Organized for GitHub & OneNote*
