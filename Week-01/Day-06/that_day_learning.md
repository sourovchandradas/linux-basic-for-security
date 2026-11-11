# Day 06: Linux Text Manipulation Utilities

This note covers the key Linux text-processing tools used to inspect, filter, and reference file contents: `cat`, `head`, `tail`, and `nl`. The examples focus on practical system administration and security workflows, especially when reviewing configuration files or logs. Always work only on systems you own or are explicitly authorized to inspect.

> **Learning objective:** Be able to explain what each command does, choose the right tool for viewing all or part of a file, and use line numbers when referencing a configuration or log entry.

## Table of Contents
* [Display a file with `cat`](#display-a-file-with-cat)
* [View the top of a file with `head`](#view-the-top-of-a-file-with-head)
* [View the bottom of a file with `tail`](#view-the-bottom-of-a-file-with-tail)
* [Number lines with `nl`](#number-lines-with-nl)
* [Hands-on practice](#hands-on-practice)
* [Quick Reference](#quick-reference)
* [Related Topics](#related-topics)
* [Key Takeaways](#key-takeaways)

---

## Display a file with `cat`

`cat` is used to concatenate and display file contents. It reads the file and prints it to the terminal from start to finish. This is useful for short files, but for larger files the output may scroll quickly and become hard to scan.

```bash
cat /etc/snort/snort.lua
```
* **Labs:** [display](labs/display)

`cat` is most practical when you want to read the whole content of a relatively small file. For large configuration files or logs, it is often better to view only the beginning or end with `head` or `tail`.

**Security note:** In a real environment, always confirm that the file path is correct and that you are authorized to read it before inspecting system configuration.

---

## View the top of a file with `head`

`head` displays the first lines of a file. By default, it shows the first **10 lines**. This is useful when you want to inspect the header, configuration comments, or the beginning of a log file without reading everything.

#### By default
```bash
head /etc/snort/snort.lua
```
* **Labs:** [head](labs/head/by_default)

#### User defined
```bash
head -n 20 /etc/snort/snort.lua
```
* **Labs:** [head](labs/head/user_defined)

The `-n` option lets you choose how many lines to display. In practical use, `head` is often the fastest way to confirm file structure or look for version information and early settings.

---

## View the bottom of a file with `tail`

`tail` displays the last lines of a file. By default, it shows the last **10 lines**. This is particularly useful for checking the end of a log file or the trailing section of a configuration file where included rules or additional definitions may appear.

#### By default
```bash
tail /etc/snort/snort.lua
```
* **Labs:** [tail](labs/tail/by_default)
#### User defind
```bash
tail -n 25 /etc/snort/snort.lua
```
* **Labs:** [tail](labs/tail/user_defined)

When analyzing logs, `tail` is often used to monitor recent activity. A common pattern is `tail -f` to follow a file as new lines are written, which is especially helpful for troubleshooting services in real time.

---

## Number lines with `nl`

`nl` adds line numbers to the output of a file. This makes it easier to reference exact lines during review, testing, troubleshooting, or documentation.

#### Without empty line
```bash
nl /etc/snort/snort.lua
```
* **Labs:** [nl](labs/nl/without_empty_line)

#### With empty line
```bash
nl -ba /etc/snort/snort.lua
```
* **Labs:** [nl](labs/nl/with_empty_line)


By default, `nl` numbers non-empty lines. The `-ba` option numbers all lines, including blank ones, which is useful when you need precise references in a script or config file.

---

## Hands-on practice

Create a small sample file in your home directory and practice the four commands on it. This keeps the activity safe and easy to understand.

```bash
mkdir -p ~/linux-practice/day06
cat > ~/linux-practice/day06/sample.txt <<'EOF'
Linux text utilities
cat displays file contents.
head reads from the beginning.

tail reads from the end.
nl adds line numbers.
EOF

cat ~/linux-practice/day06/sample.txt
head -n 3 ~/linux-practice/day06/sample.txt
tail -n 3 ~/linux-practice/day06/sample.txt
nl ~/linux-practice/day06/sample.txt
nl -ba ~/linux-practice/day06/sample.txt
```

**Check your understanding:** Which command shows all contents, which one shows the first lines, and which one shows the final lines? What is the difference between `nl` and `nl -ba` when a file contains blank lines?

---

## Quick Reference

| Command | Example | Purpose |
|---|---|---|
| `cat` | `cat file.txt` | Display the complete contents of a file. |
| `head` | `head file.txt` | Show the first 10 lines by default. |
| `head -n N` | `head -n 20 file.txt` | Show the first `N` lines. |
| `tail` | `tail file.txt` | Show the last 10 lines by default. |
| `tail -n N` | `tail -n 25 file.txt` | Show the last `N` lines. |
| `nl` | `nl file.txt` | Print output with line numbers. |
| `nl -ba` | `nl -ba file.txt` | Number all lines, including blank lines. |

---

## Related Topics
* [Day-04](../Day-04/that_day_learning.md)
* [Day-05](../Day-05/that_day_learning.md)
* [Day-07](../Day-07/that_day_learning.md)

---

## Key Takeaways

1. Use `cat` to display the full contents of a file when the file is short or manageable.
2. Use `head` to inspect the beginning of a file without reading everything.
3. Use `tail` to inspect the end of a file or the newest entries in a log.
4. Use `nl` to assign line numbers when you need a precise reference for troubleshooting or documentation.
5. Use `tail -f` carefully for live log monitoring and stop it with `Ctrl+C` when you are done.
6. Always operate on authorized systems and verify paths before reading sensitive configuration files.

---

*Last Modified : November 10, 2026*
