# 🐬 Day 32: Linux Task Automation & Job Scheduling

Welcome to Day 32 of my Linux Security learning journey. This document covers scheduling recurring tasks using the `crond` daemon and `crontab`, time field syntax rules, special operators, preset shortcuts, system runlevel architecture, boot service automation using `update-rc.d`, and service management via `rcconf`.

---

## 🎯 Key Points & Core Concepts

### 1. ⚙️ Task Automation Concepts & Use Cases

#### Strategic Objectives

* **Admin / Defensive:** Automate periodic system backups, rotate log files, clean temporary directories, and maintain system performance without manual intervention.
* **Hacker / Offensive:** Automate stealth network scanning scripts (e.g., `/usr/share/MySQLscanner.sh`) during target off-hours or maintenance windows.
* **System Boot Automation:** Automatically launch essential database daemons (e.g., `PostgreSQL` for Metasploit) upon system startup to eliminate manual service initialization prior to security testing.

---

### 2. 🏛️ Cron Architecture & Time Syntax Breakdown

#### Core Infrastructure

* **`crond` Daemon:** Background daemon that continuously evaluates `/etc/crontab`, directory entries in `/etc/cron.*/`, and user-specific crontab files every 60 seconds.
* **`crontab` File:** Configuration table storing execution schedules, user context, and absolute command paths.

#### System-Wide Crontab Field Structure (`/etc/crontab`)

A system-wide crontab entry contains **7 total fields**:

```plaintext
# Field Structure Syntax:
# M  H  DOM  MON  DOW  USER  COMMAND

```

| Field # | Time Unit | Accepted Range / Values | Operational Rules |
| --- | --- | --- | --- |
| **1** | Minute | `0–59` | Precise minute of execution. |
| **2** | Hour (24-hr) | `0–23` | 24-hour military notation (e.g., `13` = 1 PM, `23` = 11 PM). |
| **3** | Day of Month (DOM) | `1–31` | Calendar day of the month. |
| **4** | Month (MON) | `1–12` | Numerical value required (e.g., `3` = March; text names like "March" are invalid). |
| **5** | Day of Week (DOW) | `0–7` | Numerical day of the week (`0` and `7` both represent **Sunday**). |
| **6** | User Context | `root`, `username` | System user account executing the process. |
| **7** | Command Path | Absolute Path | Direct absolute binary/script path (e.g., `/usr/bin/nmap`). |

#### Field Syntax Operators

* `*` **(Wildcard):** Matches every possible unit of time for that field (e.g., every month or every day).
* `-` **(Range):** Specifies an inclusive continuous block of time (e.g., `1-5` = Monday through Friday).
* `,` **(List):** Defines discrete, non-contiguous values (e.g., `15,30` = 15th and 30th days of the month).

---

### 3. 🔑 Crontab Shortcuts & Real-World Configuration Scenarios

#### Preset Time Shortcuts

Instead of populating the first 5 numerical fields, built-in shortcuts simplify standard scheduling routines:

| Preset Shortcut | Equivalent Five-Field Schedule | Execution Interval Description |
| --- | --- | --- |
| **`@reboot`** | *N/A* | Executes once immediately at system boot. |
| **`@yearly` / `@annually**` | `0 0 1 1 *` | Runs once a year (Jan 1 at midnight). |
| **`@monthly`** | `0 0 1 * *` | Runs once a month (1st day at midnight). |
| **`@weekly`** | `0 0 * * 0` | Runs once a week (Sunday at midnight). |
| **`@daily` / `@midnight**` | `0 0 * * *` | Runs once a day at 12:00 AM. |
| **`@noon`** | `0 12 * * *` | Runs once a day at 12:00 PM. |

#### Real-World Crontab Syntax Examples

