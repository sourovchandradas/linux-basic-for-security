# 🐧 Day 14 : Modifying File and Directory Permissions

Welcome to Day 14 of my Linux Security learning journey. This document details advanced system administration and security workflows for modifying file and directory permissions via the `chmod` engine, exploring absolute numeric (octal) notation, symbolic representation (UGO syntax), and handling execution permissions on newly acquired binary payloads.

---

## 🎯 Key Points & Core Concepts

### 1. ⚙️ The Mechanics of `chmod` (Change Mode)

* Description: Access configurations in Linux are not static. The `chmod` utility allows authorized identities to redefine access controls.
* Authorization Constraints: Only the absolute system superuser (`root`) or the explicit user owner of a file possesses the architectural authorization required to change its permission bits.

---

### 2. 🔢 Absolute Modification via Decimal/Octal Notation

* Description: Under the operating system layer, permissions exist as binary ON (1) or OFF (0) bits. A single set of standard `rwx` switches can be mapped as three binary placeholders (e.g., full access `rwx` equates to `111`).
* Octal Encoding: To simplify data entry, Linux condenses these groups into single octal values running from `0` to `7`. Each position is assigned a mathematically calculated bitweight:
* `r` (Read bitweight) = **4**
* `w` (Write bitweight) = **2**
* `x` (Execute bitweight) = **1**



#### Permission Bitweight Matrix

| Permission Block | Binary Mapping | Calculated Sum | Octal Symbol |
| --- | --- | --- | --- |
| `---` | `000` | 0 + 0 + 0 | **0** |
| `--x` | `001` | 0 + 0 + 1 | **1** |
| `-w-` | `010` | 0 + 2 + 0 | **2** |
| `-wx` | `011` | 0 + 2 + 1 | **3** |
| `r--` | `100` | 4 + 0 + 0 | **4** |
| `r-x` | `101` | 4 + 0 + 1 | **5** |
| `rw-` | `110` | 4 + 2 + 0 | **6** |
| `rwx` | `111` | 4 + 2 + 1 | **7** |

Example — Applying a numeric permission configuration (`774` gives User=Full, Group=Full, Others=Read-Only):

```bash
kali > chmod 774 hashcat.hcstat

```

#### 🖼️ Terminal Output

---

Example — Checking the updated octal file state via structural lists:

```bash
kali > ls -l
total 32952
drwxr-xr-x 5 root root     4096 Dec 5 10:47 charsets
-rwxrwxr-- 1 root root 33685504 June 28 2018 hashcat.hcstat
-rw-r--r-- 1 root root 33685504 June 28 2018 hashcat.hctune

```

#### 🖼️ Terminal Output

---

### 3. 🔤 Relative Modification via Symbolic Notation (UGO Syntax)

* Description: For targets requiring minor relative modifications without overwriting other bits, the symbolic method provides an intuitive syntax. It isolates individual layers via **UGO** tags: `u` (User/Owner), `g` (Group), and `o` (Others).
* Operational Actions:
* `-` : Removes a targeted access permission.
* `+` : Appends a targeted access permission.
* `=` : Sets an absolute permission state, stripping unnamed values.



Example — Removing the write attribute exclusively from the owner layer:

```bash
kali > chmod u-w hashcat.hcstat

```

#### 🖼️ Terminal Output

---

Example — Verifying the resulting permission state (`-r-xr-xr--`):

```bash
kali > ls -l
total 32952
-r-xr-xr-- 1 root root 33685504 June 28 2018 hashcat.hcstat

```

#### 🖼️ Terminal Output

---

Example — Adjusting multiple target categories simultaneously using commas:

```bash
kali > chmod u+x,o+x hashcat.hcstat

```

#### 🖼️ Terminal Output

---

### 4. 🪓 Provisioning Execution Permissions on New Toolsets

* Description: When downloading custom utilities or scripts directly from external developer environments, the Linux filesystem assigns safe default permissions. This defensive mechanism strips the execution (`x`) bit from newly staged file payloads, creating a security sandbox.
* Bypassing "Permission Denied" Blocks: If an engineer attempts to run an unprovisioned file binary, the kernel halts execution with a failure warning. You must consciously re-evaluate the risk and inject an explicit execute bitweight.

Example — Auditing default permissions on a newly acquired security tool:

```bash
kali > ls -l
total 80
-rw-r--r-- 1 root root 1072 Dec 5 11:17 newhackertool

```

#### 🖼️ Terminal Output

---

Example — Escalating permissions to unlock owner execution (`766` = User: `rwx`, Group: `rw-`, Others: `rw-`):

```bash
kali > chmod 766 newhackertool

```

#### 🖼️ Terminal Output

---

Example — Verifying execution viability on the patched target:

```bash
kali > ls -l
total 80
-rwxrw-rw- 1 root root 1072 Dec 5 11:17 newhackertool

```

#### 🖼️ Terminal Output

---

## 🛠️ Utilities & Tool Reference

| Category | Component/Tool | Syntax / Structure | Description |
| --- | --- | --- | --- |
| **Mode Modification** | `chmod` (Numeric) | `chmod [Octal_Values] [Target]` | Instantly overwrites access arrays for User, Group, and World targets. |
| **Mode Modification** | `chmod` (Symbolic) | `chmod [u/g/o][+/-/=][r/w/x] [Target]` | Graphically appends or subtracts selective bits relative to current states. |

---

## 🔑 Key Takeaways for Revision

### Octal Calculation Framework

When reviewing or constructing file permissions using numeric formats, calculate the three-digit string from the target segments:

$$\text{chmod} \quad \underbrace{\mathbf{7}}_{\text{User: } 4+2+1} \quad \underbrace{\mathbf{6}}_{\text{Group: } 4+2+0} \quad \underbrace{\mathbf{4}}_{\text{Others: } 4+0+0} \quad \text{file.txt}$$

1. **Security-by-Default:** Linux natively blocks execution traits on newly staged file blocks to prevent runaway automated drive-by scripts from compromising the terminal ecosystem.
2. **Syntax Isolation:** When passing comma-separated statements in UGO notation (e.g., `chmod u+x,o+x`), never insert empty spaces between arguments, or the shell will process the second parameter as a target filename.

---
