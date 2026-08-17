# 🐬 Day 32 : Linux Task Automation & Job Scheduling

Welcome to Day 32 of my Linux Security learning journey. This document covers scheduling recurring jobs using `crond` and `crontab`, field syntax breakdowns, special operators, crontab shortcuts, system boot service automation, runlevels, and service management using `update-rc.d` and `rcconf`.

---

## 🎯 Key Points & Core Concepts

### 1. ⚙️ Recurring Task Automation with Cron

#### Core Concepts

* **`crond` Daemon:** A background daemon that runs continuously in Linux. It evaluates `/etc/crontab`, directory files in `/etc/cron.*/`, and user-specific crontabs every minute to execute scheduled tasks automatically.
* **`crontab` (Cron Table):** Configuration files where schedules, execution accounts, and absolute command paths are defined.

#### System-Wide Crontab Field Structure (`/etc/crontab`)

| Field # | Element | Valid Range | Description |
| --- | --- | --- | --- |
| **1** | Minute | `0–59` | Exact minute of the hour |
| **2** | Hour | `0–23` | 24-hour time notation (e.g., `13` = 1 PM, `23` = 11 PM) |
| **3** | Day of Month (DOM) | `1–31` | Day of the calendar month |
| **4** | Month (MON) | `1–12` | Month of the year (`1` = Jan, `12` = Dec) |
| **5** | Day of Week (DOW) | `0–7` | Day of the week (`0` and `7` both represent Sunday) |
| **6** | User Account | `root`, `username` | System user account executing the command |
| **7** | Command Path | Absolute Path | Exact absolute path to the binary or target script |

```plaintext
# Syntax Overview (System-Wide Crontab):
# m  h  dom  mon  dow  user  /path/to/command

```

---

### 2. 🔑 Cron Operators & Timing Shortcuts

#### Special Syntax Operators

* `*` **(Asterisk / Wildcard):** Matches every possible value within that field (e.g., every month or every day).
* `-` **(Dash / Range):** Specifies an inclusive continuous range of values (e.g., `1-5` for Monday through Friday, or `6-8` for June through August).
* `,` **(Comma / List):** Specifies discrete non-contiguous values (e.g., `15,30` for the 15th and 30th days, or `0,6` for Sunday and Saturday).

#### Predefined Crontab Shortcuts

| Predefined Shortcut | Equivalent Standard Schedule | Execution Timing Description |
| --- | --- | --- |
| **`@reboot`** | *N/A* | Executes once immediately at system startup |
| **`@yearly` / `@annually**` | `0 0 1 1 *` | Runs once a year (Jan 1 at midnight) |
| **`@monthly`** | `0 0 1 * *` | Runs once a month (1st day at midnight) |
| **`@weekly`** | `0 0 * * 0` | Runs once a week (Sunday at midnight) |
| **`@daily` / `@midnight**` | `0 0 * * *` | Runs once a day at 12:00 AM |
| **`@noon`** | `0 12 * * *` | Runs once a day at 12:00 PM |

---

### 3. 🛠️ Crontab Configuration Examples

#### Example 1: Weekday Security Scan at 2:30 AM

```plaintext
30 2 * * 1-5 root /root/myscanningscript.sh

```

* **Breakdown:** Minute `30`, Hour `2` (2:30 AM), every day of month/month, Monday through Friday (`1-5`), executed as `root`.

#### Example 2: Twice-Monthly System Backup at 2:00 AM

```plaintext
00 2 15,30 * * backup /bin/systembackup.sh

```

* **Breakdown:** Minute `0`, Hour `2` (2:00 AM), on the `15`th and `30`th days of every month, executed as user `backup`.

#### Example 3: Weekend Nightly Scan During Summer Months

```plaintext
00 2 * 6-8 0,6 user /usr/share/MySQLscanner.sh

```

* **Breakdown:** Minute `0`, Hour `2` (2:00 AM), June through August (`6-8`), on Saturdays and Sundays (`0,6`), executed as `user`.

#### Example 4: Daily Scan at Midnight Using Shortcut

```plaintext
@midnight user /usr/share/MySQLscanner.sh

```

* **Breakdown:** Replaces standard time fields with `@midnight` to run once every day at 12:00 AM.

---

### 4. 🏛️ Boot Automation with Runlevels & System Services

#### Operating System Runlevels

Runlevels define the execution mode of the system and dictate which background services initialize during startup:

| Runlevel | Mode / Target State | Description |
| --- | --- | --- |
| **`0`** | Halt | Shuts down and powers off the system. |
| **`1`** | Single-User Mode | Minimal environment without network services (used for maintenance & recovery). |
| **`2–5`** | Multi-User Modes | Standard multi-user operational states with network interfaces active. |
| **`6`** | Reboot | Restarts the operating system. |

---

#### Boot Service Management Workflow

1. **:** ps aux | grep <service_name.
" title="1. Verify Active Process State">
Check whether the target daemon or network service is currently running in memory prior to configuration.


2. **:** update-rc.d <service_name.
defaults" title="2. Register Default Boot Links">
Create initialization symlinks across standard multi-user runlevels using `update-rc.d` so the service starts on boot.


3. **3. Configure via Graphical Terminal Interface:** rcconf (Optional GUI).
Optionally open the `rcconf` TUI utility using Spacebar to select/deselect services across boot runlevels.


