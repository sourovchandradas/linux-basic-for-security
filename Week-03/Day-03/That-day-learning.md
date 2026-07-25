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
#### 🖼️ Terminal Command

![view environment](Screenshot/env-command.png)

#### 🖼️ Terminal Output

![view environment](Screenshot/env-output-01.png)
![view environment](Screenshot/env-output-02.png)


### 3. 🔍 Viewing All System & Shell Variables (set)
 * Description: The set command displays all active variables, including environment variables, local shell variables, custom functions, and command aliases.
 * Managing Output Stream: Because set dumps a massive output buffer, pipe its execution into more or grep for structured filtering.
Example — Piping full variable inventories through the more pager:
```bash
set | more

```
#### 🖼️ Terminal Command

![set | more command](Screenshot/set-more-command.png)

#### 🖼️ Terminal Output

![set | more output](Screenshot/set-more-output.png)

Example — Filtering specific targets using grep:
```bash
set | grep HISTSIZE

```
#### 🖼️ Terminal Command

![set | grep command](Screenshot/set-grep-command.png)

#### 🖼️ Terminal Output

![set | grep output](Screenshot/set-grep-output.png)

### 4. 🧠 Understanding Core Environment Variables
 * HISTSIZE: Controls the maximum number of recent commands recorded in the shell buffer (default is 1000). Setting HISTSIZE=0 disables command recording for anti-forensics and stealth operations.
 * PATH: The binary execution search index. When typing a command (like ls), the system scans directories listed in $PATH sequentially from left to right.
 * PS1: Defines the primary terminal command prompt string. Supports escape tokens (e.g., \u for user, \h for hostname, \w for present working directory).
Example — Inspecting prompt formatting string variables:
```bash
echo $PS1

```
#### 🖼️ Terminal Command
![echo PS1 command](Screenshot/PS1-command.png)

#### 🖼️ Terminal Output
![echo PS1 output](Screenshot/PS1-output.png)

### 5. 🛠️ Modifying Variables in Current Session
 * Description: Variables can be updated or overwritten directly in the shell using standard assignment statements (NAME=value).
 * Session Boundary Warning: Direct assignments affect **only** the active terminal session. Closing the terminal or launching a new subshell resets modified values back to system defaults.
Example — Disabling command history recording for stealth:
```bash
HISTSIZE=0

```

#### 🖼️ Terminal Command
![HISTSIZE=0 command](Screenshot/modify-histsize-command.png)

#### 🖼️ Terminal Output
![HISTSIZE=0 output](Screenshot/modify-histsize-output.png)

Example — Customizing the active prompt string:
```bash
PS1="[HACKED] $ "

```
#### 🖼️ Terminal Command

![PS1="LinuxSkills $ command](Screenshot/customize-active-promt-command.png)

#### 🖼️ Terminal Output

![PS1="LinuxSkills $ output-01](Screenshot/customize-active-promt-output-01.png)
![PS1="LinuxSkills $ output-02](Screenshot/customize-active-promt-output-02.png)
![PS1="LinuxSkills $ output-03](Screenshot/customize-active-promt-output-03.png)
![PS1="LinuxSkills $ output-04](Screenshot/customize-active-promt-output-04.png)
![PS1="LinuxSkills $ output-05](Screenshot/customize-active-promt-output-05.png)
![PS1="LinuxSkills $ output-06](Screenshot/customize-active-promt-output-06.png)
![PS1="LinuxSkills $ output-07](Screenshot/customize-active-promt-output-07.png)
![PS1="LinuxSkills $ output-08](Screenshot/customize-active-promt-output-08.png)
![PS1="LinuxSkills $ output-09](Screenshot/customize-active-promt-output-09.png)
![PS1="LinuxSkills $ output-10](Screenshot/customize-active-promt-output-10.png)
![PS1="LinuxSkills $ output-11](Screenshot/customize-active-promt-output-11.png)
![PS1="LinuxSkills $ output-12](Screenshot/customize-active-promt-output-12.png)
![PS1="LinuxSkills $ output-13](Screenshot/customize-active-promt-output-13.png)


Example — Appending custom script directories to $PATH without overwriting existing entries:
```bash
PATH:/home/user/scripts

```
#### 🖼️ Terminal Output
### 6. 📤 Exporting & Persisting Variable Changes (export)
 * Description: Assigning a variable only updates the active local shell context. The export command promotes a variable into an environment variable, passing its state down to all newly initialized child subshells.
 * Permanent Persistence: To ensure variables persist across reboots or terminal restarts, add export statements into shell initialization files such as ~/.bashrc or ~/.profile, then apply them using source ~/.bashrc.

Example — Exporting a variable state across child execution layers:
```bash
HISTSIZE=0
export HISTSIZE

```
#### 🖼️ Terminal Command

![export HISTSIZE command](Screenshot/export-histsize-command.png)

#### 🖼️ Terminal Output

![export HISTSIZE output](Screenshot/export-histsize-output.png)

Example — Backing up current environment variables before making changes:
```bash
set > ~valueofALL.txt

```
#### 🖼️ Terminal Command

![Backing up current environment variables before making change command](backing-up-current-environment-variables-before-making-changes-command.png)

![cat command](backing-up-current-environment-variables-before-making-changes-display-with-cat-command.png)

#### 🖼️ Terminal Output

![cat output](backing-up-current-environment-variables-before-making-changes-display-with-cat-output.png)

### 7. 🖨️ Displaying Variable Values (echo $)
 * Description: To evaluate or expand the value stored inside an environment variable, prepend the variable name with the variable expansion operator ($).

Example — Querying specific variable values:
```bash
echo $HOME

```
#### 🖼️ Terminal Command

![echo $HOME](querying-specific-variable-values-command.png)

#### 🖼️ Terminal Output

![echo $HOME](querying-specific-variable-values-command.png)

Example — Printing multiple expanded variables within a string:
```bash
echo "User: $USER, Home: $HOME, Shell: $SHELL"

```
#### 🖼️ Terminal Command

![echo "User: $USER, Home: $HOME, Shell: $SHELL" command](Screenshot/printing-multiple-expanded-variables-within-a-string-command.png)

#### 🖼️ Terminal Command

![echo "User: $USER, Home: $HOME, Shell: $SHELL" output](Screenshot/printing-multiple-expanded-variables-within-a-string-output.png)

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
