# 🐬 Day 26 : Extracting Information from MySQL / MariaDB

Welcome to Day 26 of my Linux Security learning journey. This document covers background mechanics of MySQL and MariaDB architecture, service control, root account security, interactive SQL execution, and step-by-step database enumeration and data extraction techniques for penetration testing and system administration.

---

## 🎯 Key Points & Core Concepts

### 1. ⚙️ Introduction & Importance of MySQL / MariaDB

* **MySQL:** The world's most widely deployed relational database management system (RDBMS) backing dynamic web application infrastructure.
* **MariaDB:** An open-source, community-driven drop-in replacement fork of MySQL created by its original developers under the GNU GPL license.
* **Database-Driven Web Applications:** Modern sites (e.g., WordPress, Facebook, LinkedIn, X/Twitter, Wikipedia, YouTube) rely on database backends to store and query stateful data.
* **High-Value Target:** Databases store high-value targets, including cleartext/hashed user credentials, session tokens, personally identifiable information (PII), and financial data (e.g., credit card numbers).

---

### 2. 🏛️ Evolution & Background

* **1995:** Created by MySQL AB in Sweden under the GPL license.
* **2008:** Acquired by Sun Microsystems.
* **2009:** Oracle Corporation acquired Sun Microsystems, gaining ownership of MySQL.
* **The MariaDB Fork:** Concerns regarding Oracle's enterprise stewardship prompted original developers to create **MariaDB**, pledging to keep it completely open-source. Most Linux distributions (including Kali Linux) default to MariaDB.

---

### 3. 🔑 Service Control & Authentication

#### Installation & Service Control

MySQL/MariaDB comes pre-installed on Kali Linux. On other Linux distributions, it can be installed via the default package manager or directly from official downloads.

To launch the background database service:

```bash
kali > sudo systemctl start mysql

```

#### Authentication Mechanics

Logging in as the `root` administrative user:

```bash
kali > sudo mysql -u root -p

```

> 📌 **Critical Security Vulnerability:** In default fresh installations, the database `root` password is blank. Operating system credentials and database credentials are completely separate. Setting a strong administrative database password immediately after installation is mandatory.

---

### 4. 🛠️ Interacting with SQL (Structured Query Language)

SQL is an interpreted query language used to interface with relational databases structured into tables (rows and columns).

#### Core SQL Command Reference

| Command | Primary Action / Purpose | Syntax Example |
| --- | --- | --- |
| **`SELECT`** | Retrieves records from target tables. | `SELECT user, password FROM customers;` |
| **`UNION`** | Combines result sets from multiple `SELECT` queries. | `SELECT user FROM admins UNION SELECT user FROM customers;` |
| **`INSERT`** | Adds new data records into a table. | `INSERT INTO users (user, pass) VALUES ('admin', 'pass123');` |
| **`UPDATE`** | Modifies existing table fields. | `UPDATE users SET pass='newpass' WHERE user='admin';` |
| **`DELETE`** | Removes specified records from a table. | `DELETE FROM users WHERE user='test';` |

#### Conditional Targeting Example:

```sql
SELECT user, password FROM customers WHERE user='admin';

```

*Retrieves the `user` and `password` columns from the `customers` table specifically where the `user` field equals `'admin'`.*

---

### 5. 🔒 Securing the Root Account

#### Step-by-Step Root Password Setup

1. **Launch Database Monitor & Switch to System Database:**
```sql
MariaDB [(none)]> USE mysql;

```


2. **Inspect Current Account Hashes:**
```sql
MariaDB [mysql]> SELECT user, host, password FROM mysql.user;

```


*(Notice that the `root` user password field is empty)*
3. **Configure New Administrative Password:**
```sql
MariaDB [mysql]> SET PASSWORD FOR 'root'@'localhost' = PASSWORD('hackers-arise');

```


4. **Verify Password Hash Generation:**
```sql
MariaDB [mysql]> SELECT user, host, password FROM mysql.user;

```


*MySQL/MariaDB generates a one-way cryptographic hash for the newly defined password.*

---

