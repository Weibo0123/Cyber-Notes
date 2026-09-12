# Linux Network Management

## Overview

Network management covers viewing interfaces and IP addresses, assigning addresses, enabling/disabling interfaces, viewing connections, monitoring packets, testing connectivity, resolving domain names, and sharing files over a network. This chapter uses `ifconfig`/`netstat`, the traditional tools for this — on many modern distributions these are being superseded by `ip` and `ss` respectively, but `ifconfig`/`netstat` are still extremely common in practice and worth knowing well.

## Viewing Interfaces

```bash
ifconfig -a    # -a = show ALL interfaces
```

Typical interfaces: `lo` (loopback), `ens33` (a regular network interface), `docker0` (Docker's virtual interface). An interface marked `UP` is currently active.

## Assigning an IP Address

### Dynamically, via DHCP

**DHCP (Dynamic Host Configuration Protocol)** automatically hands out IP addresses from a server-managed pool.

```bash
dhclient ens33    # request an address for this interface from a DHCP server
```

### Statically (a Fixed Address)

```bash
ifconfig ens33 down              # 1. disable the interface
ifconfig ens33 192.168.1.100     # 2. assign the fixed address
ifconfig ens33 up                # 3. re-enable the interface
```

Disabling an interface before reassigning its address, then re-enabling it, is the standard sequence — `down` removes the `UP` status, and `up` restores it.

## Viewing Network Connections: `netstat`

```bash
netstat -at       # -a = all connections, -t = TCP only
netstat -antp     # -n = numeric addresses, -p = show program name + PID — a very common combo
```

## Monitoring Packets: `tcpdump`

```bash
tcpdump -i ens33   # -i = interface to monitor; shows traffic on it in real time
```

## Testing Connectivity: `ping`

```bash
ping letsdefend.io          # send packets, wait for responses
ping -c 1 letsdefend.io     # -c = number of packets to send (here, just 1)
```

A non-response doesn't automatically mean the destination is unreachable — some systems are deliberately configured not to answer `ping` at all, as a security measure.

## DNS and Domain Name Resolution

**DNS** resolves domain names into IP addresses so users don't have to memorize numbers.

```bash
dig letsdefend.io        # DNS lookup
nslookup letsdefend.io   # DNS lookup (alternative tool)
```

### Where DNS Settings Live

```bash
cat /etc/resolv.conf    # nameserver <DNS-server-IP> — which DNS server(s) to query
```

Direct edits to `/etc/resolv.conf` can be lost after a reboot. On systems using `systemd-resolved`, the persistent place to make this change instead is `/etc/systemd/resolved.conf`.

### Local Overrides: `/etc/hosts`

```bash
cat /etc/hosts
```

```text
127.0.0.1       localhost
192.168.1.10    example.com
```

Format: `IP-address    hostname`. Entries here are checked *before* a DNS request goes out, so they let you locally override what a hostname resolves to.

## Quick File Sharing: a Python HTTP Server

```bash
python3 -m http.server
```

This serves the **current directory's** files over HTTP; another device can then fetch them at `http://<your-IP>:<port>`. Incoming requests get logged live in the terminal where the server is running.

## Related: File 02 (Linux File System Hierarchy) for `/etc` in general; file 16 (Package Management) for installing `tcpdump`/`dig`/etc. if they aren't already present; file 05 (Archive File Formats) for compressing files before sharing them over the quick HTTP server above.

## Cheat Sheet

```
ifconfig -a                    show all interfaces
ifconfig <if> down / up         disable / enable an interface
ifconfig <if> <IP>              assign an address (do this while the interface is down)
dhclient <if>                   request a DHCP address

netstat -at / -antp             TCP connections / + numeric + program+PID

tcpdump -i <if>                 monitor packets on an interface

ping <host>                     test connectivity
ping -c <n> <host>               send only <n> packets

dig <domain> / nslookup <domain>   DNS lookup
/etc/resolv.conf                 nameserver <IP>   (DNS server config; may not persist — see systemd-resolved.conf)
/etc/hosts                       IP  hostname       (local overrides, checked before DNS)

python3 -m http.server           serve current directory over HTTP
```
