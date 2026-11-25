# 🐧 Day 09: DHCP, DNS, and the Hosts File

This note covers basic host configuration and name resolution on Linux: requesting a DHCP lease, inspecting DNS records with `dig`, configuring a DNS resolver, and testing local hostname mappings. Examples use Kali Linux-style commands; interface names, tools, and network-manager configuration may differ across distributions.

> **Learning objective:** Understand how DHCP assigns network settings, how DNS records can be queried, and how `/etc/hosts` and resolver configuration affect name lookup. Practice only on systems and networks you own or are authorized to administer.

## Table of Contents
* [Request a DHCP lease](#request-a-dhcp-lease)
* [Query DNS records with `dig`](#query-dns-records-with-dig)
* [Configure DNS servers](#configure-dns-servers)
* [Test local name mappings with `/etc/hosts`](#test-local-name-mappings-with-etchosts)
* [Hands-on practice](#hands-on-practice)
* [Quick Reference](#quick-reference)
* [Related Topics](#related-topics)
* [Additional Resources](#additional-resources)
* [Key Takeaways](#key-takeaways)

---

## Request a DHCP lease

DHCP (Dynamic Host Configuration Protocol) lets a network's DHCP server provide configuration such as an IP address, subnet prefix, default gateway, and DNS servers. The DHCP exchange is commonly summarized as **Discover, Offer, Request, Acknowledge (DORA)**.

On systems that use the `dhclient` client, you can request or renew a lease for an interface. Replace `eth0` with the interface name shown by `ip link`:

```bash
ip link
sudo dhclient eth0
ip addr show dev eth0
```

Some systems are managed by NetworkManager or `systemd-networkd` and do not use `dhclient` directly. Check the system's network manager before changing or renewing a lease. Renewing a lease can briefly interrupt connectivity.

* **Labs:** [DHCP client output](Screenshot/dhclient.png), [interface configuration](Screenshot/ifconfig.png)

---

## Query DNS records with `dig`

DNS (Domain Name System) resolves names to records such as IP addresses and mail servers. `dig` is a command-line DNS query tool. Query only domains you are permitted to investigate; the examples below use `example.com` for learning.

### Query name servers (NS)

```bash
dig example.com NS
```

The answer section contains the requested records. `+short` prints a compact answer:

```bash
dig example.com NS +short
```

### Query mail exchangers (MX)

MX records identify mail servers configured for a domain. They do not reveal mailboxes or grant access to those systems.

```bash
dig example.com MX
dig example.com MX +short
```

* **Labs:** [NS query](Screenshot/nameserver.png), [MX query](Screenshot/mail-exchange.png)

---

## Configure DNS servers

Linux systems may receive DNS settings through DHCP and manage them with NetworkManager, `systemd-resolved`, or another network service. On many systems, `/etc/resolv.conf` is generated or managed automatically; editing it directly can be temporary or overwritten. First inspect the active configuration:

```bash
resolvectl status
cat /etc/resolv.conf
```

### NetworkManager

Find the active connection name, then configure a DNS server on that connection. Replace `Wired connection 1` with the name shown by `nmcli connection show`:

```bash
nmcli connection show
sudo nmcli connection modify "Wired connection 1" ipv4.dns "1.1.1.1 8.8.8.8"
sudo nmcli connection up "Wired connection 1"
```

This changes the connection's DNS configuration and may briefly reconnect it. Use DNS servers appropriate for your network or lab, and follow your organization's policy. To revert to DNS settings supplied automatically by DHCP:

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.dns ""
sudo nmcli connection modify "Wired connection 1" ipv4.ignore-auto-dns no
sudo nmcli connection up "Wired connection 1"
```

If you are using a different network manager, consult its documentation rather than replacing a managed `/etc/resolv.conf` file.

* **Labs:** [Open resolver settings](Screenshot/mousepad-resolv.conf.png), [resolver configuration example](Screenshot/resolv.conf-edit.png), [command output](Screenshot/resolv.conf-command.png)

---

## Test local name mappings with `/etc/hosts`

The `/etc/hosts` file provides static, local hostname-to-address mappings. The system's lookup order is configured in `/etc/nsswitch.conf`; on many Linux installations, the `hosts:` line checks local files before DNS.

For a safe local test, map a reserved `.test` name to the loopback address. This affects name resolution only on your own machine and does not change public DNS:

```bash
sudoedit /etc/hosts
```

Add a line such as:

```text
127.0.0.1    example.test www.example.test
```

Separate the address and names with spaces or tabs. Save the file, then verify the mapping:

```bash
getent hosts example.test
```

Remove the test line when you are finished. Do not use hosts-file changes to impersonate real services or redirect other people's traffic.

* **Labs:** [Open hosts file](Screenshot/mousepad-hosts.png), [before editing](Screenshot/before-edit-mousepad-hosts.png), [after editing](Screenshot/after-edit-mousepad.png)

---

## Hands-on practice

These commands make read-only DNS queries and test a local mapping. The hosts-file edit is optional; remove the test entry afterward.

```bash
# Inspect the interface and current resolver configuration
ip addr
resolvectl status

# Query example DNS records
dig example.com NS +short
dig example.com MX +short

# After adding the local example.test mapping to /etc/hosts:
getent hosts example.test
```

**Check your understanding:** What information does DHCP provide? What is the difference between an NS record and an MX record? Which file can provide a local hostname mapping, and how can you verify the result?

---

## Quick Reference

| Command / file | Example | Purpose |
|---|---|---|
| `ip link` | `ip link` | List network interfaces and their names. |
| `dhclient` | `sudo dhclient eth0` | Request or renew a lease when this client is used by the system. |
| `dig` | `dig example.com NS` | Query DNS records, such as NS or MX. |
| `nmcli` | `nmcli connection show` | Inspect and configure NetworkManager connections. |
| `resolvectl` | `resolvectl status` | Inspect DNS settings managed by `systemd-resolved`. |
| `/etc/resolv.conf` | `cat /etc/resolv.conf` | Resolver configuration or a link to a manager-generated file. |
| `/etc/hosts` | `getent hosts example.test` | Local hostname mappings, subject to system lookup configuration. |

---

## Related Topics
* [Week 02, Day 01: Linux Networking Basics](../Day-01/that_day_learning.md)
* [Week 01, Day 03: Linux Basics & Core Commands](../../../Week-01/Day-03/that_day_learning.md)

---

## Additional Resources
* Manual pages: `man dhclient`, `man dig`, `man nmcli`, `man resolv.conf`, `man hosts`
* [NetworkManager documentation](https://networkmanager.dev/docs/)
* [systemd-resolved documentation](https://www.freedesktop.org/software/systemd/man/latest/systemd-resolved.service.html)

---

## Key Takeaways

1. DHCP can automatically provide an address, gateway, and DNS settings; the client and network manager vary by system.
2. Use `dig` to query DNS record types such as NS and MX for authorized learning and administration.
3. Prefer configuring DNS through the active network manager instead of directly overwriting a managed `/etc/resolv.conf`.
4. `/etc/hosts` can provide local name mappings; the system's name-service configuration determines lookup order.
5. Use reserved test names such as `.test` and loopback addresses for local exercises, and clean up changes afterward.
6. Make network configuration changes only on systems and networks you own or are authorized to manage.