### 6. 🗺️ Database Enumeration & Extraction Architecture

When interacting with a local or remote MySQL/MariaDB server via CLI, every command must terminate with a semicolon (`;`) or `\g`.

```
+------------------------+     TCP Port 3306     +---------------------------------+
|   Kali Linux Machine   | --------------------> |    Target Database Server       |
|  (Attacker / Client)   |                       |    IP: 192.168.1.101             |
+------------------------+                       +---------------------------------+
                                                                  |
                                                                  v
                                                       +--------------------+
                                                       | MySQL / MariaDB    |
                                                       | (Enumeration Flow) |
                                                       +--------------------+

```

1. **Establish Connection:** Connect locally or across the network.
* Local Host Connection:

```bash
kali > mysql -u root -p

```

* Remote Server Connection:

```bash
kali > mysql -u root -p 192.168.1.101

```


2. **Enumerate Databases:** Identify target databases.
List all databases hosted on the active instance:

```sql
MariaDB [(none)]> SHOW DATABASES;

```

*Default system databases include `information_schema`, `mysql`, `performance_schema`, and `sys`.*


3. **Select Target Database:** Set working database context.
Switch context to a target database (e.g., `creditcardnumbers`):

```sql
MariaDB [(none)]> USE creditcardnumbers;

```

*Output:* `Database changed`


4. **List & Describe Tables:** Inspect database schema.
Display available tables in the active database:

```sql
MariaDB [creditcardnumbers]> SHOW TABLES;

```

Inspect column structure, field names, and data types:

```sql
MariaDB [creditcardnumbers]> DESCRIBE cardnumbers;

```


5. **Extract Data from Table:** Fetch sensitive records.
Extract specific target columns:

```sql
MariaDB [creditcardnumbers]> SELECT customers, cc FROM cardnumbers;

```

Dump all table columns simultaneously using the wildcard (`*`):

```sql
MariaDB [creditcardnumbers]> SELECT * FROM cardnumbers;

```


#### 🖼️ Terminal Output View (`SELECT * FROM cardnumbers;`)

```plaintext
+-----------+----------------+-------------+-------+----------+
| customers | address        | city        | state | cc       |
+-----------+----------------+-------------+-------+----------+
| Jones     | 1 Wall St      | NY          | NY    | 12345678 |
| Sawyer    | 12 Piccadilly  | London      | UK    | 23456789 |
| Doe       | 25 Front St    | Los Angeles | CA    | 45678987 |
+-----------+----------------+-------------+-------+----------+

```

---

## 🛠️ Utilities & Command Reference

| Utility / Command | Syntax Example | Primary Purpose / Description |
| --- | --- | --- |
| **`systemctl`** | `sudo systemctl start mysql` | Starts the MySQL / MariaDB daemon. |
| **`mysql` CLI** | `mysql -u root -p 192.168.1.101` | Connects to a local or remote MySQL/MariaDB database server. |
| **`SHOW DATABASES;`** | `SHOW DATABASES;` | Lists available databases on the instance. |
| **`USE`** | `USE creditcardnumbers;` | Selects a specific database for active context. |
| **`SHOW TABLES;`** | `SHOW TABLES;` | Displays tables contained within the active database. |
| **`DESCRIBE`** | `DESCRIBE cardnumbers;` | Shows table column properties and data types. |
| **`SELECT`** | `SELECT * FROM cardnumbers;` | Queries and outputs records from target tables. |

---

## 🔑 Key Takeaways for Revision

1. **Default Security Vulnerability:** MySQL/MariaDB `root` account has a blank password by default.
2. **OS vs. Database Passwords:** Operating system user credentials are completely separate from MySQL user accounts.
3. **Query Suffix Requirement:** Statements in the MariaDB/MySQL prompt must end with `;` or `\g`.
4. **Data Extraction Core Sequence:**

$$\text{SHOW DATABASES;} \longrightarrow \text{USE } <db> \longrightarrow \text{SHOW TABLES;} \longrightarrow \text{DESCRIBE } <table> \longrightarrow \text{SELECT } * \text{ FROM } <table>;$$



---