| Operational Target Scenario | Complete Crontab Syntax String |
| --- | --- |
| **Daily scan at 2:30 AM (Mon–Fri)** | `30 2 * * 1-5 root /root/myscanningscript` |
| **Weekly backup (Every Sunday at 2:00 AM)** | `00 2 * * 0 backup /bin/systembackup.sh` |
| **Bi-monthly backup (15th & 30th at 2:00 AM)** | `00 2 15,30 * * backup /root/systembackup.sh` |
| **Nightly scan at 9:00 AM (Every day)** | `00 9 * * * user /usr/share/MySQLscanner.sh` |
| **Summer weekend scan (June–Aug at 2:00 AM)** | `00 2 * 6-8 0,6 user /usr/share/MySQLscanner.sh` |
| **Daily scan at midnight using shortcut** | `@midnight user /usr/share/MySQLscanner.sh` |

---

### 4. 🛠️ System Boot Architecture & Service Management Workflow

#### Linux Runlevel Definitions

Runlevels dictate the operational mode of the system and define which background daemons initialize during boot:

| Runlevel | Mode / System State | Purpose / Description |
| --- | --- | --- |
| **`0`** | Halt System | Powers off the hardware completely. |
| **`1`** | Single-User Mode | Minimal diagnostic state without network interfaces (used for system recovery). |
| **`2–5`** | Multi-User Modes | Standard operational environments with active networking and user services. |
| **`6`** | Reboot System | Restarts the operating system. |

---

#### Boot Automation & Verification Workflow

1. **1. Inspect Active Process Table:** ps aux | grep postgresql.
Query active processes using `ps aux | grep <process_name>` to verify whether the daemon is running before making service changes.


2. **2. Enable Boot Initialization via CLI:** sudo update-rc.d postgresql defaults.
Register initialization symlinks for the daemon across standard multi-user runlevels using `update-rc.d`.


3. **3. Manage Boot Services via TUI Interface:** sudo rcconf.
Optionally launch the `rcconf` graphical/terminal interface. Use arrow keys, `Spacebar` to select/deselect, and `Tab` to confirm changes.


4. **4. Reboot Host & Confirm Autostart:** reboot & ps aux | grep postgresql.
Restart the system and verify that the target daemon's absolute path appears in the active process table automatically.


---

### 5. 🖼️ Terminal Commands & Execution Output Views

#### Modifying Crontabs (`crontab -e` & Configuration Files)

* **Safely Edit User Crontab Entry:**
```bash
kali > crontab -e

```


* **Direct Editing of System-Wide Configuration File:**
```bash
kali > nano /etc/crontab

```



#### 🖼️ Terminal Output View (`cat /etc/crontab`)

```plaintext
# /etc/crontab: system-wide crontab
SHELL=/bin/sh
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin

# m h dom mon dow user	command
17 *	* * *	root	cd / && run-parts --report /etc/cron.hourly
25 6	* * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.daily )
47 6	* * 7	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.weekly )
52 6	1 * *	root	test -x /usr/sbin/anacron || ( cd / && run-parts --report /etc/cron.monthly )
30 2    * * 1-5 root    /root/myscanningscript

```

---

#### Service Management Commands (`ps` & `update-rc.d`)

* **Process Filtering Command:**
```bash
kali > ps aux | grep postgresql

```



#### 🖼️ Terminal Output View (`ps aux | grep postgresql` - Not Running)

```plaintext
root      3412  0.0  0.0   12780   940 pts/0    S+   10:32   0:00 grep --color=auto postgresql

```

*(Only the `grep` process is returned, proving the daemon is currently inactive).*

* **Enable PostgreSQL Boot Persistence:**
```bash
kali > sudo update-rc.d postgresql defaults

```



#### 🖼️ Terminal Output View (`update-rc.d`)

```plaintext
 Synchronizing State of postgresql.service with SysV service script with /lib/systemd/systemd-sysv-install.
 Executing: /lib/systemd/systemd-sysv-install enable postgresql

```

* **Post-Reboot Active Verification View:**
```bash
kali > ps aux | grep postgresql

```



#### 🖼️ Terminal Output View (`ps aux | grep postgresql` - Running)

