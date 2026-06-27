# 📖 Learning Linux Basics for Security

## 🖥️ A Tour of Kali Linux
-> Login Screen: When you start Kali, you will see a login screen (**Logging-into-Kali**).  
![Logging into Kali]([Logging-into-Kali.png](https://github.com/sourovchandradas/linux-basic-for-security/blob/main/Week-01/Day-02/A%20tour%20of%20Kali/Logging-into-Kali.png))  

-> Default Credentials:  
   - Username: root  
   - Password: toor (use your new password if you changed it earlier).  

-> After Login: You will get access to the Kali desktop (**The-Kali-Desktop**).  
![The Kali Desktop](The-Kali-Desktop.png) 

-> Key Aspects to Explore: The Terminal Interface and File Structure.  

---

## 💻 The Terminal (Command Line Interface)
-> How to Open: Click the terminal icon along the left of the Kali desktop (**The-Kali-Terminal**).  
![The Kali Terminal](The-Kali-Terminal.png)  

-> The Shell: The terminal opens the command line environment known as the shell. It enables you to run commands on the underlying operating systems and write scripts.  

-> Default Shell: Bash (Bourne-Again SHell) is the most popular and the default shell in Kali and many other Linux distributions.  

-> Password Command: To change your password, use the command:  
```bash
passwd
Output : ![Figure 1-4: default Password Change](default-Password-Change.png) 
📂 The Linux Filesystem
-> Structure Concept: Unlike Windows, Linux does not have physical drives (like C: drive) at the base. It uses a logical filesystem instead.

-> The Root (/): At the very top of the structure is "/" (the root of the filesystem), which looks like an upside-down tree (Filesystem-Root).
[Looks like the result wasn't safe to show. Let's switch things up and try something else!]  
Figure 1-4: The Linux Filesystem

-> Note: The root (/) filesystem is different from the root user.

-> Most Important Subdirectories (First-level Directories):

/root   : The home directory of the all-powerful root user.

/etc    : Contains Linux configuration files (controls when and how programs start up).

/home   : The regular user's home directory.

/mnt    : Where other filesystems are attached or mounted.

/media  : Where CDs and USB devices are usually attached or mounted.

/bin    : Where application binaries (equivalent to executables/.exe in Windows) reside.

/lib    : Contains libraries (shared programs similar to Windows DLLs).

🛡️ Security Best Practice (Root vs Regular User)
-> The Warning: Do not log in as 'root' when performing routine tasks (browsing the web, running tools like Wireshark, etc.).

-> Why? If your system gets hacked while you are logged in as root, the hacker immediately gains root privileges and completely "owns" your system.

-> For Labs: For the practice and training exercises in this book, staying logged in as root is perfectly fine.

📌 Summary
-> Learned how to log into Kali Linux.
-> Explored the desktop and terminal interface.
-> Understood the Linux filesystem structure.
-> Reviewed security best practices (root vs regular user).
