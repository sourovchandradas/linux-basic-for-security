# 🦈 Day 29 : Understanding and Inspecting Wireless Networks (Part 01)

Welcome to Day 29 of my Linux Security learning journey. This document covers background mechanics of Wi-Fi (IEEE 802.11) technologies, security protocols, wireless operational modes, interface commands, terminal outputs, and step-by-step reconnaissance and cryptographic cracking workflows for penetration testing and system administration.

---

## 🎯 Key Points & Core Concepts

### 1. ⚙️ Introduction & Context

* **Core Significance:** The ability to scan, analyze, and connect to any network device is a fundamental skill for penetration testers and hackers.
* **Standard Media:** Wi-Fi (IEEE 802.11) and Bluetooth serve as standard media for modern wireless communications; mastering them is critical for reconnaissance.
* **Security & Threat Risks:** Gaining unauthorized access to a wireless Access Point (AP) grants entry into the internal network, exposing all connected hosts. Attackers can perform packet sniffing, credential harvesting, and man-in-the-middle (MitM) attacks once on the network.
* **Operating System Integration:** Kali Linux comes equipped with native network stacks, specialized drivers, and the complete `aircrack-ng` suite.

---

### 2. 🏛️ Fundamental Wi-Fi Terminology & Technologies

#### Network Identifiers

* **AP (Access Point):** Central networking device providing wireless connectivity to client devices.
* **SSID (Service Set Identifier):** Human-readable name of a single wireless network.
* **ESSID (Extended Service Set ID):** Collective network name used across multiple APs spanning a wider LAN.
* **BSSID (Basic Service Set ID):** Unique hardware MAC address identifying a specific Access Point interface.

#### Physical Layer Specifications

* **Channels:** Operates on 14 channels globally (1–14); legally restricted to channels 1–11 in the United States.
* **Signal Power (PWR):** Proximity to an AP yields higher signal-to-noise ratios, leading to lower packet corruption during captures.
* **Operational Range:** US regulatory power limit is 0.5 Watts (~300 feet / 100 meters); extended up to 20 miles with directional high-gain antennas.
* **Frequency Bands:** Primary operational frequencies are 2.4 GHz (longer range) and 5 GHz (higher throughput).

#### Security Protocols & Wireless Operational Modes

* **WEP (Wired Equivalent Privacy):** Deprecated and legacy protocol; vulnerable to statistical key recovery (cracked in minutes).
* **WPA (Wi-Fi Protected Access):** Transitional standard created to address WEP vulnerabilities using TKIP.
* **WPA2-PSK (Pre-Shared Key):** Standard modern protocol relying on CCMP/AES encryption and a 4-Way Handshake authentication flow.
* **Managed Mode:** Default client behavior (associates and communicates directly with an Access Point).
* **Master Mode:** Converts the wireless network adapter to function as an Access Point itself.
* **Monitor Mode:** Places the card in promiscuous RF mode to sniff and capture raw 802.11 frames from all surrounding networks.

---

### 3. 🔑 Basic Linux Wireless Commands

#### Interface Inspection (`ifconfig`)

Lists activated network interfaces and assigned IP configurations. Linux designates wireless interfaces sequentially as `wlan0`, `wlan1`, etc.

```bash
ifconfig wlan0

```

#### Wireless Configuration (`iwconfig`)

Displays operational parameters specific to wireless adapters including Wireless Standard, Operational Mode, ESSID Status, AP MAC Address, Transmit Power, and Signal Level.

```bash
iwconfig wlan0

```

#### 🖼️ Terminal Output View (`iwconfig wlan0`)


#### AP Scanning (`iwlist`)

Returns detailed wireless survey data from surrounding APs.

```bash
iwlist wlan0 scan

```

#### 🖼️ Terminal Output View (`iwlist wlan0 scan`)


#### Connection Management (`nmcli`)

Scans surrounding Wi-Fi networks and manages AP associations via NetworkManager.

* **Network Survey Command:**
```bash
nmcli dev wifi

```



#### 🖼️ Terminal Output View (`nmcli dev wifi`)


* **AP Association Command:**
```bash
nmcli dev wifi connect Hackers-Arise password 12345678

```



#### 🖼️ Terminal Output View (`nmcli dev wifi connect`)


---

### 4. 🛠️ Wi-Fi Reconnaissance & Cracking Workflow (`aircrack-ng` Suite)

#### Core Reconnaissance Requirements

1. Target AP's MAC Address (BSSID)
2. Target AP's Operating Channel Number
3. Connected Client's MAC Address (Station)
4. A Dictionary File (Wordlist)

1. **1. Enable Monitor Mode:** airmon-ng start wlan0.
Transitions interface from Managed to Monitor mode (creates `wlan0mon`) to capture raw RF frames. Execute `airmon-ng check kill` if background daemons interfere.


2. **2. Ambient Survey:** airodump-ng wlan0mon.
Scans surrounding airwaves to reveal target BSSIDs, operating channels, signal strengths, and active associated clients.


3. **3. Handshake Capture:** Targeted Capture & Deauth.
Locks packet capture onto the target channel while sending deauthentication frames to force client re-association and capture the WPA2 4-Way Handshake.


4. **4. Cryptographic Cracking:** aircrack-ng -w wordlist.
Executes an offline dictionary attack against the captured 4-Way Handshake file without sending packets over the air.


#### 3-Terminal Targeted Attack Workflow