```plaintext
postgres  1248  0.0  0.1  212340 18450 ?        S    10:14   0:00 /usr/lib/postgresql/13/bin/postgres -D /var/lib/postgresql/13/main -c config_file=/etc/postgresql/13/main/postgresql.conf
root      3412  0.0  0.0   12780   940 pts/0    S+   10:32   0:00 grep --color=auto postgresql

```

---

#### TUI Service Management Utility (`rcconf`)

* **Install & Launch Utility:**
```bash
kali > sudo apt-get install rcconf
kali > sudo rcconf

```



#### 🖼️ Terminal View Simulation (`rcconf`)

```plaintext
 ┌───────────────────────┤ Service Configuration ├────────────────────────┐
 │                                                                        │
 │  Select services to be started upon system boot:                       │
 │                                                                        │
 │    [*] cron ........................... System Command Scheduler       │
 │    [*] networking ..................... Networking Support             │
 │    [*] postgresql ..................... PostgreSQL Database Server     │
 │    [ ] ssh ............................ OpenBSD Secure Shell server    │
 │                                                                        │
 │                    <  OK  >            < Cancel >                      │
 │                                                                        │
 └────────────────────────────────────────────────────────────────────────┘

```

---

### 6. 🗺️ Technology Comparison: Cron Jobs vs. Boot Services

| Feature / Metric | Recurring Task Scheduler (`crond`) ⏰ | System Boot Automation (`update-rc.d`) 🚀 |
| --- | --- | --- |
| **Execution Objective** | Periodic, time-triggered task execution | One-time daemon startup during system boot |
| **Primary Trigger** | System clock matching designated interval | System reaching target operational runlevel |
| **Configuration File** | User crontabs or `/etc/crontab` | Init scripts inside `/etc/init.d/` |
| **User Context** | Specified per entry (`user` field or crontab owner) | Usually executes under `root` or dedicated service accounts |
| **Lifetime Pattern** | Runs script to completion and exits | Starts background daemons intended to stay active |

---

## 🛠️ Utilities & Command Reference

| Command / Utility | Example Syntax | Primary Purpose / Description |
| --- | --- | --- |
| **`crontab -e`** | `crontab -e` | Safely opens user crontab in default terminal editor. |
| **`crontab -l`** | `crontab -l` | Lists scheduled cron jobs for current logged-in user. |
| **`crontab -r`** | `crontab -r` | Deletes user's current crontab file completely. |
| **`update-rc.d`** | `sudo update-rc.d postgresql defaults` | Registers SysV init service scripts across default runlevels. |
| **`update-rc.d disable`** | `sudo update-rc.d -f postgresql disable` | Disables auto-start links for specified service across runlevels. |
| **`rcconf`** | `sudo rcconf` | Launches TUI interface to toggle boot services visually. |
| **`ps aux`** | `ps aux | grep postgresql` | Filters active process table to verify service state. |

---

## 🔑 Key Takeaways for Revision

* **Field Count Variation:** User crontabs (`crontab -e`) use **5 time fields**, while `/etc/crontab` uses **7 fields** (includes explicit `USER` field).
* **Numerical Requirements:** The Month field strictly requires numbers `1–12` (no month names), and Day of Week accepts `0` or `7` for Sunday.
* **Absolute Path Rule:** Cron executes with a sparse environment; always specify full absolute binary and script paths.
* **Runlevel 1 vs. 6:** Runlevel `1` enters single-user mode for maintenance without networking, while Runlevel `6` reboots the host.
* **Service Persistence Command:** Use `update-rc.d <service> defaults` to ensure critical tools (like PostgreSQL) start on boot.
* **Task Automation Workflow:**

$$\text{Check Running Service } (\texttt{ps aux}) \longrightarrow \text{Edit Cron } (\texttt{crontab -e}) \longrightarrow \text{Configure Boot Links } (\texttt{update-rc.d}) \longrightarrow \text{Verify Execution}$$

---

*⚡ End of Week 05 • Day 02 Notes • Organized for GitHub & OneNote*
