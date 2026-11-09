# Day 05: Linux File & Directory Modification Utilities

This note covers common Linux commands for creating, copying, renaming, and removing files and directories. The examples use a Kali Linux shell, but these commands are available on most Linux distributions.

> **Learning objective:** Be able to choose the right command for a basic file operation, understand whether it overwrites or preserves existing data, and verify the result in a safe practice directory.

## Table of Contents
* [Create and display files with `cat`](#create-and-display-files-with-cat)
* [Create files with `touch`](#create-files-with-touch)
* [Create directories with `mkdir`](#create-directories-with-mkdir)
* [Copy files with `cp`](#copy-files-with-cp)
* [Move and rename with `mv`](#move-and-rename-with-mv)
* [Remove files and directories](#remove-files-and-directories)
* [Hands-on practice](#hands-on-practice)
* [Quick Reference](#quick-reference)
* [Related Topics](#related-topics)
* [Additional Resources](#additional-resources)
* [Key Takeaways](#key-takeaways)

---

## Create and display files with `cat`

`cat` (concatenate) displays or combines file contents. With shell redirection, it can also create a small text file. `cat > filename` reads text from the terminal until you press `Ctrl-D` (end-of-file). If the file already exists, `>` replaces its contents. Use `>>` to append instead.

For larger files, use a text editor such as `vim`, `emacs`, `gedit`, or `kate`.

```bash
cat > LinuxSkills
My goal is to become a cybersecurity engineer.
# Press Ctrl-D to finish input.

cat LinuxSkills

cat >> LinuxSkills
Linux plays a very important role in my Linux learning journey.
# Press Ctrl-D to finish input.

cat LinuxSkills
```
* **Labs:** [cat](labs/cat/create_and_display)


The `>` and `>>` operators are shell redirections; `cat` itself is not entering a special file-editing mode. Be careful with `>` because it truncates an existing file immediately.

To demonstrate overwriting, the original example redirects new input to the same file with `>`:

```bash
cat > LinuxSkills
Without hacking skills, everyone in IT security is in the dark.
# Press Ctrl-D to finish input.
cat LinuxSkills
```

* **Labs:** [cat](labs/cat/overwrite)

---

## Create files with `touch`

`touch` creates an empty file if it does not exist. If the file already exists, it updates its access and modification timestamps rather than clearing its contents. A newly created empty file has a size of 0 bytes.

```bash
ls
touch newfile
ls
ls -l newfile
```

* **Labs:** [touch](labs/touch)

---

## Create directories with `mkdir`

`mkdir` (make directory) creates a new directory. Use `cd` to move into it. By default, the parent directory must already exist; `mkdir -p` can create missing parent directories as well.

```bash
mkdir newdirectory
cd newdirectory
pwd
```

* **Labs:** [mkdir](labs/mkdir)

---

## Copy files with `cp`

`cp` creates a copy at the destination and leaves the original in place. The destination can be a directory or a new file path; providing a new filename copies and renames the file at the same time.

* **Syntax**
```bash
cp [source_file] [destination_path]
```

For example, to copy `oldfile` into `newdirectory` under the name `newfile`:

```bash
touch oldfile
cp oldfile newdirectory/newfile
ls -l oldfile newdirectory/newfile
```

* **Labs:** [cp](labs/cp)

---

## Move and rename with `mv`

`mv` moves a file or directory to another location. When the source and destination are in the same directory, it effectively renames the item. If the destination already exists, it may be replaced; check the destination carefully before running the command.

* **Syntax**
```bash
mv [source] [destination]
```

```bash
mv newfile newfile2
ls
```

* **Labs:** [mv](labs/mv)

---

## Remove files and directories

- `rm filename` removes a file. It generally does not move the file to a trash/recycle bin.
- `rmdir dirname` removes a directory only when it is empty.
- `rm -r dirname` removes a directory and its contents recursively. `-r` means **recursive**, not force.

Deletion can be difficult or impossible to undo. Verify the path and its contents before removing anything, especially before using `rm -r`. Avoid broad paths and never run a recursive deletion command on your home directory unless you intentionally mean to remove its contents.

Remove a file:

```bash
rm newfile2
```

* **Labs:** [remove files and directories](labs/remove_files_and_directories/rm)

Trying to remove a non-empty directory with `rmdir` fails and leaves its contents in place:

```bash
rmdir newdirectory
```

* **Labs:** [remove files and directories](labs/remove_files_and_directories/rmdir)

After checking that `newdirectory` is the intended directory, remove it and its contents recursively:

```bash
rm -r newdirectory
```

* **Labs:** [remove files and directories](labs/remove_files_and_directories/rm_-r)

---

## Hands-on practice

Practice in a dedicated folder in your home directory. The cleanup command below targets only the example folder created by this exercise; inspect the path before running it.

```bash
cd ~
mkdir -p linux-practice/day05
cd linux-practice/day05

cat > notes.txt
These are practice notes.
# Press Ctrl-D to finish input.
cat notes.txt

touch empty.txt
mkdir copies
cp notes.txt copies/notes-copy.txt
mv empty.txt renamed-empty.txt
ls -lah

cd ~
# Remove only this practice folder after confirming the path and contents:
ls -lah linux-practice/day05
rm -r linux-practice/day05
```

**Check your understanding:** What is the difference between `>` and `>>`? How does `cp` differ from `mv`? Why will `rmdir` refuse to remove a non-empty directory?

---

## Quick Reference

| Command | Example | Purpose |
|---|---|---|
| `cat >` | `cat > notes.txt` | Create or replace a file from terminal input. |
| `cat >>` | `cat >> notes.txt` | Append terminal input to a file. |
| `cat` | `cat notes.txt` | Display file contents. |
| `touch` | `touch empty.txt` | Create an empty file, or update timestamps on an existing file. |
| `mkdir` | `mkdir practice` | Create a directory. |
| `cp` | `cp notes.txt copy.txt` | Copy a file, keeping the original. |
| `mv` | `mv old.txt new.txt` | Move or rename a file or directory. |
| `rm` | `rm notes.txt` | Remove a file. |
| `rmdir` | `rmdir empty-directory` | Remove an empty directory only. |
| `rm -r` | `rm -r practice` | Remove a directory and its contents recursively; use with care. |

---

## Related Topics
* [Day-03](../Day-03/that_day_learning.md)
* [Day-04](../Day-04/that_day_learning.md)
* [Day-06](../Day-06/that_day_learning.md)

---

## Additional Resources
* [Linux Basics For Hackers](https://books.google.com.bd/books?id=HkFYswEACAAJ&printsec=copyright&redir_esc=y#v=onepage&q&f=false)

---

## Key Takeaways

1. `cat >` creates or replaces file contents, while `cat >>` appends; `cat filename` displays a file.
2. `touch` creates an empty file if needed, but updates timestamps when the file already exists.
3. `cp` preserves the original, whereas `mv` moves or renames the source.
4. `rmdir` only removes empty directories; `rm -r` removes contents recursively.
5. Check the target path before deleting, and use recursive deletion only on a directory you have verified.

---

*Last Modified : November 8, 2026*
