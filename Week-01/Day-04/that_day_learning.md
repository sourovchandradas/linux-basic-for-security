# Day 04: Linux File Searching & Filtering Utilities

This note covers practical Linux commands for finding files, locating executables, and filtering large outputs. These tools are especially useful when you need to identify installed software, trace configuration files, or inspect running processes in a controlled and authorized environment.

> **Learning objective:** Be able to explain what each command does, choose the right search method, and verify the result before acting on it.

## Table of Contents
* [Search files with `locate`](#search-files-with-locate)
* [Find binaries with `whereis`](#find-binaries-with-whereis)
* [Find binaries in `PATH` with `which`](#find-binaries-in-path-with-which)
* [Search in real time with `find`](#search-in-real-time-with-find)
* [Use wildcards to broaden matches](#use-wildcards-to-broaden-matches)
* [Filter output with `grep` and piping](#filter-output-with-grep-and-piping)
* [Hands-on practice](#hands-on-practice)
* [Quick Reference](#quick-reference)
* [Related Topics](#related-topics)
* [Additional Resources](#additional-resources)
* [Key Takeaways](#key-takeaways)

---

## Search files with `locate`

`locate` searches a pre-built database of filesystem paths and is usually much faster than scanning the live filesystem. It is helpful when you want a quick answer to, "Where is this file or command installed?"

Important limitation: the database is normally updated periodically, so newly created files may not appear immediately.

```bash
locate aircrack-ng
```

* **Labs:** [locate](labs/locate)

**Why it matters:**
* Very fast for broad searches.
* Good for general file lookup.
* Not ideal for very recent files because it depends on the database update cycle.

---

## Find binaries with `whereis`

`whereis` is useful for locating the binary, source code, and manual page related to a command. It is often more precise than a broad search when you already know the program name.

```bash
whereis aircrack-ng
```

* **Labs:** [whereis](labs/whereis)

**What it returns:**
* the executable path
* the source path (if installed)
* the man page location

---

## Find binaries in `PATH` with `which`

`which` checks only the directories listed in the `PATH` environment variable. It shows the exact executable the shell would run when you type a command name.

```bash
which aircrack-ng
```

* **Labs:** [which](labs/which)

**What is `PATH`?**
`PATH` is an environment variable containing a list of directories such as `/usr/bin` and `/usr/sbin`. When you type a command, the shell searches those directories in order.

---

## Search in real time with `find`

`find` is the most powerful and flexible search utility in Linux. Unlike `locate`, it searches the filesystem live and can filter by file type, size, time, permissions, and name.

### Syntax

```bash
find [directory] [options] [expression]
```

### Understanding the syntax

1. **`[directory]`**: where the search starts, such as `/`, `/etc`, or `.`.
2. **`[options]`**: criteria such as `-type f` for files or `-type d` for directories.
3. **`[expression]`**: the name or pattern to match.

### Practical examples

#### Example 01: Search from the root directory

```bash
find / -type f -name apache2
```

* **Labs:** [find](labs/find/example-01)

This is a broad search and can be slow because it checks the whole filesystem.

#### Example 02: Search only in `/etc`

```bash
find /etc -type f -name apache2
```

* **Labs:** [find](labs/find/example-02)

This is usually faster and more focused than scanning `/`.


> **Best practice:** Start with a narrow directory whenever possible to reduce search time and output clutter.
>
> 

---

## Use wildcards to broaden matches

Wildcards help when you are unsure of the exact file name. They match patterns instead of literal characters.

* `?` matches exactly one character.
  * Example: `?at` matches `cat`, `hat`, `bat`
* `[]` matches any character inside the brackets.
  * Example: `[c,b]at` matches `cat` or `bat`
* `*` matches any number of characters.

```bash
find /etc -type f -name 'apache2.*'
```

This pattern matches files such as `apache2.conf` or `apache2.log`.

* **Labs:** [wildcards](labs/wildcards)

---

## Filter output with `grep` and piping

The pipe symbol `|` sends the output of one command into another command. This makes Linux very powerful for filtering and inspecting relevant data.

`grep` searches text for a keyword and shows only matching lines.

### Example: List active processes and filter for a service

```bash
ps aux | grep apache2
```

* **Labs:** [grep](labs/grep)

This command:
1. shows all running processes with `ps aux`
2. sends the output into `grep`
3. filters the result to lines containing `apache2`

**Why this matters:** You can quickly confirm whether a service is running without manually scanning a long process list.

---

## Hands-on practice

Practice in your own home directory with a small, harmless example.

```bash
cd ~
mkdir -p linux-practice/day04
cd linux-practice/day04
pwd
touch notes.txt
ls -lah
find . -type f
ps aux | grep bash
```

### Check your understanding

* Which command is better for a fast broad search: `locate` or `find`?
* What is the difference between `which` and `whereis`?
* When would you use `grep` with a pipe?
* Why is `find /` usually slower than `find /etc`?

---

## Quick Reference

| Command | Example | Purpose |
|---|---|---|
| `locate` | `locate aircrack-ng` | Search a prebuilt file database. |
| `whereis` | `whereis aircrack-ng` | Show executable, source, and man page locations. |
| `which` | `which aircrack-ng` | Show the executable in `PATH`. |
| `find` | `find /etc -type f -name apache2` | Do a live filesystem search by criteria. |
| `grep` | `ps aux | grep apache2` | Filter output for a keyword. |
| `|` | `[command] | grep [keyword]` | Pipe output from one command to another. |

---

## Related Topics
* [Day-03](../Day-03/that_day_learning.md)
* [Day-05](../Day-05/that_day_learning.md)
* [Day-06](../Day-06/that_day_learning.md)

---

## Additional Resources
* [Linux Basics For Hackers](https://books.google.com.bd/books?id=HkFYswEACAAJ&printsec=copyright&redir_esc=y#v=onepage&q&f=false)

---

## Key Takeaways

1. Use `locate` for a fast, database-backed search when freshness is not critical.
2. Use `whereis` to find an executable and its related documentation paths.
3. Use `which` to confirm which executable your shell will actually run.
4. Use `find` when you need precise, real-time control over the search.
5. Use wildcards to match uncertain file names.
6. Use `grep` and pipes to filter large output into only the relevant lines.
7. Always verify results before relying on command output in a security or system context.

---

*Last Modified : November 7, 2026*