4. **:** reboot & ps aux | grep <service_name.
" title="4. Reboot & Verify Service State">
Restart the operating system and confirm that the service process auto-initialized successfully.


---

### 5. 🖼️ Terminal Commands & Execution Output Views

#### Modifying Crontabs (`crontab -e` & File Editing)

* **Open User Crontab Editor:**
```bash
kali > crontab -e

```


* **Inspect & Edit System-Wide Crontab Direct File:**
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
30 2    * * 1-5 root    /root/myscanningscript.sh

```

---

#### Managing Boot Services (`update-rc.d` & `ps`)

* **Verify Target Daemon Running State:**
```bash
kali > ps aux | grep postgresql

```



#### 🖼️ Terminal Output View (`ps aux | grep postgresql`)

```plaintext
postgres  1248  0.0  0.1  212340 18450 ?        S    10:14   0:00 /usr/lib/postgresql/13/bin/postgres -D /var/lib/postgresql/13/main -c config_file=/etc/postgresql/13/main/postgresql.conf
root      3412  0.0  0.0   12780   940 pts/0    S+   10:32   0:00 grep --color=auto postgresql

```

* **Enable PostgreSQL Auto-Startup at Boot:**
```bash
kali > update-rc.d postgresql defaults

```



#### 🖼️ Terminal Output View (`update-rc.d postgresql defaults`)

```plaintext
 Synchronizing State of postgresql.service with SysV service script with /lib/systemd/systemd-sysv-install.
 Executing: /lib/systemd/systemd-sysv-install enable postgresql

```

---

#### Managing Boot Services via TUI (`rcconf`)

* **Install and Launch GUI/TUI Configuration Utility:**
```bash
kali > apt-get install rcconf
kali > rcconf

```



#### 🖼️ Terminal View Simulation (`rcconf` Interface)

```plaintext
 ┌───────────────────────┤ Service Configuration ├────────────────────────┐
 │                                                                        │
 │  Select services to be started upon system boot:                       │
 │                                                                        │
 │    [*] cron ........................... System Command Scheduler       │
 │    [*] networking ..................... Networking Support             │
 │    [*] postgresql ..................... PostgreSQL Database Server     │
 │    [ ] ssh .................─────────── OpenBSD Secure Shell server   │
 │                                                                        │
 │                    <  OK  >            < Cancel >                      │
 │                                                                        │
 └────────────────────────────────────────────────────────────────────────┘

```

*(Navigation: Arrow keys to move, Spacebar to toggle `[*]`, Tab to select OK, Enter to save).*

---

### 6. 🗺️ Technology Comparison: Cron (`crond`) vs. System Boot (`init.d` / `update-rc.d`)

| Feature / Metric | Cron Job Scheduling (`crond`) ⏰ | Boot Service Automation (`update-rc.d`) 🚀 |
| --- | --- | --- |
| **Primary Design Goal** | Periodic time-based command execution | One-time daemon initialization during OS boot |
| **Trigger Mechanism** | Minute-by-minute system clock evaluation | Reaching a specific system runlevel target |
| **Configuration File** | User crontabs or `/etc/crontab` | Init scripts stored in `/etc/init.d/` |
| **Execution Frequency** | Recurring (e.g., hourly, daily, monthly) | Single execution during system startup |
| **Service Persistence** | Runs command to completion and exits | Starts daemons intended to run continuously |

---

## 🛠️ Utilities & Command Reference

| Utility / Command | Syntax Example | Primary Purpose / Description |
| --- | --- | --- |
| **`crontab -e`** | `crontab -e` | Opens the current user's crontab for editing. |
| **`crontab -l`** | `crontab -l` | Lists all scheduled cron jobs for the current user. |
| **`crontab -r`** | `crontab -r` | Removes all scheduled cron entries for the current user. |
| **`update-rc.d`** | `update-rc.d postgresql defaults` | Registers SysV init scripts to default boot runlevels. |
| **`update-rc.d -f disable`** | `update-rc.d -f postgresql disable` | Disables service initialization links across boot runlevels. |
| **`rcconf`** | `rcconf` | Terminal GUI (TUI) tool to toggle boot services interactively. |
| **`ps aux`** | `ps aux | grep cron` | Lists active process tables to confirm running daemons. |

---

## 🔑 Key Takeaways for Revision

* **Crontab Structure Difference:** User crontabs (`crontab -e`) use **5 time fields**, whereas the system-wide `/etc/crontab` file includes a **6th field for the User Account** before the command.
* **Absolute Path Requirement:** Cron executes scripts with a minimal environment; always use absolute paths for scripts and binaries (e.g., `/usr/bin/nmap` instead of `nmap`).
* **Day Field Overlap:** Setting both Day of Month and Day of Week creates an `OR` condition—cron runs if *either* field matches.
* **Runlevel 1 vs. Multi-User:** Single-user mode (Runlevel 1) disables network daemons for recovery, whereas Runlevels 2–5 enable networking and startup services.
* **Automation Workflow:**

$$\text{Check Running Process } (\texttt{ps aux}) \longrightarrow \text{Edit Cron } (\texttt{crontab -e}) \longrightarrow \text{Configure Boot Links } (\texttt{update-rc.d}) \longrightarrow \text{Verify Execution}$$

---
