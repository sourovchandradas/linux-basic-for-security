# 🐧 Day 23 : The Logging System

Welcome to Day 23 of my Linux Security learning journey. This document details the architectural evolution from legacy `syslogd` to `systemd-journald`, structured querying using `journalctl`, log priority and facility filtering, anti-forensic techniques like log vacuuming and multi-pass shredding (`shred`), and neutralizing log generation by configuring `Storage=null`.

---

## 🎯 Key Points & Core Concepts

### 1. 📜 Introduction & Logging Framework Evolution

* **Objectives of Linux Log Management:**
* **Forensic / Defensive:** Incident response, reconstruct attack timelines, track user activity (who did what).
* **Anti-Forensic / Offensive:** Cover execution tracks, sanitize service logs, or completely suppress system logging during post-exploitation.


* **Architectural Shift (`syslogd` vs. `systemd-journald`):**
* **Legacy (`syslog` / `syslogd`):** Stores logs as plain-text files under `/var/log`. Searching requires custom shell scripts with `grep`, `awk`, and `sed`.
* **Modern (`systemd-journald`):** Stores structured binary log files under `/var/log/journal/`. Queried natively across Linux distributions using `journalctl`.


* **Basic Execution Behavior:**
* Running bare `journalctl` displays system logs sequentially, automatically piping the output into the `more` terminal pager.



Example — Viewing system logs with bare `journalctl`:

```bash
journalctl

```

#### 🖼️ Terminal Command
![Journal utility command](Screenshot/journalctl-command.png)

#### 🖼️ Terminal Output
![Journal utility output-01](Screenshot/journalctl-output-01.png)
![Journal utility output-02](Screenshot/journalctl-output-02.png)
![Journal utility output-03](Screenshot/journalctl-output-03.png)
![Journal utility output-04](Screenshot/journalctl-output-04.png)

---

### 2. 🛠️ Command-Line Options & Help System Breakdown (`journalctl -h`)

* Display syntax and help options:

```bash
journalctl -h

```
#### 🖼️ Terminal Command




#### Key Source & Filtering Flags

| Flag / Option | Description / Function |
| --- | --- |
| `--system` | Query the main system journal. |
| `--user` | Query log entries for the current user session. |
| `-D, --directory=PATH` | Query journal files from a custom directory path. |
| `-S, --since=DATE` | Show log entries strictly **newer** than the specified date/time. |
| `-U, --until=DATE` | Show log entries strictly **older** than the specified date/time. |
| `-b, --boot[=ID]` | Filter logs for the current boot cycle or specified boot ID. |
| `-u, --unit=UNIT` | Filter logs matching a specific service unit (e.g., `apache2`, `ssh`). |
| `-p, --priority=RANGE` | Filter by log severity level (0–7 or textual name). |
| `-g, --grep=PATTERN` | Filter log entries matching a regular expression pattern. |
| `-k, --dmesg` | Filter kernel ring buffer messages from the current boot. |

---

### 3. 🚨 Log Priorities and Facility Filters

* **Numerical Severity Levels (0 to 7):**
Linux categorizes log messages by severity. **Lower numerical values represent higher severity.**

| Code | Severity | Description |
| --- | --- | --- |
| **0** | `emerg` | System is unusable (highest severity). |
| **1** | `alert` | Action must be taken immediately. |
| **2** | `crit` | Critical conditions (e.g., kernel panics, CPU lockups). |
| **3** | `err` | Non-critical error conditions. |
| **4** | `warning` | Warning conditions. |
| **5** | `notice` | Normal but significant conditions. |
| **6** | `info` | Informational messages. |
| **7** | `debug` | Debug-level messages (lowest severity). |

* **Filtering by Priority (`-p`):**
* Specifying a priority by number returns that level **and all higher-severity levels above it**.



Example — Querying logs by service unit and priority:

```bash
journalctl -u apache2 -p err

```
#### 🖼️ Terminal Command
![Journalctl -p err command](Screenshot/journalctl-p-err-command.png)

#### 🖼️ Terminal Output
![Journalctl -p err output-01](Screenshot/journalctl-p-err-output-01.png)
![Journalctl -p err output-02](Screenshot/journalctl-p-err-output-02.png)
![Journalctl -p err output-03](Screenshot/journalctl-p-err-output-03.png)

---

### 4. 🔍 Advanced `journalctl` Querying Techniques

* **Time-Based Filtering (`--since`):** Accepts human-readable relative time strings or ISO timestamps.
* **Quiet Mode (`-q`):** Suppresses status banners, headers, and metadata to reduce terminal footprint.
* **User ID Matchers (`_UID`):** Isolates log events tied directly to a specific user account (Root UID = `0`, standard user UID = `1000`).

Example — Querying logs triggered by User ID `1000` from the past 24 hours in quiet mode:

