# 🐬 Day 28 : Understanding and Inspecting Wireless Networks (Part 02)

Welcome to Day 28 of my Linux Security learning journey. This document covers the fundamental mechanics of Bluetooth wireless communications, the BlueZ Linux protocol stack, reconnaissance tools, terminal outputs, service discovery, and reachability testing workflows for security analysis.

---

## 🎯 Key Points & Core Concepts

### 1. ⚙️ Introduction & Core Concepts

* **Ubiquity of Bluetooth Technology:** Built into almost every modern computing device—smartphones, laptops, tablets, speakers, game controllers, and IoT devices. Compromising Bluetooth connections can lead to unauthorized data exfiltration, remote device control, and malicious payload delivery.
* **Protocol & Frequency Spectrum:** Low-power, short-range communications operating across the 2.4 GHz to 2.485 GHz ISM radio frequency band.
* **Frequency Hopping:** Employs Pseudo-Random Frequency Hopping (FHSS) at **1,600 hops per second** to minimize radio interference and provide baseline transmission confidentiality.
* **Range Specifications:** Standard minimum operational range is 10 meters, though Class 1 implementations can reach up to 100 meters (extendable further using high-gain directional antennas).
* **Pairing & Discovery Flow:** Connecting two devices requires pairing. Devices broadcast their presence only when placed in "discoverable mode."
* **Discoverable Broadcast Elements:** Device Name, Class of Device (CoD), List of Services, and Technical Specifications.
* **Authentication Link Keys:** Paired devices generate and exchange a shared secret Link Key, storing it locally to authenticate future automated reconnects.


* **Device Identification:** Every interface possesses a unique 48-bit IEEE MAC-style Bluetooth Device Address (`BD_ADDR`) and a user-assigned device name.

---

### 2. 🏛️ Linux Bluetooth Protocol Stack (BlueZ)

#### What is BlueZ?

* The official Linux implementation of the Bluetooth wireless protocol family.
* Pre-installed by default across Kali Linux and standard Linux distributions.
* **Package Management:**
```bash
apt-get install bluez

```



#### Primary Utilities Overview

* **`hciconfig`:** Controls host controller interfaces (operates similarly to `ifconfig` for Wi-Fi/Ethernet).
* **`hcitool`:** Inquiry utility to extract device names, MAC addresses, device classes, and clock offsets.
* **`hcidump`:** Low-level packet analyzer that sniffs Bluetooth frames and captures data over the air.
* **`sdptool`:** Interrogates the Service Discovery Protocol (SDP) database to enumerate hosted services.
* **`l2ping`:** Transmits Link Layer Control and Adaptation Protocol (L2CAP) echo packets to measure reachability and round-trip latency.

---

### 3. 🔑 Bluetooth Reconnaissance & Inspection Workflow

1. **1. Check and Enable Interface:** hciconfig hci0 up.
Verify the local Bluetooth hardware status using `hciconfig`. Bring up the main adapter (`hci0`) to enable active RF transmissions.


2. **2. Ambient Discovery & Low-Level Inquiry:** hcitool scan & hcitool inq.
Perform beacon scans to detect discoverable devices, then execute low-level inquiries to capture clock offsets and Class of Device (CoD) parameters.


3. **:** sdptool browse <BD_ADDR.
" title="3. Service Enumeration">
Query the target's Service Discovery Protocol (SDP) engine to list running profiles and channel descriptors without needing discoverable mode.


4. **:** l2ping <BD_ADDR.
-c 3" title="4. Reachability Verification">
Send L2CAP echo requests to verify active proximity and measure link response latency.


---

### 4. 🛠️ Terminal Commands, Output Views & Data Breakdowns

#### Step 1: Interface Inspection & Activation (`hciconfig`)

Checks host hardware state and initializes the `hci0` interface.

* **Check Adapter State Command:**
```bash
hciconfig

```



#### 🖼️ Terminal Output View (`hciconfig`)



#### Output Data Breakdown:

* `BD Address`: Unique 48-bit hardware address (`10:AE:60:58:F1:37`) of the attacker's adapter.
* `UP RUNNING`: Confirms interface status is active and processing packets.
* `PSCAN INQUIRY`: Indicates Page Scan and Inquiry Scan modes are enabled.
* **Enable Adapter Command:**
```bash
hciconfig hci0 up

```


*(Executes silently with no output when successful)*

---

#### Step 2: Discovery Scanning & Inquiries (`hcitool`)

