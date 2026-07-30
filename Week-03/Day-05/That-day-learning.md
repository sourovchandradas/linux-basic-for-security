# 🐧 Day 21 (Week 03 • Day 07): Bash Scripting (Part 01)

Welcome to Day 07 of Week 03 of my Linux Security learning journey. This document details foundational Bash scripting principles, interactive shell concepts, Shebang interpreter directives, execution permission structures (`chmod`), and capturing dynamic user input via variables.

---

## 🎯 Key Points & Core Concepts

### 1. ⚙️ Introduction to Bash Scripting & Hacker Requirements

* Description: Bash scripting involves writing a sequence of executable shell commands inside a single text file to automate complex administrative workflows line-by-line.
* Security & Automation Goals:
* Eliminates repetitive manual CLI tasks.
* Automates multi-tool execution chains for reconnaissance and scanning.


* Auxiliary Scripting Languages in Cybersecurity:
* **Python:** Industry-standard language for contemporary exploit development and security tooling.
* **Ruby:** Core language of the Metasploit Framework module development.
* **Perl:** Preferred language for advanced regex pattern matching and text parsing.



---

### 2. 🐚 A Crash Course in Shell Basics

* What is a Shell?: An interactive command interpreter between the user and the Linux kernel that executes binaries without graphical user interface (GUI) overhead.
* Popular Linux Shell Types:
* `ksh` (Korn Shell)
* `zsh` (Z Shell)
* `csh` (C Shell)
* `bash` (Bourne-Again Shell): The default and most widely adopted shell across distributions.


* Editor Requirements: Scripts must be written using plain-text editors (e.g., `nano`, `vim`, `gedit`, `leafpad`) to avoid hidden rich-text formatting corruption.

---

### 3. 📜 Writing Your First Script (`HelloHackersArise`)

* The Shebang Line (`#!/bin/bash`): Placed at the very first line of a script file to inform the kernel which binary interpreter must execute the script.
* Script Comments (`#`): Lines beginning with `#` (except the Shebang) are ignored by the interpreter and serve as inline documentation.

Example — Simple output script (`HelloHackersArise`):

```bash
#!/bin/bash
# This is my first bash script. Wish me luck.
echo "Hello, Hackers-Arise!"

```

---

### 4. 🔐 Setting File Execution Permissions

* Default Rights: Newly created files default to `-rw-r--r--` (`644`), lacking execution (`x`) rights even for the creator.
* Enabling Execution: Using `chmod +x` or `chmod 755` adds execute permissions. In Linux color-coded terminals, executable files are highlighted in **green text**.

Example — Granting execution permissions and verifying file modes:

```bash
kali > chmod 755 HelloHackersArise
kali > ls -l HelloHackersArise
-rwxr-xr-x 1 root root 90 Oct 22 14:32 HelloHackersArise

```

#### 🖼️ Terminal Output

---

### 5. 🚀 Executing Scripts safely (`./`)

* The `./` Operator: Standard security policies omit the current directory (`.`) from the system `$PATH`. Explicitly calling `./script_name` targets execution strictly within the present working directory, preventing accidental invocation of system-wide binaries with matching names.

Example — Executing the localized script:

```bash
kali > ./HelloHackersArise

```

#### 🖼️ Terminal Output

---

### 6. 🔀 Adding Functionality with Variables & User Input

* Variables: Named memory storage locations that hold strings or numeric values.
* Variable Rules:
* **Assignment:** Assign values without the `$` symbol (e.g., `name="User"`).
* **Dereferencing / Access:** Access stored values using the `$` prefix (e.g., `echo $name`).


* Capturing Input (`read`): The `read` command halts execution, intercepts standard keyboard input, and binds the entered data into a specified variable.

Example — Interactive script implementation (`WelcomeScript.sh`):

```bash
#!/bin/bash
# Interactive user prompt script

echo "What is your name?"
read name

echo "What chapter are you on in Linux Basics for Hackers?"
read chapter

echo "Welcome $name to Chapter $chapter of Linux Basics for Hackers!"

```

Example — Granting execution rights and running the interactive script:

```bash
kali > chmod +x WelcomeScript.sh
kali > ./WelcomeScript.sh
What is your name?
OccupytheWeb
What chapter are you on in Linux Basics for Hackers?
8
Welcome OccupytheWeb to Chapter 8 of Linux Basics for Hackers!

```

#### 🖼️ Terminal Output

---

## 🛠️ Utilities & Tool Reference

| Category | Component/Tool | Syntax / Structure | Description |
| --- | --- | --- | --- |
| **Interpreter Header** | Shebang | `#!/bin/bash` | Instructs the kernel to invoke the Bash shell interpreter. |
| **Console Output** | `echo` | `echo "[text]"` | Prints text strings or expanded variables to standard output. |
| **User Input** | `read` | `read [VAR_NAME]` | Captures user terminal input and binds it to a variable name. |
| **Permissions** | `chmod` | `chmod 755 [file]` | Modifies file permission bits to allow script execution. |
| **Local Invocation** | `./` | `./[script.sh]` | Executes a file located explicitly in the current working directory. |

---

## 🔑 Key Takeaways for Revision

1. **Shebang Mandatory First Line:** Always start scripts with `#!/bin/bash` to ensure consistent interpreter selection across systems.
2. **Permission Boundary:** Executing a script without `chmod +x` or `chmod 755` results in a `Permission denied` error.
3. **Variable Dereferencing Rule:**

$$\text{Assigning Variable: } \texttt{name="Target"} \longrightarrow \text{No \$ prefix used}$$


$$\text{Reading Variable: } \texttt{echo \$name} \longrightarrow \text{Requires \$ prefix for expansion}$$


4. **Execution Safety:** Use `./filename.sh` to safely execute local scripts without altering system `$PATH` variables.

---
