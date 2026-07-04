# 📖 Learning Linux Basics for Security

## 🖥️ A Tour of Kali Linux

### Login Screen
When you start Kali, you will see a login screen.

![Logging into Kali](./A%20tour%20of%20Kali/Logging-Into-Kali.png)

### Default Credentials
- **Username:** `root`  
- **Password:** `toor` (use your new password if you changed it earlier)

### After Login
You will get access to the Kali desktop interface.

![The Kali Desktop](A%20tour%20of%20Kali/The-Kali-Desktop.png)

### Key Aspects to Explore
- Terminal Interface
- File Structure
- Desktop Environment

---

## 💻 The Terminal (Command Line Interface)

### How to Open the Terminal
Click the terminal icon on the left side of the Kali desktop.

![The Kali Terminal](A%20tour%20of%20Kali/The-Kali-Terminal.png)

### What is the Shell?
The terminal opens the command line environment known as the **shell**. It enables you to:
- Run commands on the underlying operating system
- Write and execute scripts
- Manage files and directories
- Configure system settings

### Default Shell: Bash
**Bash (Bourne-Again SHell)** is the most popular and default shell in Kali and many other Linux distributions.

### Change Your Password
Use the `passwd` command to change your password:

```bash
passwd
```

**Output:**
```
Changing password for root.
Current password:
New password:
Retype new password:
```

![Default Password Change](https://raw.githubusercontent.com/sourovchandradas/linux-basic-for-security/main/Week-01/Day-02/A%20tour%20of%20Kali/Default-Password-Change.png)

### Essential Basic Commands

| Command | Purpose | Example |
|---------|---------|---------|
| `pwd` | Print working directory | `pwd` |
| `ls` | List files and directories | `ls` or `ls -la` |
| `cd` | Change directory | `cd /home` |
| `mkdir` | Make a new directory | `mkdir my-folder` |
| `touch` | Create empty file | `touch file.txt` |
| `cat` | Display file contents | `cat file.txt` |
| `cp` | Copy files | `cp source.txt dest.txt` |
| `mv` | Move/rename files | `mv old.txt new.txt` |
| `rm` | Remove files | `rm file.txt` |
| `sudo` | Run as superuser | `sudo apt-get update` |

---

## 📂 The Linux Filesystem

### Structure Concept
Unlike Windows, Linux does not have physical drives (like C: drive) at the base. Instead, it uses a **logical filesystem** structure.

### The Root Directory (/)
At the very top of the structure is **"/"** (the root of the filesystem), which looks like an upside-down tree. All other directories branch from it.

⚠️ **Note:** The root `/` filesystem is different from the `root` user account.

### Most Important Subdirectories

| Directory | Purpose |
|-----------|---------|
| `/root` | Home directory of the root user |
| `/home` | Regular users' home directories |
| `/etc` | System configuration files |
| `/bin` | Essential command binaries |
| `/sbin` | System binaries (for root only) |
| `/lib` | System libraries (like Windows DLLs) |
| `/usr` | User programs and libraries |
| `/var` | Variable data (logs, cache, etc.) |
| `/tmp` | Temporary files |
| `/mnt` | Mount point for filesystems |
| `/media` | Mount point for removable media (CDs, USBs) |
| `/opt` | Optional software packages |

### Filesystem Hierarchy Example

```
/
├── root          (root user's home)
├── home          (regular users)
├── etc           (configuration)
├── bin           (commands)
├── lib           (libraries)
├── var           (logs, data)
└── tmp           (temporary files)
```

---

## 🛡️ Security Best Practice (Root vs Regular User)

### ⚠️ The Warning
**Do not log in as 'root' when performing routine tasks** such as:
- Browsing the web
- Running security tools (Wireshark, Burp Suite, etc.)
- Opening emails or documents
- Installing untrusted software

### Why is This Important?
If your system gets hacked while you are logged in as `root`:
- The attacker immediately gains **root privileges**
- The attacker gains **complete control** of your system
- All your data becomes vulnerable

### For Labs
For practice and training exercises in this course, staying logged in as `root` is **perfectly fine** since this is a controlled learning environment.

### Best Practice: Using `sudo`
```bash
# Instead of logging in as root, use sudo for specific commands:
sudo apt-get update
sudo systemctl restart apache2
```

---

## 📚 Quick Reference

### Useful Keyboard Shortcuts in Terminal

| Shortcut | Function |
|----------|----------|
| `Ctrl + C` | Cancel/stop current command |
| `Ctrl + L` | Clear screen |
| `Ctrl + Z` | Suspend current process |
| `↑ Arrow` | Previous command |
| `↓ Arrow` | Next command |
| `Tab` | Auto-complete filename or command |

### File Permissions Quick Guide

```bash
# View file permissions
ls -l

# Change permissions (read, write, execute)
chmod 755 file.txt

# Change file owner
chown user:group file.txt
```

---

## ✅ Summary

- ✅ Learned how to log into Kali Linux with default credentials
- ✅ Explored the desktop and terminal interface  
- ✅ Understood the Linux filesystem structure and key directories
- ✅ Reviewed security best practices (root vs regular user)
- ✅ Learned essential Linux commands for navigation and file management
- ✅ Understood the importance of using `sudo` instead of logging in as root

---

## 🎯 Practice Exercises

1. **Open Terminal:** Click the terminal icon and open your first shell
2. **Navigate:** Use `cd` to navigate to different directories (/home, /etc, /tmp)
3. **List Files:** Use `ls -la` to see all files including hidden ones
4. **Create Directory:** Use `mkdir` to create a new practice folder
5. **Change Password:** Use `passwd` to set a new password
6. **View Configuration:** Use `cat /etc/os-release` to see system info

---

## 📖 Additional Resources

- [Linux Foundation Documentation](https://www.linux.org/)
- [GNU Bash Manual](https://www.gnu.org/software/bash/manual/)
- [Kali Linux Official Documentation](https://www.kali.org/docs/)
