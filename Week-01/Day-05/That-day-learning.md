# 🐧 Day 05: Linux File & Directory Modification Utilities

Welcome to Day 05 of my Linux Security learning journey. This document summarizes core tools and techniques for creating, copying, renaming, and deleting files and directories in Linux. It focuses on practical usage, common options, and safety notes useful for learners.

---

## 🎯 Key Points & Core Concepts

### 1. 🔍 Creating and Displaying Files with `cat`

- Description: `cat` (short for "concatenate") is commonly used to display file contents and can also be used to create small files by redirecting standard input to a file.
- Example — create/overwrite a file:

```bash
$ cat > hackingskills.txt
Everyone in IT security without hacking skills is in the dark
^D
```
(Press Ctrl-D to send EOF and finish input.)

#### 🖼️ Terminal Output

![Create file with cat](Screenshot/create-file-cat.png)

- Note: `cat > filename` will overwrite an existing file. To append instead of overwrite, use `cat >> filename`:

```bash
$ cat >> hackingskills.txt
Everyone should learn hacking
^D
```

#### 🖼️ Terminal Output (append)

![Append with cat](Screenshot/append-with-cat.png)

- Display the file content:

```bash
$ cat hackingskills.txt
Everyone in IT security without hacking skills is in the dark
Everyone should learn hacking
```

#### 🖼️ Terminal Output (display)

![Display with cat](Screenshot/display-with-cat.png)

- Alternatives for larger or structured input: use a text editor (`vim`, `nano`, `gedit`), `tee` (for piping to a file while still seeing output), or here-documents (`<<EOF ... EOF`).

---

### 2. 📁 File Creation with `touch`

- Description: `touch` updates file timestamps. If the file does not exist, it creates an empty file (0 bytes).
- Useful options:
  - `-c` : do not create any files
  - `-a` : change only the access time
  - `-m` : change only the modification time
  - `-t [[CC]YY]MMDDhhmm[.ss]` : specify the timestamp

Example:

```bash
$ touch newfile.txt
$ ls -l newfile.txt
-rw-r--r-- 1 user user 0 Jun 30 12:00 newfile.txt
```

#### 🖼️ Terminal Output

![File created with touch](Screenshot/file-create-with-touch.png)

---

### 3. 🌐 Creating Directories with `mkdir`

- Description: `mkdir` makes directories.
- Helpful option: `-p` creates parent directories as needed (no error if existing).

Example:

```bash
$ mkdir -p projects/linux-basics
$ cd projects/linux-basics
```

#### 🖼️ Terminal Output

![Make directory with mkdir](Screenshot/make-directory.png)

---

### 4. 💪 Copying Files with `cp`

- Description: `cp` duplicates files or directories.
- Common options:
  - `-r` : recursive (for directories)
  - `-a` : archive (preserve attributes and copy recursively)
  - `-i` : interactive (prompt before overwrite)
  - `-v` : verbose

Example — copy and rename:

```bash
$ touch oldfile.txt
$ cp oldfile.txt /root/newdirectory/newfile.txt
$ ls /root/newdirectory
newfile.txt
```

#### 🖼️ Terminal Output

![Copy with cp](Screenshot/copy-with-cp.png)

---

### 5. 🃏 Moving and Renaming with `mv`

- Description: `mv` moves files/directories or renames them.
- It performs both "move to a new location" and "rename within same directory".
- Useful options: `-i` (interactive), `-n` (no clobber), `-v` (verbose).

Example — rename:

```bash
$ mv newfile.txt newfile2.txt
$ ls
newfile2.txt
```

Example — move to another directory:

```bash
$ mv newfile2.txt ../otherdir/
```

#### 🖼️ Terminal Output

![Rename and move](Screenshot/rename-and-move.png)

---

### 6. 🪠 Removing Files and Directories (`rm`, `rmdir`)

- `rm filename` : removes a file.
- `rmdir dirname` : removes an empty directory only.
- `rm -r dirname` : removes a directory and all its contents recursively.
- `rm -f` : force (do not prompt). Combined as `rm -rf` — very dangerous.

Safety notes:
- Avoid running `rm -rf /` or similar dangerous commands. Modern coreutils protect root with `--preserve-root` by default, but always be careful.
- Prefer interactive deletion when unsure: `rm -ri dirname`.
- Do not run `rm` with `sudo` unless you understand the consequences.

Examples:

```bash
$ rm newfile2.txt
```

#### 🖼️ Process Output

![Remove file](Screenshot/remove-file.png)


```bash
$ rmdir emptydirectory
```

#### 🖼️ Process Output

![Remove directory with rmdir](Screenshot/remove-directory-with-rmdir.png)


```bash
$ rm -r oldproject
```

#### 🖼️ Process Output

![Remove directory with rm -r](Screenshot/remove-directory-with-rm-r.png)

---

## 🛠️ Utilities & Tool Reference

| Category | Component/Tool | Syntax / Structure | Description |
| --- | --- | --- | --- |
| File Creation / Display | `cat` | `cat [file]` / `cat > [filename]` / `cat >> [filename]` | Display file contents; redirect stdin to create/append small files. |
| File Creation (empty) | `touch` | `touch [filename]` | Create an empty file or update timestamps. |
| Folder Creation | `mkdir` | `mkdir [-p] [directory_name]` | Create a new directory; `-p` creates parents as needed. |
| Copy File/Directory | `cp` | `cp [options] [source] [destination]` | Copy files or directories. Use `-r` or `-a` for directories. |
| Move / Rename | `mv` | `mv [source] [destination]` | Move or rename files and directories. |
| Safe Deletion | `rmdir` | `rmdir [directory_name]` | Remove empty directories only. |
| Recursive / Force Deletion | `rm` | `rm -r [directory_name]` / `rm -rf [path]` | Delete directory and contents (use with extreme caution). |

---
