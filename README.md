# Networkwalks B083F – Week 1 Cybersecurity Lab Setup

## Project Overview

This repository documents my Week 1 lab setup for the Networkwalks Ethical Hacking & Cybersecurity internship. The goal was to build a safe virtual lab using VirtualBox and Kali Linux for practicing cybersecurity techniques.

## Week 1 Objectives

- Install 7-Zip for extracting compressed VM files
- Install VirtualBox (latest version)
- Create a NAT Network on `10.0.0.0/24`
- Import a Kali Linux VM
- Configure Kali with static IP `10.0.0.2/24`
- Verify internet connectivity from Kali
- Take a clean snapshot of the working VM
- Document the whole process with screenshots

## Lab Configuration

| Component | Configuration |
|-----------|---------------|
| Host OS | Windows 11 |
| Host | HP Laptop |
| Hypervisor | VirtualBox 7.2.14 |
| Security OS | Kali Linux |
| Kali RAM | 4096 MB |
| Kali Disk | 25 GB |
| Virtual Network | NAT Network |
| Network Address | 10.0.0.0/24 |
| Kali IP | 10.0.0.2/24 |
| Gateway | 10.0.0.1 |
| DNS | 8.8.8.8 |
| Adapter Type | Intel PRO/1000 MT Desktop |

## Lab Setup Procedure

### Step 1 – Install 7-Zip
Installed 7-Zip to extract the compressed Kali Linux virtual machine package downloaded from kali.org.

### Step 2 – Install VirtualBox
Installed VirtualBox 7.2.14 from virtualbox.org and confirmed it launched correctly.

### Step 3 – Create the NAT Network
Created a NAT Network in VirtualBox:

- **Name:** `NatNetwork`
- **IPv4 Prefix:** `10.0.0.0/24`
- **DHCP:** Enabled
- **IPv6:** Disabled

![NAT Network](screenshots/05-nat-network.png)

### Step 4 – Import / Clone Kali Linux
Imported the Kali Linux VM into VirtualBox and attached Adapter 1 to the `NatNetwork`:

![Kali Network Settings](screenshots/06-kali-network.png)

### Step 5 – Configure Kali Linux Network
Set a static IPv4 address inside Kali using `nmcli`:

```bash
sudo nmcli connection modify "Wired connection 1" \
  ipv4.addresses 10.0.0.2/24 \
  ipv4.gateway 10.0.0.1 \
  ipv4.dns 8.8.8.8 \


  ipv4.method manual

sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
https://screenshots/02-kali-ip.png

Step 6 – Snapshot
Took a clean snapshot named Week1-Clean after confirming everything worked:

https://screenshots/04-virtualbox-snapshot.png

Lab Verification
Test	Command	Result
IP address	ip a	✅ 10.0.0.2/24
Gateway	ping 10.0.0.1	✅ 0% packet loss
Internet	ping 8.8.8.8	✅ 0% packet loss
https://screenshots/07-kali-ping.png

Problems Encountered & Solutions
1. Network was set to plain NAT instead of NAT Network
Problem: The VM was attached to NAT (default VirtualBox NAT) instead of NAT Network, giving the VM a different subnet (10.0.2.0/24). The gateway 10.0.0.1 was unreachable.

Solution: Changed Adapter 1 from NAT → NAT Network and selected NatNetwork.

2. NAT Network prefix was wrong
Problem: When first creating the NAT Network, the IPv4 prefix was 10.0.2.0/24 (VirtualBox default) instead of 10.0.0.0/24.

Solution: Edited the NAT Network and set IPv4 Prefix to 10.0.0.0/24.

3. IPv4 address was lost on eth0
Problem: After switching adapter type, eth0 had no IPv4 address — only IPv6. Pings returned "Destination Host Unreachable".

Solution: Re-applied the manual IP using nmcli connection modify and cycled the connection up/down.

4. VirtualBox 7 + Kali 2026.x DAD timeout bug
Problem: Even with correct settings, Kali sometimes lost internet due to a known DAD (Duplicate Address Detection) timeout issue.

Solution:

bash
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
5. MAC address conflict between original and clone
Problem: Cloning the Kali VM initially kept the same MAC address as the source VM, which would have caused network conflicts.

Solution: During cloning, selected "Generate new MAC addresses for all network adapters".

What I Learned
Virtual networking: How VirtualBox NAT Network differs from plain NAT and Bridged.

Kali network configuration: Using nmcli to set static IP, gateway, and DNS.

Troubleshooting: Diagnosing "Destination Host Unreachable" step by step.

Snapshots: The value of a clean restore point before starting labs.

Documentation: Recording every step, error, and fix as part of a technical project.

Week 1 Status
✅ Lab setup completed successfully.
✅ Kali Linux configured with correct IP and internet access.
✅ Snapshot Week1-Clean saved.
✅ Ready for Week 2 practical exercises.

Tools Used
7-Zip

VirtualBox 7.2.14

Kali Linux (pre-built VM)

Windows 11
