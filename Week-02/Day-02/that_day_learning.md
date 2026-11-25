# Day 09: DHCP, DNS, and the Hosts File

This note introduces Linux host configuration and name resolution. It covers DHCP lease management, DNS queries with `dig`, resolver configuration, and local hostname mappings in `/etc/hosts`. Commands and network-management tools vary by distribution.

> **Learning objective:** Understand how DHCP provides network settings, how DNS records are queried, and how resolver settings and `/etc/hosts` affect name lookup. Practice only on systems and networks you own or are authorized to administer.

## Table of Contents
* [Request or renew a DHCP lease](#request-or-renew-a-dhcp-lease)
* [Query DNS records with `dig`](#query-dns-records-with-dig)
* [Inspect and configure DNS servers](#inspect-and-configure-dns-servers)
* [Test local name mappings with `/etc/hosts`](#test-local-name-mappings-with-etchosts)
* [Hands-on practice](#hands-on-practice)
* [Quick Reference](#quick-reference)
* [Related Topics](#related-topics)
* [Additional Resources](#additional-resources)
* [Key Takeaways](#key-takeaways)

---

## Request or renew a DHCP lease

DHCP (Dynamic Host Configuration Protocol) allows a DHCP server on a network to provide settings such as an IP address, subnet prefix, default gateway, and DNS servers. The exact lease-management command depends on the network manager in use.

On systems that use `dhclient`, replace `eth0` with the interface name shown by `ip link`:

```bash
ip link
sudo dhclient eth0
ip addr show dev eth0
```

Many systems use NetworkManager or `systemd-networkd` instead of invoking `dhclient` directly. Check which service manages the connection before requesting a lease. Renewing a lease can briefly interrupt connectivity, so avoid doing it on a remote or production system without a recovery plan.

* **Labs:** [DHCP client output](Screenshot/dhclient.png), [interface configuration](Screenshot/ifconfig.png)

---

## Query DNS records with `dig`

DNS (Domain Name System) resolves names to records such as IP addresses and mail exchangers. `dig` is a command-line utility for making DNS queries. The examples below use `example.com` for learning; query domains only as permitted by your network or organization's policy.

### Query name server (NS) records

NS records identify the name servers associated with a domain:

```bash
dig example.com NS
```

Use `+short` for a compact answer:

```bash
dig example.com NS +short
```

### Query mail exchanger (MX) records

MX records identify the mail servers configured to receive email for a domain. They do not reveal mailboxes or provide access to those systems.

```bash
dig example.com MX
dig example.com MX +short
```

* **Labs:** [NS query](Screenshot/nameserver.png), [MX query](Screenshot/mail-exchange.png)

---

## Inspect and configure DNS servers

Linux systems may receive DNS settings through DHCP and manage them with NetworkManager, `systemd-resolved`, or another network service. On many systems, `/etc/resolv.conf` is generated or managed by that service; avoid editing or replacing it directly unless your system documentation says to do so.

Inspect resolver configuration with the tools available on your system:

```bash
resolvectl status
cat /etc/resolv.conf
```

### Configure DNS with NetworkManager

First identify the active connection name. Replace `Wired connection 1` with the connection name shown by `nmcli connection show`. Use DNS servers appropriate for your network or lab and follow local policy.

```bash
nmcli connection show
sudo nmcli connection modify "Wired connection 1" ipv4.dns "1.1.1.1 8.8.8.8"
sudo nmcli connection up "Wired connection 1"
```

This changes the connection's DNS configuration and may briefly reconnect it. To clear the explicitly configured DNS servers and allow automatic DNS settings again:

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.dns ""
sudo nmcli connection modify "Wired connection 1" ipv4.ignore-auto-dns no
sudo nmcli connection up "Wired connection 1"
```

If another network manager is in use, follow its documentation instead of replacing a manager-controlled `/etc/resolv.conf` file.

* **Labs:** [Open resolver settings](Screenshot/mousepad-resolv.conf.png), [resolver configuration example](Screenshot/resolv.conf-edit.png), [command output](Screenshot/resolv.conf-command.png)

---

## Test local name mappings with `/etc/hosts`

The `/etc/hosts` file provides static, local mappings between IP addresses and hostnames. The system's lookup order is configured in `/etc/nsswitch.conf`; many Linux systems consult the hosts file as part of name resolution.

For a local test, use a reserved `.test` name and the loopback address. This changes name resolution only on your own machine and does not change public DNS.

Open the file with a safe editor:

```bash
sudoedit /etc/hosts
```

Add a line such as:

```text
127.0.0.1    example.test www.example.test
```

Separate the address and names with spaces or tabs. Save the file, then check the result using the system name-service lookup:

```bash
getent hosts example.test
```

Remove the test entry when finished. Do not use hosts-file changes to impersonate real services or redirect other people's traffic.

* **Labs:** [Open hosts file](Screenshot/mousepad-hosts.png), [before editing](Screenshot/before-edit-mousepad-hosts.png), [after editing](Screenshot/after-edit-mousepad.png)

---

## Hands-on practice

These commands inspect local settings and make DNS queries for the reserved example domain. The optional hosts-file edit changes only your own machine; remove the test entry afterward.

```bash
# Inspect the interface and resolver configuration
ip addr
resolvectl status

# Query example DNS records
dig example.com NS +short
dig example.com MX +short

# After adding the local example.test mapping to /etc/hosts:
getent hosts example.test
```

**Check your understanding:** What settings can DHCP provide? What is the difference between NS and MX records? How can `/etc/hosts` affect local name lookup, and how do you verify a local mapping?

---

## Quick Reference

| Command / file | Example | Purpose |
|---|---|---|
| `ip link` | `ip link` | List network interfaces and their names. |
| `dhclient` | `sudo dhclient eth0` | Request or renew a DHCP lease on systems that use this client. |
| `dig` | `dig example.com NS` | Query DNS records such as NS and MX. |
| `nmcli` | `nmcli connection show` | Inspect and configure NetworkManager connections. |
| `resolvectl` | `resolvectl status` | Inspect DNS settings managed by `systemd-resolved`. |
| `/etc/resolv.conf` | `cat /etc/resolv.conf` | Show resolver configuration or a manager-generated file. |
| `/etc/hosts` | `getent hosts example.test` | Check local hostname mappings through system name resolution. |

---

## Related Topics
* [Week 02, Day 01: Linux Networking Basics](../Day-01/that_day_learning.md)
* [Week 01, Day 03: Linux Basics & Core Commands](../../Week-01/Day-03/that_day_learning.md)

---

## Additional Resources
* Manual pages: `man dhclient`, `man dig`, `man nmcli`, `man resolv.conf`, `man hosts`
* [NetworkManager documentation](https://networkmanager.dev/docs/)
* [systemd-resolved documentation](https://www.freedesktop.org/software/systemd/man/latest/systemd-resolved.service.html)

---

## Key Takeaways

1. DHCP can provide an IP address, gateway, and DNS settings; the client and network manager vary by system.
2. Use `dig` to query DNS record types such as NS and MX for authorized learning and administration.
3. Prefer configuring DNS through the active network manager instead of directly overwriting a managed `/etc/resolv.conf`.
4. `/etc/hosts` provides local hostname mappings, while the system's name-service configuration determines lookup order.
5. Use reserved test names such as `.test` and loopback addresses for local exercises, then remove test changes afterward.
6. Make network configuration changes only on systems and networks you own or are authorized to manage.

---

*Last Modified : November 25, 2026*
