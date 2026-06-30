# Phase 1 — Lab Setup

**Date:** 25th June 2026  
**Time spent:** ~2 hours  
**Status:** Complete

---

## What This Phase Is About

Before any security tools can be installed, you need a network of machines to monitor and attack. Phase 1 is about building that foundation which includes three virtual machines that simulate a small company network.

The goal is not just to get VMs running, but to get them talking to each other on an isolated private network, with fixed IPs, and with a real Windows domain already configured.

---

## What I Built

### Three Virtual Machines in HyperV

| VM | OS | Role |
|---|---|---|
| Ubuntu Server | 22.04 LTS | Will host all security tools |
| Windows Server 2022 | Server 2022 | Domain controller for the lab |
| Windows 11 | 22H2 | Simulated employee endpoint |

### Network Design

All VMs run in Hyper-V using an internal virtual switch. Each VM has a single virtual adapter connected to the lab switch, which provides both inter-VM communication on 192.168.50.0/24 and internet access routed through the host machine. IP addresses are managed via DHCP reservations on Windows Server.

| Machine | Reserved IP |
|---|---|
| Windows Server | 192.168.50.10 |
| Ubuntu Server | 192.168.50.11 |
| Windows 11 | 192.168.50.12 |

### Active Directory Already Configured

Windows Server 2022 was promoted to a domain controller with:
- A working AD domain
- Domain users created
- DHCP server role installed and reservations configured
- DNS handled by the domain controller

---

## Problems I Hit

**Problem:** Ubuntu disk showing only 28GB available even though 60GB was allocated in HyperV  
**Cause:** Ubuntu's default installer creates an LVM volume but doesn't expand it to use all available disk space  
**Fix:**
```bash
sudo vgdisplay                                          # confirmed free space existed
sudo lvextend -l +100%FREE /dev/ubuntu-vg/ubuntu-lv    # claimed all free space
sudo resize2fs /dev/ubuntu-vg/ubuntu-lv                # resized filesystem
df -h                                                   # confirmed ~55GB now available
```

**Problem:** DHCP reservation for Ubuntu showed "client is not valid" error  
**Cause:** Windows Server DHCP expects MAC addresses with dashes (`08-00-27-xx-xx-xx`), but Linux displays them with colons (`08:00:27:xx:xx:xx`)  
**Fix:** Replaced colons with dashes when entering the MAC in the DHCP reservation window

---

## Verification

- All three VMs can ping each other across the host-only network
- Ubuntu holds its reserved IP (`192.168.50.11`) across reboots
- Windows 11 joined the domain successfully
- SSH from host machine into Ubuntu works

---

## What I Learned

- HyperV LVM installs don't automatically use all allocated disk space, you have to manually extend it
- DHCP reservations are better than static IPs in a lab because everything is managed in one place (the server), not scattered across each machine
- Setting up AD before the security tools means the identity layer is already there when it's needed later