```bash
journalctl _UID=1000 -q --since "24 hours ago"

```
#### 🖼️ Terminal Command
![journalctl utility by uid command](Screenshot/journalctl-utility-by-uid-command.png)

#### 🖼️ Terminal Output
![journalctl utility by uid output-01](Screenshot/journalctl-utility-by-uid-output-01.png)
![journalctl utility by uid output-02](Screenshot/journalctl-utility-by-uid-output-02.png)

---

Example — Viewing kernel-level ring buffer messages (`-k`):

```bash
journalctl -k --since "24 hours ago"

```
#### 🖼️ Terminal Command
![Journalctl utility kernel command](Screenshot/journalctl-utility-kernel-command.png)

#### 🖼️ Terminal Output
![Journalctl utility kernel Output-01](Screenshot/journalctl-utility-kernel-output-o1.png)
![Journalctl utility kernel Output-02](Screenshot/journalctl-utility-kernel-output-o2.png)
![Journalctl utility kernel Output-03](Screenshot/journalctl-utility-kernel-output-o3.png)

---

### 5. 🧹 Anti-Forensics: Log Vacuuming and Multi-Pass Shredding

* **Selective Vacuuming (`--vacuum-time`):** Truncates archived journal files older than a specified relative timeframe.

```bash
sudo journalctl -u apache2 --vacuum-time=1d

```
#### 🖼️ Terminal Command
![delete 1 day traks command](Screenshot/delete-1day-tracks-command.png)

#### 🖼️ Terminal Output
![delete 1 day tracks output](Screenshot/delete-1day-tracks-output.png)


* **Standard Deletion (`rm`) vs. Secure Overwriting (`shred`):**
* **`rm` File Deletion:** Unlinks file metadata pointers; raw binary data remains intact on disk blocks until overwritten, making it easily recoverable via forensics tools.
* **`shred` Utility:** Overwrites targeted disk blocks multiple times with random bit patterns.
* **Default Pass Count:** `shred` executes **4 overwrite passes** by default.



Example — Executing a 10-pass forced secure wipe on journal binary logs:

```bash
sudo shred -f -n 10 /var/log/journal/*/*

```

#### 🖼️ Terminal Output

> ⚠️ **Verification:** Attempting to view a shredded binary log using a text editor (e.g., `mousepad`) results in unreadable, corrupted binary garbage.

---

### 6. 🚫 Neutralizing Logging (`Storage=null`)

* **Concept:** Redirects all incoming system and service log streams directly to a null sink (`/dev/null`), preventing log generation on disk entirely.
* **Target Configuration File:** `/etc/systemd/journald.conf`

Example — Disabling persistent system logging:

```bash
# 1. Open the journald configuration file
kali > sudo mousepad /etc/systemd/journald.conf

# 2. Modify the Storage directive under [Journal] header:
# Change: #Storage=auto  --->  Storage=null

# 3. Restart the systemd-journald daemon to enforce changes
kali > sudo systemctl restart systemd-journald

```

#### 🖼️ Terminal Output

---

## 🛠️ Utilities & Tool Reference

| Tool / File | Syntax / Example | Purpose / Description |
| --- | --- | --- |
| `journalctl` | `journalctl -u ssh -S "1 hour ago"` | Command-line utility used to query systemd binary log journals. |
| `journald.conf` | `/etc/systemd/journald.conf` | Main configuration file governing journald storage behavior. |
| `shred` | `shred -f -n 10 [file]` | Securely overwrites files multiple times with random data to prevent forensic recovery. |
| `systemctl` | `systemctl restart systemd-journald` | Controls systemd daemons and reloads modified service configurations. |

---

## 🔑 Key Takeaways for Revision

1. **Syslog vs. Journald Storage Formats:**

$$\text{syslogd } \longrightarrow \text{Plain-Text Files } (/var/log)$$


$$\text{systemd-journald } \longrightarrow \text{Binary Files } (/var/log/journal/)$$


2. **Priority Code Scale:** Lower numerical priority numbers equal higher severity levels:

$$0 \text{ (emerg)} > 1 \text{ (alert)} > 2 \text{ (crit)} > 3 \text{ (err)} > 4 \text{ (warning)} > 5 \text{ (notice)} > 6 \text{ (info)} > 7 \text{ (debug)}$$


3. **Forensic Overwrite Difference:**

$$\texttt{rm} \longrightarrow \text{Unlinks metadata pointers (Data recoverable)}$$


$$\texttt{shred -f -n <passes>} \longrightarrow \text{Multi-pass bitwise overwrite (Data unrecoverable)}$$


4. **Complete Log Suppression Directive:** Set `Storage=null` in `/etc/systemd/journald.conf` and restart `systemd-journald`.

---