Scans for discoverable beacons and extracts low-level operational hardware metadata.

* **Scan Discoverable Devices Command:**
```bash
hcitool scan

```



#### 🖼️ Terminal Output View (`hcitool scan`)


* **Low-Level Inquiry Command:**
```bash
hcitool inq

```



#### 🖼️ Terminal Output View (`hcitool inq`)


#### Output Data Breakdown:

* `clock offset`: Internal clock counter difference used to synchronize frequency hopping channels.
* `class`: Device Class Code (`0x5a020c`) defining hardware capabilities (e.g., Smartphone, Laptop, Audio).

---

#### Step 3: Service Discovery Protocol Enumeration (`sdptool`)

Interrogates the target device's SDP records to discover active services, protocols, and channel bindings.

* **Browse Hosted Services Command:**
```bash
sdptool browse 72:6E:46:65:72:66

```



#### 🖼️ Terminal Output View (`sdptool browse`)



#### Output Data Breakdown:

* `Service RecHandle`: Unique numerical reference handle for the registered service entry.
* `Protocol Descriptor List`: Displays underlying protocol layers—such as `L2CAP` and `ATT` (Attribute Protocol)—highlighting exposure to Low Energy (BLE) attack vectors.

---

#### Step 4: Link Layer Ping & Reachability Testing (`l2ping`)

Sends L2CAP echo requests to verify physical proximity and active connection state.

* **Target Ping Command:**
```bash
l2ping 72:6E:46:65:72:66 -c 3

```



#### 🖼️ Terminal Output View (`l2ping`)


---

### 5. 🗺️ Technology Comparison: Wi-Fi (802.11) vs. Bluetooth (802.15.1)

| Feature / Metric | Wi-Fi (802.11a/b/g/n/ac) 📶 | Bluetooth / BLE (802.15.1) 🔷 |
| --- | --- | --- |
| **Primary Design Goal** | High-throughput Wireless Local Area Networks (WLAN) | Low-power Wireless Personal Area Networks (WPAN) |
| **Frequency Band** | 2.4 GHz & 5 GHz bands | 2.4 GHz ISM band (2.400–2.4835 GHz) |
| **Channel Architecture** | Fixed Channels (1–14 in 2.4GHz) | Frequency Hopping (FHSS, 1,600 hops/sec) |
| **Hardware Addressing** | 48-bit MAC Address (`BSSID`) | 48-bit Bluetooth Device Address (`BD_ADDR`) |
| **Linux Control Utilities** | `iwconfig`, `airmon-ng`, `nmcli` | `hciconfig`, `hcitool`, `sdptool` |
| **Reconnaissance Tool** | `airodump-ng` | `hcitool scan` / `sdptool browse` |

---

## 🛠️ Utilities & Command Reference

| Utility / Command | Syntax Example | Primary Purpose / Description |
| --- | --- | --- |
| **`apt-get install bluez`** | `apt-get install bluez` | Installs the complete BlueZ Linux Bluetooth stack. |
| **`hciconfig`** | `hciconfig hci0 up` | Displays local adapter status or activates/deactivates interface. |
| **`hcitool scan`** | `hcitool scan` | Performs active scanning for surrounding discoverable devices. |
| **`hcitool inq`** | `hcitool inq` | Returns low-level device metadata including clock offsets and Class Codes. |
| **`sdptool browse`** | `sdptool browse <BD_ADDR>` | Enumerates hosted SDP service records and protocol descriptor parameters. |
| **`l2ping`** | `l2ping <BD_ADDR> -c 3` | Sends L2CAP echo requests to verify physical target proximity. |
| **`hcidump`** | `hcidump -X` | Sniffs local Bluetooth host controller data traffic over the air. |

---

## 🔑 Key Takeaways for Revision

* **Interface Control:** `hciconfig` manages local Bluetooth adapters identically to how `ifconfig` manages standard networking interfaces.
* **Frequency Hopping Advantage:** Bluetooth changes frequencies 1,600 times per second across 79 channels to resist interference and sniffing.
* **Non-Discoverable Service Probing:** `sdptool browse` can query SDP services on targets even when they are not broadcasting discoverable beacons, provided their MAC address is known.
* **Reconnaissance Formula:**

$$\text{Adapter Initialization } (\texttt{hciconfig}) \longrightarrow \text{Inquiry } (\texttt{hcitool}) \longrightarrow \text{Service Enumeration } (\texttt{sdptool}) \longrightarrow \text{Reachability } (\texttt{l2ping})$$

---
