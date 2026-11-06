# Day 03: Linux Basics & Core Commands

This note covers foundational Linux commands for navigating the filesystem, identifying the current user, inspecting directory contents, and finding command documentation. The examples use a Kali Linux shell, but these commands are available on most Linux distributions.

> **Learning objective:** Be able to explain what each command does, choose an appropriate option, and verify the result in an authorized local environment.

## Table of Contents
* [Check the current directory with `pwd`](#check-the-current-directory-with-pwd)
* [Identify the current user with `whoami`](#identify-the-current-user-with-whoami)
* [Navigate directories with `cd`](#navigate-directories-with-cd)
* [Inspect directory contents with `ls`](#inspect-directory-contents-with-ls)
* [Find command options with `--help`](#find-command-options-with---help)
* [Read manual pages with `man`](#read-manual-pages-with-man)
* [Hands-on practice](#hands-on-practice)
* [Quick Reference](#quick-reference)
* [Related Topics](#related-topics)
* [Additional Resources](#additional-resources)
* [Key Takeaways](#key-takeaways)

---

## Check the current directory with `pwd`

`pwd` prints the absolute path of the current working directory. Knowing where you are helps prevent running commands against the wrong files or folders.

```bash
pwd
```
* **Labs:** [pwd](labs/pwd)

---

## Identify the current user with `whoami`

`whoami` prints the username associated with the current effective user ID. It is useful to confirm which account a shell is using. It does **not** show every group or permission; use `id` for that information.

```bash
whoami
```

Most everyday commands can run as a regular user. Some administrative actions or particular operations may require elevated privileges; requirements depend on the command and its options. Use elevated privileges only when needed and authorized.

* **Labs:** [whoami](labs/whoami)

---

## Navigate directories with `cd`

`cd` is a shell built-in that changes the current working directory.

| Command | Meaning |
|---|---|
| `cd /etc` | Go to the absolute path `/etc`. |
| `cd ..` | Go to the parent of the current directory. |
| `cd ../..` | Go up two directory levels. |
| `cd ~` | Go to the current user's home directory. |
| `cd -` | Return to the previous working directory. |

An **absolute path** starts from `/`; a **relative path** is interpreted from the current directory.

```bash
cd /etc
pwd
cd ..
pwd
```

* **Labs:** [cd](labs/cd)

---

## Inspect directory contents with `ls`

`ls` lists directory entries. With no path argument, it lists the current directory; with a path, it lists that directory.

| Option | Meaning |
|---|---|
| `-l` | Use a long format showing permissions, link count, owner, group, size, and modification time. |
| `-a` | Include hidden entries whose names begin with `.` (including `.` and `..`). |
| `-h` | With `-l`, display file sizes in a human-readable format. |

Combine options as `ls -lah` (or `ls -alh`). In a long listing, the displayed timestamp is generally the **last modification time**, not the file's creation time. File metadata and available timestamps can vary by filesystem.

```bash
ls
ls -l
ls -h
ls -a
ls -la
ls -lh
ls -ah
ls -lha
```

### Explanation
* `ls -l`    : Shows list with **long**.
* `ls -h`    : Shows list with **human readable format**.
* `ls -a`    : Shows list with **hidden file**.
* `ls -la`   : Shows list with **long and hidden**.
* `ls -lh`   : Shows list with **long and human readable format**.
* `ls -ah`   : Shows list with **hidden and human readable format**.
* `ls -lha`  : Show list with **long, human readable format and hidden**.

* **Labs:** [ls](labs/ls)

---

## Find command options with `--help`

Many command-line programs provide a brief usage summary with `--help`; some use `-h`, and behavior is program-specific. Check the command's own help output rather than assuming every program follows the same convention.

```bash
ls --help
```

`ls --help` displays a summary of the options supported by `ls`.

* **Labs:** [--help](labs/--help)

---

## Read manual pages with `man`

`man` opens the installed manual page for a command. Manual pages provide more detail than a short help summary; availability depends on the package and system configuration.

```bash
man ls
```

Useful navigation keys in the pager:

- `Space` or `Page Down`: move forward one page.
- `b` or `Page Up`: move backward one page.
- `/term` then `Enter`: search for `term`; press `n` for the next match.
- `q`: quit the manual page.

* **Labs:** [man](labs/man)

---

## Hands-on practice

Practice in your own home directory. These commands create a small, harmless example folder and file, then inspect them. The `mkdir` and `touch` commands are included here to make the navigation and listing exercise reproducible.

```bash
cd ~
mkdir -p linux-practice/day03
cd linux-practice/day03
pwd
touch notes.txt
ls -lah
cd ..
pwd
cd -
pwd
```

**Check your understanding:** What changes in the output between `ls`, `ls -l`, and `ls -la`? Which command confirms your current location, and which one confirms your username?

---

## Quick Reference

| Command | Example | Purpose |
|---|---|---|
| `pwd` | `pwd` | Print the current working directory. |
| `whoami` | `whoami` | Print the effective username. |
| `id` | `id` | Show user and group IDs. |
| `cd` | `cd /etc` | Change the current working directory. |
| `ls` | `ls -lah` | List entries, including hidden entries, with detailed information. |
| `--help` | `ls --help` | Show a program's brief help, where supported. |
| `man` | `man ls` | Open the installed manual page. |

---

## Related Topics
* [Day-04](../Day-04/that_day_learning.md)
* [Day-05](../Day-05/that_day_learning.md)
* [Day-06](../Day-06/that_day_learning.md)

---

## Additional Resources
* [Linux Basics For Hackers](https://books.google.com.bd/books?id=HkFYswEACAAJ&printsec=copyright&redir_esc=y#v=onepage&q&f=false)

---

## Key Takeaways

1. Use `pwd` to verify the current working directory before acting on files.
2. Use `whoami` to identify the current effective username; use `id` to inspect user and group IDs.
3. Understand whether a path is absolute or relative before using `cd`.
4. Use `ls -lah` to inspect hidden entries and common file metadata; its timestamp is not generally a creation time.
5. Consult the program's own `--help` output or manual page, since options vary between programs.
6. Use security tools only within an explicitly authorized scope.

---

*Last Modified : November 6, 2026*
