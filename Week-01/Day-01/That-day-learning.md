# 🐧 Day 01: Linux Basics & Essential Terms for Hackers

Welcome to Day 01 of my Linux Security learning journey. This document is a concise, corrected, and slightly expanded summary of foundational concepts required to understand and operate in a Linux environment efficiently. It focuses on terminology, system concepts, and a few practical examples to get you started safely.

---

## 🔭 What you'll learn (quick overview)

- Why Linux is case-sensitive and how that affects everyday use.
- The role and risks of the root account.
- Basic filesystem layout and where user files live.
- Difference between a terminal and a shell.
- What binaries and scripts are, and why they matter for security work.
- A short hands-on lab to practice safely.

---

## 🎯 Key Points & Core Concepts

### 1. 🔤 Case Sensitivity
Unlike Windows, the Linux filesystem is strictly **case-sensitive**.
* `Desktop`, `desktop`, and `DeskTop` are treated as three completely different entities.
* Troubleshooting tip: If you receive a "file or directory not found" error while you are sure the file exists, always double-check the filename's casing and surrounding path.

### 2. 👑 The `root` Account
* Definition: The `root` account is the absolute administrator (superuser) in Linux.
* Privileges: It has unrestricted power to reconfigure the system, add or remove users, modify system files, and change network settings.
* Security note: Using `root` carelessly can damage the system or expose it to misconfiguration. Use `sudo` for single commands when possible and avoid logging in as root interactively unless necessary.

### 3. 📂 Directory Structure & Home
* Linux uses a single rooted hierarchy starting at `/`.
* Important top-level directories: `/home` (user directories), `/etc` (configuration), `/bin` and `/usr/bin` (user binaries), `/var` (variable data), `/tmp` (temporary files), `/root` (root user's home).
* Each user has a personal directory under `/home` (e.g., `/home/username`). New personal files are typically created here.

### 4. ⚙️ Binaries & Executables
* Binaries are compiled program files that the kernel can execute directly (e.g., ELF files). They are typically stored in `/bin`, `/usr/bin`, `/sbin`, or `/usr/sbin`.
* You can check whether a file is executable with `ls -l` (look for `x` in permissions) and run it if permitted: `./script.sh` or `sudo some-binary`.

### 5. 💻 Shell vs. Terminal
* Terminal: The terminal emulator (application) where you type (e.g., GNOME Terminal, xterm, Windows Terminal, or a virtual console).
* Shell: The command interpreter that runs inside the terminal (e.g., `bash`, `zsh`, `sh`). The shell reads your commands, parses them, and asks the OS to execute them.
* Practical difference: You can have many terminals open but the shell is the software that understands your commands. Customize your shell via `~/.bashrc` (for bash) or `~/.zshrc` (for zsh).

### 6. 📜 Scripts
* A script is a plain text file containing a sequence of commands executed by an interpreter line-by-line (for example, `bash` or `python`).
* Make a script executable: `chmod +x script.sh` and run it with `./script.sh` (or explicitly with the interpreter: `bash script.sh`).
* Python is widely used for tooling and automation in security work; keep an eye on dependencies and virtual environments (use `venv` or `pipx`).

### 7. 🐉 Kali Linux (brief)
* Kali is a specialized Debian-based distribution tailored for penetration testing and digital forensics. It includes many preinstalled security tools.
* Note: Kali is a toolset, not a guide — learn the basics first, and always use Kali and its tools ethically and legally.

---

## 🛠️ Utilities & Tool Reference

| Category | Component / Tool | Description |
| --- | ---: | --- |
| Common Binaries | `ps`, `cat`, `ls`, `ifconfig` | Standard utilities for viewing processes, reading files, listing directories, and querying network interfaces (note: `ifconfig` is legacy on some distros; prefer `ip`). |
| Security Tools | `aircrack-ng`, `snort` | Tools for wireless auditing and network IDS respectively. Use responsibly. |
| Default Shell | `bash` | The common default shell; many tutorials assume `bash` but `zsh` and others are popular too. |

---

### 📝 Example of Case Sensitivity

```bash
# Creating two different files due to case sensitivity
touch Secret.txt
touch secret.txt

# Attempting to access the file with wrong case will return:
cat SECRET.txt
# Output: cat: SECRET.txt: No such file or directory
```

---

## 🔐 Safety & Best Practices (short)

- Use `sudo` for single commands instead of logging in as root.
- Test risky commands in an isolated VM or container.
- Keep backups of configuration files before editing them (e.g., `cp /etc/some.conf /etc/some.conf.bak`).
- For persistent changes to system config (network, service files), learn your distribution's management tool (NetworkManager, netplan, systemd-networkd).

---

## 🧪 Mini Lab — Safe steps to practice (5–10 minutes)

1. Open a terminal in a disposable VM or container.
2. Create two files that differ only by case:

```bash
cd ~
mkdir -p lab-day01 && cd lab-day01
touch Report.txt
touch report.txt
ls -l
```

Expected: `Report.txt` and `report.txt` both exist as separate files.

3. Create a small script and run it:

```bash
cat > hello.sh <<'EOF'
#!/bin/bash
 echo "Hello from Day 01 lab"
EOF
chmod +x hello.sh
./hello.sh
```

Expected: The script prints the greeting. If it doesn't, check the shebang and executable bit.

4. Practice `sudo` safely:

```bash
# Show current user and try a harmless sudo command
whoami
sudo -v  # refresh sudo timestamp (no harm)
```

5. Clean up lab files when finished:

```bash
cd ~
rm -rf lab-day01
```

---

## 📚 Further reading

- man pages: `man bash`, `man ls`, `man sudo`
- The Linux Documentation Project: https://www.tldp.org/
- Debian Administrator's Handbook (useful even if not running Debian): https://debian-handbook.info/

---

*Changelog: Filled truncated sentences, clarified shell vs terminal, added quick overview, safety notes, and a short hands-on lab. If you want, I can also:*
- Add screenshots or command outputs, or
- Create a similar improved draft for Week-02/Day-01 (networking), or
- Open a PR with these changes if you'd prefer that workflow.