```
+------------------------+     Raw 802.11 Frames     +---------------------------------+
|   Kali Linux Machine   | ------------------------> |    Target Access Point (AP)     |
|  (Attacker / wlan0mon) |                           |    BSSID: 01:01:AA:BB:CC:22     |
+------------------------+                           +---------------------------------+
            |                                                        |
            | Deauth Frame                                           | 4-Way Handshake
            v                                                        v
+------------------------+                           +---------------------------------+
|   Associated Client    | <-----------------------> |     WPA2 4-Way Handshake      |
| MAC: A0:A3:E2:44:7C:E5 |   Auto-Reconnection Flow  | (Saved to Hackers-ArisePSK.cap) |
+------------------------+                           +---------------------------------+

```

#### 🟢 Terminal 1: Focused Packet Capture

Locks capture onto Channel 10 and filters specifically for target BSSID while writing capture files to disk.

```bash
airodump-ng -c 10 --bssid 01:01:AA:BB:CC:22 -w Hackers-ArisePSK wlan0mon

```

#### 🖼️ Terminal Output View (`airodump-ng` Ambient & Targeted Capture)


#### Output Data Breakdown:

* **Top Section (Access Points):**
* `BSSID`: MAC address of the target Access Point.
* `PWR`: Signal strength level (closer to 0 indicates stronger signal).
* `CH`: Operating channel number (1–14).
* `ENC`: Encryption type (WEP, WPA, WPA2).
* `ESSID`: Broadcast network name.


* **Bottom Section (Associated Clients):**
* `BSSID`: Access Point MAC address the client is associated with.
* `STATION`: Unique MAC address of the connected target client device.
* `Notes`: Displays `EAPOL` / `WPA handshake` once captured.



#### 🟡 Terminal 2: Client Deauthentication Attack

Transmits 100 deauthentication frames to disconnect the target client, forcing an automatic reconnection that broadcasts the 4-Way Handshake.

```bash
aireplay-ng --deauth 100 -a 01:01:AA:BB:CC:22 -c A0:A3:E2:44:7C:E5 wlan0mon

```

#### 🖼️ Terminal Output View (`aireplay-ng`)



#### 🔴 Terminal 3: Offline Cryptographic Cracking

Compares computed PMK hashes from the dictionary file against the captured WPA2 handshake.

```bash
aircrack-ng -w wordlist.dic -b 01:01:AA:BB:CC:22 Hackers-ArisePSK-01.cap

```

#### 🖼️ Terminal Output View (`aircrack-ng`)


---

### 5. ⚖️ Technology Comparison: Wireless Security Protocols

| Feature / Metric | WEP 🔓 | WPA (TKIP) ⚠️ | WPA2-PSK (AES/CCMP) 🛡️ |
| --- | --- | --- | --- |
| **Encryption Algorithm** | RC4 (Stream Cipher) | RC4 with TKIP wrapper | AES-CCMP (Block Cipher) |
| **Key Exchange** | Static Key | Per-packet Key Mixing | 4-Way Handshake |
| **Security Status** | Deprecated / Broken | Vulnerable / Legacy | Standard Modern Protocol |
| **Crack Method** | Statistical IV Accumulation | Dictionary / Handshake | Dictionary / PMKID Capture |
| **Crack Speed** | Minutes (100% success) | Hours to Days (Wordlist) | Hours to Days (Wordlist) |

---

## 🛠️ Utilities & Command Reference

| Utility / Command | Syntax Example | Primary Purpose / Description |
| --- | --- | --- |
| **`ifconfig`** | `ifconfig wlan0` | Lists activated network interfaces and assigned IP configurations. |
| **`iwconfig`** | `iwconfig wlan0` | Displays wireless adapter operational parameters (Mode, ESSID, Tx-Power). |
| **`iwlist`** | `iwlist wlan0 scan` | Performs detailed wireless survey listing cells, channels, and encryption. |
| **`nmcli`** | `nmcli dev wifi connect <SSID> password <pass>` | NetworkManager CLI utility to scan and connect to wireless networks. |
| **`airmon-ng`** | `airmon-ng start wlan0` | Enables monitor mode on adapter and manages conflicting services. |
| **`airodump-ng`** | `airodump-ng -c 10 --bssid <MAC> -w <file> wlan0mon` | Captures raw 802.11 frames and writes 4-Way Handshakes to disk. |
| **`aireplay-ng`** | `aireplay-ng --deauth 100 -a <AP> -c <Client> wlan0mon` | Generates wireless traffic and sends deauthentication frames to clients. |
| **`aircrack-ng`** | `aircrack-ng -w <wordlist> -b <AP_MAC> <capture.cap>` | Performs offline dictionary attack against captured 4-Way Handshakes. |

---

## 🔑 Key Takeaways for Revision

* **Monitor Mode Function:** Places the wireless network adapter into promiscuous RF mode to capture raw 802.11 frames across surrounding channels.
* **Deauthentication Vulnerability:** Management frames in 802.11a/b/g/n are unencrypted by default, allowing attackers to spoof deauth packets and force client reconnects.
* **4-Way Handshake Capture:** The goal of deauthenticating a client is to force a re-association flow, capturing the transmitted WPA2 4-Way Handshake.
* **Offline Cracking Formula:**

$$\text{Reconnaissance } (\texttt{airodump-ng}) \longrightarrow \text{Deauth } (\texttt{aireplay-ng}) \longrightarrow \text{Capture } (\text{.cap}) \longrightarrow \text{Crack } (\texttt{aircrack-ng})$$

---

