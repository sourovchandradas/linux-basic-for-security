# Week 05 — Day 04: Linux Task Automation & Job Scheduling

## Summary
This note explains how to schedule recurring tasks on Linux using cron, how to run jobs at system startup, and modern alternatives (anacron and systemd timers). It includes practical examples, best practices for reliability and security, logging/debugging tips, and templates you can copy into your systems.

---

## 1) Cron: Basics and Field Layout
Cron is a background daemon (crond) that runs commands at scheduled times according to crontab entries.

System-wide crontab files (e.g., `/etc/crontab` and files in `/etc/cron.d/`) include a user field and therefore have seven columns:

FIELD | DESCRIPTION
---|---
Minute (0–59) | exact minute
Hour (0–23) | 24-hour hour
Day of month (1–31) | day of month
Month (1–12) | month
Day of week (0–7) | 0 and 7 = Sunday
User | user account to run the command as
Command | absolute path to command or script

Per-user crontabs (edited with `crontab -e`) do NOT include the user column — they contain only the five time fields followed by the command.

Operators: `*` (any), `-` (range), `,` (list), `/` (step values, e.g., `*/15` for every 15 minutes).

Shortcuts: `@reboot`, `@yearly`, `@annually`, `@monthly`, `@weekly`, `@daily`, `@midnight`, `@noon`.

---

## 2) Common Examples
- Weekdays (Mon–Fri) at 02:30:

```
30 2 * * 1-5 root /root/myscanningscript.sh
```

- Mid-month (15th & 30th) at 02:00 as user `backup`:

```
00 2 15,30 * * backup /bin/systembackup.sh
```

- Summer weekends (June–August) at 02:00 on Saturday and Sunday as `user`:

```
00 2 * 6-8 0,6 user /usr/share/MySQLscanner.sh
```

- Use a shortcut to run daily at midnight:

```
@midnight user /usr/share/MySQLscanner.sh
```

---

## 3) System vs Per-user crontab
- `/etc/crontab` (system): contains a user column; system services and multi-user scheduling.
- `/etc/cron.d/`: also system-wide and supports file-based job definitions (must include user column).
- `crontab -e`: per-user crontab; no user column. Jobs run as the editing user.

Always prefer absolute paths in crontab entries because cron runs with a minimal PATH.

---

## 4) Best Practices (reliability & maintenance)
1. Set explicit SHELL and PATH at the top of system crontab (example for `/etc/crontab`):

```
SHELL=/bin/bash
PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin
```

2. Use absolute paths for all commands and script references (`/usr/bin/python3`, `/usr/bin/rsync`).

3. Redirect stdout/stderr to log files so you can inspect job output:

```
00 02 * * * backup /bin/systembackup.sh >> /var/log/systembackup.log 2>&1
```

4. Prevent overlapping executions with locking (`flock`):

```
00 2 * * * user flock -n /var/lock/myscan.lock /usr/share/MySQLscanner.sh >> /var/log/myscan.log 2>&1
```

Or implement locking inside your script using a PID file or `flock`.

5. Exit codes and monitoring: ensure scripts return non-zero exit codes on failure and integrate logs with monitoring/alerting.

6. Test jobs manually with the same user and environment the cron will use:

```
sudo -u backup /bin/systembackup.sh
```

7. Use readable, structured logs (timestamps) and log rotation (logrotate) for long-running logging.

8. Keep credentials out of scripts or restrict their permissions and use secure stores or environment variables with caution.

---

## 5) Logging & Debugging Cron Jobs
- Check system logs:
  - Debian/Ubuntu (sysv cron): `grep CRON /var/log/syslog` or `tail -f /var/log/syslog | grep cron`
  - systemd-based cron: `journalctl -u cron --since "1 hour ago"`
- Have your script log start/stop and important steps; use `set -x` and redirect output to a logfile for debugging.
- Inspect mail for cron output (some systems mail output to the user). Configure `MAILTO=` in crontab if needed.

---

## 6) anacron — for machines that are not always on
- `anacron` ensures daily/weekly/monthly jobs are executed if the machine was off at the scheduled time.
- `/etc/anacrontab` example entry:

```
# period delay job-identifier command
1 10 cron.daily run-parts /etc/cron.daily
```

- anacron is complementary to cron and useful on laptops or desktops that are frequently powered off.

---

## 7) systemd timers — modern alternative
systemd timers can replace cron in many setups and offer richer features (calendar expressions, persistent runs, unit dependency integration).

Example: Create `/etc/systemd/system/myscan.service`:

```
[Unit]
Description=Run MySQL scanner

[Service]
Type=oneshot
ExecStart=/usr/share/MySQLscanner.sh
User=myuser
```

Create `/etc/systemd/system/myscan.timer`:

```
[Unit]
Description=Run MySQL scanner daily at 09:00

[Timer]
OnCalendar=*-*-* 09:00:00
Persistent=true

[Install]
WantedBy=timers.target
```

Enable & start the timer:

```
sudo systemctl daemon-reload
sudo systemctl enable --now myscan.timer
```

View timer status and last run:

```
systemctl list-timers --all | grep myscan
journalctl -u myscan.service --since "1 day ago"
```

systemd timers are preferable when you need integration with service units, failure handling, or precise calendar scheduling.

---

## 8) Security Considerations
- Run scanners and non-essential tasks as non-root users (`User=` in systemd or the user field in `/etc/crontab`).
- Set strict permissions for scripts (`chmod 700`) and restrict who can edit crontabs.
- Be aware of the legal and ethical implications of running network scanners.

---

## 9) Quick Cheat-Sheet
- Edit user crontab: `crontab -e`
- List user crontab: `crontab -l`
- Edit system crontab: `sudo nano /etc/crontab`
- Add service to rc.d (SysV): `sudo update-rc.d <service> defaults`
- Install rcconf (TUI): `sudo apt-get install rcconf` then run `rcconf`
- Check processes: `ps aux | grep <process>`
- Check cron logs: `tail -f /var/log/syslog | grep cron` or `journalctl -u cron`

---

## 10) Testing Recipes
- Run the script as the target user: `sudo -u backup /bin/systembackup.sh`
- Simulate cron environment (minimal env):

```
env -i SHELL=/bin/bash PATH=/usr/local/sbin:/usr/local/bin:/sbin:/bin:/usr/sbin:/usr/bin /bin/bash -c '/usr/share/MySQLscanner.sh >> /tmp/myscan.debug 2>&1'
```

- Check the timestamped log output and `journalctl` for systemd timers.

---

## References
- `man 5 crontab`, `man cron`, `man anacron`
- systemd timers: https://www.freedesktop.org/software/systemd/man/systemd.timer.html

---

*File generated and formatted for readability. Tested examples are templates—adapt paths, users, and filenames to your environment.*
