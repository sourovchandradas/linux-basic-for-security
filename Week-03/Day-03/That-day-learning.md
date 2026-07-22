# 🐧 Day 17 : Managing User Environment Variables (Part 01)
Welcome to Day 17 of my Linux Security learning journey. This document details the architectural concepts of Linux environment variables, analyzing system versus shell scope, inspecting variables with env and set, customizing active environment attributes, and implementing session-wide updates using export.
## 🎯 Key Points & Core Concepts
### 1. 🌐 Introduction to Environment Variables
 * Description: Environment variables are system-wide, process-inherited variables embedded within the Linux operating system. They govern workspace behavior, visual themes, application defaults, and command search behaviors.
 * Scope & Inheritance: Unlike shell variables (which exist only inside the defining shell and use lowercase letters), environment variables use **UPPERCASE** notation and are automatically inherited by any child subshells or child processes spawned by the parent shell.
 * Value Syntax Formatting:
   * Single value: KEY=value
   * Multiple values: KEY=value1:value2:value3 (separated by colons)
   * Values containing spaces: KEY="value with spaces" (enclosed in quotation marks)
### 2. 👁️ Viewing Environment Variables (env)
 * Description: The env command queries and displays default system-level environment variables initialized during system startup.
 * Key Default Variables:
   * HOME: Absolute path to the active user's home directory (e.g., /root).
   * PATH: Colon-separated list of directories searched when executing terminal commands.
   * SHELL: Path to the active shell binary (e.g., /bin/bash).
   * USER: Identity of the logged-in user account.
   * TERM: Active terminal emulator emulation type (e.g., xterm-256color).
   * PWD: Present working directory path.
Example — Displaying default environment variables:
```bash
env

```
#### 🖼️ Terminal Output
### 3. 🔍 Viewing All System & Shell Variables (set)
 * Description: The set command displays all active variables, including environment variables, local shell variables, custom functions, and command aliases.
 * Managing Output Stream: Because set dumps a massive output buffer, pipe its execution into more or grep for structured filtering.
Example — Piping full variable inventories through the more pager:
```bash
kali > set | more

```
#### 🖼️ Terminal Output
Example — Filtering specific targets using grep:
```bash
kali > set | grep HISTSIZE

```
#### 🖼️ Terminal Output
### 4. 🧠 Understanding Core Environment Variables
 * HISTSIZE: Controls the maximum number of recent commands recorded in the shell buffer (default is 1000). Setting HISTSIZE=0 disables command recording for anti-forensics and stealth operations.
 * PATH: The binary execution search index. When typing a command (like ls), the system scans directories listed in $PATH sequentially from left to right.
 * PS1: Defines the primary terminal command prompt string. Supports escape tokens (e.g., \u for user, \h for hostname, \w for present working directory).
Example — Inspecting prompt formatting string variables:
```bash
kali > echo $PS1

```
#### 🖼️ Terminal Output
### 5. 🛠️ Modifying Variables in Current Session
 * Description: Variables can be updated or overwritten directly in the shell using standard assignment statements (NAME=value).
 * Session Boundary Warning: Direct assignments affect **only** the active terminal session. Closing the terminal or launching a new subshell resets modified values back to system defaults.
Example — Disabling command history recording for stealth:
```bash
kali > HISTSIZE=0

```
#### 🖼️ Terminal Output
Example — Customizing the active prompt string:
```bash
kali > PS1="[HACKED] $ "

```
#### 🖼️ Terminal Output
Example — Appending custom script directories to $PATH without overwriting existing entries:
```bash
kali > PATH=$PATH:/home/user/scripts

```
#### 🖼️ Terminal Output
### 6. 📤 Exporting & Persisting Variable Changes (export)
 * Description: Assigning a variable only updates the active local shell context. The export command promotes a variable into an environment variable, passing its state down to all newly initialized child subshells.
 * Permanent Persistence: To ensure variables persist across reboots or terminal restarts, add export statements into shell initialization files such as ~/.bashrc or ~/.profile, then apply them using source ~/.bashrc.
Example — Exporting a variable state across child execution layers:
```bash
kali > HISTSIZE=0
kali > export HISTSIZE

```
#### 🖼️ Terminal Output
Example — Backing up current environment variables before making changes:
```bash
kali > set > ~/valueofALL.txt

```
#### 🖼️ Terminal Output
### 7. 🖨️ Displaying Variable Values (echo $)
 * Description: To evaluate or expand the value stored inside an environment variable, prepend the variable name with the variable expansion operator ($).
Example — Querying specific variable values:
```bash
kali > echo $HOME

```
#### 🖼️ Terminal Output
Example — Printing multiple expanded variables within a string:
```bash
kali > echo "User: $USER, Home: $HOME, Shell: $SHELL"

```
#### 🖼️ Terminal Output
## 🛠️ Utilities & Tool Reference
| Category | Component/Tool | Syntax / Structure | Description |
|---|---|---|---|
| **Env Audit** | env | env | Lists all active environment variables inherited by sub-processes. |
| **All Variable Audit** | set | set or set | grep [NAME] | Displays all environment, shell, alias, and local function definitions. |
| **Export Scope** | export | export [VARIABLE]=value | Promotes local shell variables to environment scope for child inheritance. |
| **Value Inspection** | echo | echo $[VARIABLE] | Expands and prints the value stored inside a target variable name. |
## 🔑 Key Takeaways for Revision
### Variable Evaluation & Expansion Syntax
Always use the $ expansion prefix when reading variable values:
 1. **Naming Conventions:** Environment variables use **UPPERCASE** characters (PATH, HOME), while local shell variables typically use lowercase characters.
 2. **Safe PATH Appending:** Always preserve existing search directories when modifying $PATH by appending new locations (PATH=$PATH:/new/path). Replacing $PATH outright will break standard command lookups.
 3. **Scope Propagation Rules:**
