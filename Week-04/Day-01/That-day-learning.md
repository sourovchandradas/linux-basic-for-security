# 🐧 Day 22 : Filesystem and Storage Device Management

Welcome to Day 22 of my Linux Security learning journey. This document details Linux storage architecture, `/dev` hardware node classifications, drive partitioning standards, manual/automatic mounting mechanisms, and storage monitoring and filesystem repair workflows using utilities like `fdisk`, `lsblk`, `df`, and `fsck`.

---

## 🎯 Key Points & Core Concepts

### 1. 📂 Fundamentals of Linux Storage & Drive Representation

* **Windows vs. Linux Storage Architecture:**
* **Windows:** Assigns distinct drive letters (e.g., `C:`, `D:`, `E:`) to separate storage partitions.
* **Linux:** Uses a single, unified hierarchical directory tree structure starting at the root (`/`) directory. External drives and storage partitions must be logically attached ("mounted") onto an empty directory within this tree.


* **Role of the `/dev` Directory:**
* In Linux, every physical or virtual hardware device is represented as a special device node file inside the `/dev` directory.


* **Device Node Classification (`ls -l /dev`):**
* **Block Devices (Prefix `b`):** Read and write data in fixed-sized chunks or blocks (e.g., 512B, 4KB sectors). Examples: Hard drives (`sda`), USB drives (`sdb`), NVMe drives.
* **Character Devices (Prefix `c`):** Stream data serially character-by-character. Examples: Terminal interfaces (`/dev/tty`), serial ports.



---

### 2. 🏷️ Storage Device & Partition Naming Conventions

* **Physical Drive Designations:**
* **Legacy Drives:** Floppy disks (`fd0`), IDE drives (`hda`).
* **Modern Storage:** SATA, SCSI, NVMe, and USB flash drives are designated sequentially:
* `/dev/sda` : First physical SATA/SCSI/USB storage drive.
* `/dev/sdb` : Second physical storage drive.
* `/dev/sdc` : Third physical storage drive.




* **Partition Identification (Minor Numbers):**
* Individual drive partitions are identified by appending a minor number to the drive designation:
* `/dev/sda1` : First partition on the primary drive.
* `/dev/sda2` : Second partition on the primary drive.
* `/dev/sdb1` : First partition on the secondary drive.




* **Filesystem Types:**
* **Linux Native:** `ext2`, `ext3`, `ext4` (`ext4` is standard across distributions).
* **Cross-Platform / Windows:** `NTFS`, `FAT32`, `exFAT` (common on external flash drives).



---

### 3. 🔌 Partition Inspection & Drive Mounting

* **Inspecting Storage Devices:**
* `fdisk -l` : Displays detailed structural layout, sector counts, partition tables, and file system types.
* `lsblk` : Displays a clean, tree-structured overview of block devices, partitions, sizes, and active mount points.



Example — Inspecting storage block layout with `lsblk`:

```bash
lsblk

```

#### 🖼️ Terminal Output

---

* **Mounting & Unmounting:**
* **Mount Point:** An empty target directory used to attach a filesystem (e.g., `/mnt` for manual mounts, `/media` for automatic removable media mounts).
* **`/etc/fstab`:** Configuration table listing storage devices mounted automatically during system boot.



Example — Manually mounting a partition (`/dev/sdb1`) to the `/mnt` directory:

```bash
mount /dev/sdb1 /mnt

```

#### 🖼️ Terminal Output

---

Example — Safely detaching an unmounted partition using `umount`:

```bash
umount /dev/sdb1

```

> ⚠️ **Syntax Warning:** The unmount command is spelled **`umount`** (there is no "n" after "u"). Active devices with open files or active terminal sessions cannot be unmounted.

---

### 4. 📊 Filesystem Health & Disk Usage Monitoring

* **Monitoring Storage Capacity (`df`):**
* The `df` (disk free) utility displays total, used, and available space across mounted file systems.



Example — Checking available disk space across all mounted drives:

```bash
df -h

```

#### 🖼️ Terminal Output

---

* **Checking & Repairing Filesystems (`fsck`):**
* The `fsck` (filesystem check) utility scans and repairs damaged partition sectors.
* `-p` Flag: Automatically repairs detected filesystem errors without interactive prompting.



> 🚨 **CRITICAL RULE:** A partition **MUST be unmounted** before running `fsck`. Running `fsck` on an active, mounted partition can cause severe, unrecoverable data loss and filesystem corruption.

Example — Unmounting a damaged partition and running an automated repair:

```bash
umount /dev/sdb1
fsck -p /dev/sdb1

```

#### 🖼️ Terminal Output

---

## 🛠️ Utilities & Command Reference

| Category | Command / File | Syntax / Example | Description |
| --- | --- | --- | --- |
| **Device Nodes** | `/dev` | `ls -l /dev/sd*` | Directory housing physical and virtual hardware node files. |
| **Deep Partition Info** | `fdisk` | `fdisk -l` | Lists detailed partition tables, sectors, and device specs. |
| **Visual Block Tree** | `lsblk` | `lsblk` | Displays a visual tree of block devices, partitions, and mount paths. |
| **Mount Device** | `mount` | `mount /dev/sdb1 /mnt` | Attaches a partition to a specific directory within the filesystem tree. |
| **Unmount Device** | `umount` | `umount /dev/sdb1` | Detaches a target partition from its assigned mount point. |
| **Boot Configuration** | `/etc/fstab` | `cat /etc/fstab` | File configuring automated drive mounts at boot time. |
| **Capacity Check** | `df` | `df -h` | Displays disk space usage for active mounted filesystems. |
| **Filesystem Repair** | `fsck` | `fsck -p /dev/sdb1` | Checks and repairs corrupted storage partitions (Requires unmounting first). |

---

## 🔑 Key Takeaways for Revision

1. **Unified Root Paradigm:** Linux attaches every physical storage device to a single directory tree rooted at `/`, avoiding Windows-style drive letters (`C:`, `D:`).
2. **Device Node Prefixes:**

$$\text{Block Device (Drives/Partitions)} \longrightarrow \texttt{b prefix in ls -l}$$


$$\text{Character Device (Serial/Terminal)} \longrightarrow \texttt{c prefix in ls -l}$$


3. **Spelling Precision:** Always use `umount` (without an "n") to unmount filesystems.
4. **Order of Operation for Filesystem Maintenance:**

$$\text{Step 1: Check Space } (\texttt{df}) \longrightarrow \text{Step 2: Unmount Partition } (\texttt{umount}) \longrightarrow \text{Step 3: Repair Filesystem } (\texttt{fsck -p})$$



---
