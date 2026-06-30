========================================================================
                      LINUX COMMANDS: FINDING STUFF
========================================================================
Topic: Linux File & Process Searching (Day 04)
Source: Chapter 1 - Getting Started with the Basics
========================================================================

## 1. Searching with 'locate'
------------------------------------------------------------------------
* Description: Scans entire filesystem for a keyword. Very fast but uses a database updated once a day (new files won't show up).
* Example:
  kali > locate aircrack-ng

![Locate](Screenshots/locate.png)


## 2. Finding Binaries with 'whereis'
------------------------------------------------------------------------
* Description: Finds binary, source, and man page locations.
* Example:
  kali > whereis aircrack-ng

![Whereis](Screenshots/whereis.png)


## 3. Finding Binaries in PATH with 'which'
------------------------------------------------------------------------
* Description: Only searches in directories listed in the $PATH variable.
* Example:
  kali > which aircrack-ng

![Which](Screenshots/which.png)


## 4. Powerful Searches with 'find'
------------------------------------------------------------------------
* Description: Most powerful search tool. Can look by name, date, size, permissions, etc. Only shows exact matches.
* Syntax: find [directory] [options] [expression]

### 4.1 Understanding Syntax:
1. [directory]: Where to start searching (e.g., / for root, /etc for config folder, . for current folder).
2. [options]: Search criteria (e.g., -type f for files, -type d for folders).
3. [expression]: What name to match (e.g., -name apache2).

* Example 1 (Search from Root - Slow):
  kali > find / -type f -name apache2

![Find Root](Screenshots/find-with-root.png)

* Example 2 (Search in /etc - Faster):
  kali > find /etc -type f -name apache2

![Find Config Folder](Screenshots/find-with-configuration-file.png)


## 5. A Quick Look at Wildcards
------------------------------------------------------------------------
* '?' : Matches 1 character (e.g., ?at finds cat, hat).
* '[]' : Matches characters inside brackets (e.g., [c,b]at finds cat, bat).
* '*' : Matches any characters of any length.
* Example with find:
  kali > find /etc -type f -name apache2.\*

![Find Wildcard](Screenshots/find-with-main-apache-config.png)


## 6. Filtering with 'grep' and Piping (|)
------------------------------------------------------------------------
* Piping (|): Sends output of one command as input to another.
* grep: Filters text for specific keywords.
* Example (List all processes and filter for apache2):
  kali > ps aux | grep apache2

![PS Aux](Screenshots/ps-aux.png)
![Grep](Screenshots/grep.png)

========================================================================
                          End of Day 04 Notes
========================================================================
