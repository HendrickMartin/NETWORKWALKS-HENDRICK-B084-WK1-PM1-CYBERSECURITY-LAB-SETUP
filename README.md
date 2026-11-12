# NETWORKWALKS-HENDRICK-B084-WK1-PM1-CYBERSECURITY-LAB-SETUP
# 🔐 Cybersecurity Lab Environment Setup

**Building an isolated virtual lab for penetration testing and ethical hacking practice**

![Skill](https://img.shields.io/badge/Skill-Cybersecurity-red) ![VirtualBox](https://img.shields.io/badge/VirtualBox-7.2.22-blue) ![Kali](https://img.shields.io/badge/Kali_Linux-2026.3-black) ![Network](https://img.shields.io/badge/NATNetwork-10.0.0.0%2F24-teal) ![Batch](https://img.shields.io/badge/Networkwalks-B084-orange)

---

## 📌 Project Overview

This project sets up a **virtual cybersecurity and penetration-testing laboratory** using Virtual Box and Kali Linux on my own Windows laptop.

The lab runs on a private virtual network, so more machines can be added later and used as targets for **authorized** security testing only. Everything in this repository was done on my own devices inside my own lab. No third-party systems were touched.

- **Project:** Week 1 | Project Module 1 (WK1-PM1) – Practical Lab Environment Setup
- **Program:** Networkwalks Cybersecurity Internship, Batch B084
- **Author:** Hendrick Martin

---

## ✅ Requirements and Result

| Requirement | Status |
|---|---|
| VirtualBox as base (latest version) | ✅ VirtualBox 7.2.22 |
| Kali Linux as attacker VM (latest version) | ✅ Kali Linux 2026.3 (prebuilt VirtualBox image) |
| NATNetwork with 10.0.0.0/24 subnet | ✅ `NatNetwork`, DHCP enabled |
| Clipboard and drag/drop enabled | ✅ Bidirectional |
| Shared folder (Downloads from host) | ✅ Auto-mount enabled |
| Kali IP address 10.0.0.2/24 | ✅ Static IP set with NetworkManager |
| Kali has full Internet access | ✅ Verified with ping tests |
| Snapshot taken | ✅ `My Fresh Kali Linux after installation` |

---

## 🗺️ Lab Architecture

```
┌──────────────────────────── Host: Windows 11 ────────────────────────────┐
│                                                                          │
│   ┌──────────────────────── VirtualBox 7.2.22 ────────────────────────┐  │
│   │                                                                   │  │
│   │   NATNetwork  "NatNetwork"  10.0.0.0/24  (DHCP enabled)           │  │
│   │        │                                                          │  │
│   │   ┌────┴─────────────────────┐                                    │  │
│   │   │ Kali Linux 2026.3 (VM)   │  Static IP: 10.0.0.2/24            │  │
│   │   │ Attacker machine         │  Gateway:   10.0.0.1               │  │
│   │   └──────────────────────────┘  Internet via NAT                  │  │
│   └───────────────────────────────────────────────────────────────────┘  │
└──────────────────────────────────────────────────────────────────────────┘
```

**VM resources:** 4 GB RAM, 2 CPUs, 80 GB virtual disk.

---

## 🛠️ Tools and Versions

| Tool | Version |
|---|---|
| Host OS | Windows 11 |
| 7-Zip | latest (to extract the Kali image) |
| Oracle VirtualBox | 7.2.22 |
| Kali Linux | 2026.3 (VirtualBox amd64 image) |

---

## 🚀 Step-by-Step Setup

### Step 1: Install VirtualBox
Downloaded VirtualBox from the official site and ran the installer.

![VirtualBox installer](images/01-virtualbox-install.png)

![VirtualBox Manager after install](images/02-virtualbox-manager.png)

### Step 2: Download and extract the Kali Linux image
Downloaded the prebuilt **Kali Linux VirtualBox** image from kali.org and extracted the `.7z` archive with 7-Zip.

![Extracting the Kali archive](images/03-extract-kali.png)

### Step 3: Add the Kali VM to VirtualBox
Added the extracted `.vbox` file with **Machine → Add**. This step gave an error, explained in [Troubleshooting](#-troubleshooting-problem-and-solution). After the fix, the VM appeared in the Machines list.

![Kali VM listed in VirtualBox](images/05-kali-vm-listed.png)

### Step 4: Create the NATNetwork (10.0.0.0/24)
**File → Tools → Network → NAT Networks → Create**, then set:

- Name: `NatNetwork`
- IPv4 Prefix: `10.0.0.0/24`
- Enable DHCP: ✔

![NAT Networks list](images/06-nat-networks.png)

### Step 5: Attach Kali to the NATNetwork
**Kali → Settings → Network → Adapter 1**:

- Attached to: `NAT Network`
- Name: `NatNetwork`
- Promiscuous Mode: `Allow All`

![Kali network adapter settings](images/07-kali-network-settings.png)

### Step 6: Enable clipboard and drag-and-drop
**Settings → General → Advanced**: Shared Clipboard and Drag-and-Drop both set to `Bidirectional`.

![Clipboard and drag-and-drop](images/08-clipboard-dragdrop.png)

### Step 7: Increase RAM
Raised the base memory from 2048 MB to 4096 MB for a smoother desktop.

![RAM settings](images/09-ram-settings.png)

### Step 8: Share the host Downloads folder
**Settings → Shared Folders → Add**: host Downloads folder, Auto-mount enabled.

![Shared folder settings](images/10-shared-folder-settings.png)

Inside Kali, add the user to the `vboxsf` group so the folder is accessible, then reboot:

```bash
sudo usermod -aG vboxsf $USER
sudo reboot
```

After the reboot, check the share is mounted:

```bash
ls -ld /media/sf_Downloads
```

![Shared folder visible in Kali](images/11-shared-folder-in-kali.png)

### Step 9: Configure the static IP 10.0.0.2/24
Listed the connections to get the exact name:

```bash
nmcli connection show
```

Then set the static IP, gateway and DNS, and apply it:

```bash
sudo nmcli connection modify "Wired connection 1" ipv4.method manual ipv4.addresses 10.0.0.2/24 ipv4.gateway 10.0.0.1 ipv4.dns 8.8.8.8 ipv4.dad-timeout 0
sudo nmcli connection down "Wired connection 1"; sudo nmcli connection up "Wired connection 1"
```

### Step 10: Verify the IP and Internet access

```bash
ip -4 a show eth0
ping -c3 10.0.0.1
ping -c3 8.8.8.8
ping -c3 google.com
```

**Results:**

- `eth0` shows `10.0.0.2/24`
- Gateway `10.0.0.1`: 0% packet loss
- `8.8.8.8`: 0% packet loss
- `google.com`: 0% packet loss (DNS resolution works)

![IP and ping verification](images/12-ip-and-ping-test.png)

### Step 11: Take a snapshot
Shut the VM down and took a snapshot named **My Fresh Kali Linux after installation**, so the clean state can be restored at any time.

![Snapshot taken](images/13-snapshot.png)

---

## 🧩 Troubleshooting: Problem and Solution

**Problem:** Adding the Kali `.vbox` file failed with `E_FAIL (0x80004005)`: *"Failed to open virtual machine… which has the same UUID as an existing virtual machine."*

![UUID error](images/04-uuid-error.png)

**Cause:** The Kali VM was already registered in VirtualBox from an earlier attempt. The Manager window simply was not showing it, so adding it again clashed with the existing entry.

**Solution:**

1. Checked what VirtualBox had registered:
   ```
   "C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" list vms
   ```
   Kali was already listed.
2. Confirmed its state and config path:
   ```
   "C:\Program Files\Oracle\VirtualBox\VBoxManage.exe" showvminfo "kali-linux-2026.3-virtualbox-amd64" --machinereadable | findstr /i "CfgFile VMState"
   ```
   It was registered and powered off.
3. Restarted VirtualBox Manager and opened the **Machines** view (the second icon in the sidebar, not the Home page). The VM was there, so no re-adding was needed.

**Lesson:** If a UUID error appears, check `VBoxManage list vms` first. The VM may already be registered.

---

## 🎓 What I Learned

- How to build an isolated lab with VirtualBox and a custom NAT network
- Static IP configuration on Kali with `nmcli`
- Why isolating attack machines from the real network matters
- How to verify connectivity (gateway, IP, DNS) step by step
- Using snapshots as a safe restore point
- Reading VirtualBox errors and diagnosing them with `VBoxManage`

---

## ⚖️ Ethical Use Notice

This lab is for **education and authorized practice only**. All testing is done on my own devices and virtual machines in my own lab. Unauthorized access to systems you do not own or have written permission to test is illegal.

---

*Networkwalks Cybersecurity Internship | Batch B084 | Week 1 Project 1*
