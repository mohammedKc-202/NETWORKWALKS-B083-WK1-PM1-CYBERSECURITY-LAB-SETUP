# 🛡️ Cybersecurity & Ethical Hacking Lab Setup (WK1-PM1)

A comprehensive guide and documentation for setting up an isolated, multi-OS Cybersecurity & Ethical Hacking testing laboratory using **Oracle VM VirtualBox** and **Kali Linux**.

---

## 📋 Table of Contents
1. [Project Objectives](#-project-objectives)
2. [Hardware & Software Prerequisites](#-hardware--software-prerequisites)
3. [Network Architecture & IP Allocation](#-network-architecture--ip-allocation)
4. [Phase 1: Core Lab Deployment (Kali Linux & NATNetwork)](#-phase-1-core-lab-deployment-kali-linux--natnetwork)
5. [Phase 2: Target Machines Setup (Optional)](#-phase-2-target-machines-setup-optional)
6. [Troubleshooting & Common Issues](#-troubleshooting--common-issues)
7. [Verification Checklist](#-verification-checklist)

---

## 🎯 Project Objectives
The goal of **Task WK1-PM1** is to build a virtualized penetration testing environment on a host PC/Laptop with the following specifications:
- **Hypervisor:** Oracle VM VirtualBox (latest recommended version).
- **Attacking Machine:** Kali Linux (`10.0.0.2/24`).
- **Network Topology:** Isolated `NATNetwork` operating on subnet `10.0.0.0/24` with full internet access.
- **Host Integration:** Bidirectional Clipboard, Drag & Drop, and a permanent `/downloads` Shared Folder mapped to the host machine's `Downloads` directory.

---

## 💻 Hardware & Software Prerequisites

### Recommended Host Specifications
- **CPU:** Intel Core i3/i5 or AMD equivalent (with Hardware Virtualization `VT-x` / `AMD-V` enabled in BIOS/UEFI).
- **RAM:** 8 GB minimum (16 GB+ recommended when running multiple target VMs simultaneously).
- **Storage:** 256 GB SSD or more.
- **Host OS:** Windows 10 / 11, macOS, or Linux.

### Required Software
| Software | Purpose | Official Link |
| :--- | :--- | :--- |
| **7-Zip** | Extracting the `.7z` pre-built Kali Linux archive | [7-zip.org/download.html](https://7-zip.org/download.html) |
| **Oracle VirtualBox** | Base Hypervisor for running virtual machines | [virtualbox.org/wiki/Downloads](https://www.virtualbox.org/wiki/Downloads) |
| **Kali Linux (Pre-built VM)** | Attacking / Ethical Hacking distribution | [kali.org/get-kali](https://www.kali.org/get-kali/) |

---

## 🌐 Network Architecture & IP Allocation

All virtual machines communicate over a custom VirtualBox **NATNetwork** (`NatNetwork`) within the `10.0.0.0/24` subnet. VirtualBox assigns `10.0.0.1` as the default gateway.

```text
+-----------------------------------------------------------------------------------+
| Host Machine (Main OS - Windows 10/11)                                            |
|                                                                                   |
|   +---------------------------------------------------------------------------+   |
|   | Oracle VM VirtualBox                                                      |   |
|   | Network Mode: NATNetwork (NatNetwork) | Subnet: 10.0.0.0/24 | GW: 10.0.0.1|   |
|   |                                                                           |   |
|   |   +-------------------------------------------------------------------+   |   |
|   |   | [ ATTACKER MACHINE ]                                              |   |   |
|   |   | Kali Linux                                                        |   |   |
|   |   | Static IP: 10.0.0.2/24                                            |   |   |
|   |   +-------------------------------------------------------------------+   |   |
|   |                                     |                                     |   |
|   |            +------------------------+------------------------+            |   |
|   |            |                        |                        |            |   |
|   |            v                        v                        v            |   |
|   |   +-----------------+      +-----------------+      +-----------------+   |   |
|   |   | Windows 11      |      | Windows 10      |      | Windows 7       |   |   |
|   |   | IP: 10.0.0.11/24|      | IP: 10.0.0.10/24|      | IP: 10.0.0.7/24 |   |   |
|   |   +-----------------+      +-----------------+      +-----------------+   |   |
|   |                                                                           |   |
|   |   +-----------------+      +-----------------+                            |   |
|   |   | Win Server 2016 |      | Android 9.0 R2  |                            |   |
|   |   | IP: 10.0.0.16/24|      | IP: 10.0.0.9/24 |                            |   |
|   |   +-----------------+      +-----------------+                            |   |
|   +---------------------------------------------------------------------------+   |
+-----------------------------------------------------------------------------------+
```

### IP Addressing Table
| Virtual Machine | Role | IPv4 Address | Subnet Mask | Default Gateway | DNS Server |
| :--- | :--- | :--- | :--- | :--- | :--- |
| **Kali Linux** | **Attacker** | `10.0.0.2` | `255.255.255.0` (`/24`) | `10.0.0.1` | `8.8.8.8` (or `10.0.0.1`) |
| **Windows 11** | Target | `10.0.0.11` | `255.255.255.0` (`/24`) | `10.0.0.1` | `8.8.8.8` |
| **Windows 10** | Target | `10.0.0.10` | `255.255.255.0` (`/24`) | `10.0.0.1` | `8.8.8.8` |
| **Windows 7** | Target (Optional) | `10.0.0.7` | `255.255.255.0` (`/24`) | `10.0.0.1` | `8.8.8.8` |
| **Windows Server 2016** | Target (Optional) | `10.0.0.16` | `255.255.255.0` (`/24`) | `10.0.0.1` | `8.8.8.8` |
| **Android 9.0 R2** | Target (Optional) | `10.0.0.9` | `255.255.255.0` (`/24`) | `10.0.0.1` | `8.8.8.8` / `10.0.0.1` |

---

## 🚀 Phase 1: Core Lab Deployment (Kali Linux & NATNetwork)

### Step 1: Create the VirtualBox `NATNetwork`
1. Open **Oracle VirtualBox Manager**.
2. Navigate to **File > Tools > Network Manager** (or press `Ctrl + H`).
3. Select the **NAT Networks** tab and click **Create** (`+`).
4. Configure the network under **General Options**:
   - **Name:** `NatNetwork`
   - **IPv4 Prefix:** `10.0.0.0/24`
   - **Enable DHCP:** Checked (`Enabled`)
   - **Enable IPv6:** Unchecked
5. Click **Apply**.

### Step 2: Import & Configure the Kali Linux VM
1. Extract the downloaded Kali Linux `.7z` archive using **7-Zip**.
2. In VirtualBox, click **Machine > Add** (or double-click the `.vbox` file) to register the VM.
3. Open the Kali Linux VM **Settings** (Switch to **Expert** mode in VirtualBox v7+) and configure:
   - **General > Advanced:**
     - **Shared Clipboard:** `Bidirectional`
     - **Drag'n'Drop:** `Bidirectional`
   - **System > Motherboard & Acceleration:**
     - **Base Memory:** `2048 MB` (or higher)
     - **Pointing Device:** `USB Tablet`
     - **Hardware Virtualization:** Enable `Nested Paging`
   - **Network > Adapter 1:**
     - **Enable Network Adapter:** Checked
     - **Attached to:** `NAT Network`
     - **Name:** `NatNetwork`
     - **Adapter Type:** `Intel PRO/1000 MT Desktop (82540EM)`
     - **Promiscuous Mode:** `Allow All`
     - **Virtual Cable Connected:** Checked
   - **Shared Folders:**
     - Add a new shared folder pointing to your host's `C:\Users\<username>\Downloads` folder.
     - **Folder Name:** `Downloads` (or `downloads`)
     - **Mount point:** `/downloads`
     - **Access:** `Full` | **Auto Mount:** `Yes` | **Make Permanent:** Checked.
4. Click **OK** to save the VM settings.

### Step 3: Configure Static IP inside Kali Linux (`10.0.0.2/24`)
1. Start the **Kali Linux** VM and log in (`kali` / `kali`).
2. Right-click the **Network Manager** icon in the top-right panel and select **Edit Connections...**
3. Select **Wired connection 1** and click the gear icon to edit.
4. Navigate to the **IPv4 Settings** tab:
   - **Method:** `Manual`
   - Click **Add** under **Addresses** and enter:
     - **Address:** `10.0.0.2`
     - **Netmask:** `24`
     - **Gateway:** `10.0.0.1`
   - **DNS servers:** `8.8.8.8` *(Note: Use `10.0.0.1` if internet resolution gives issues)*.
5. Click **Save** and reconnect the network interface.

### Step 4: Take a Baseline Snapshot
Once Kali Linux is configured and verified, take a clean snapshot in VirtualBox (**Machine > Take Snapshot**) so you can easily revert the VM if needed during future labs.

---

## 🖥️ Phase 2: Target Machines Setup (Optional)

### Windows 11 / 10 / 7 / Server 2016 VMs
1. Import or install the Windows Virtual Machines in VirtualBox.
2. In each VM's **Settings > Network > Adapter 1**:
   - **Attached to:** `NAT Network`
   - **Name:** `NatNetwork`
   - **Adapter Type:** `Intel PRO/1000 MT Desktop (82540EM)`
   - **Promiscuous Mode:** `Allow All`
3. Boot the Windows VM and configure its static IPv4 address according to the [IP Addressing Table](#-ip-addressing-table) (e.g., `10.0.0.10/24` for Windows 10, Gateway `10.0.0.1`, DNS `8.8.8.8`).
4. Take a snapshot (e.g., `Win10_v1507_10.0.0.10`).

### Android 9.0 R2 VM
1. In the Android VM **Settings > Display > Screen**:
   - **Video Memory:** `16 MB` (or higher)
   - **Graphics Controller:** `VBoxVGA`
   - **Acceleration:** Uncheck `Enable 3D Acceleration`
2. Attach **Network Adapter 1** to `NatNetwork`.
3. Boot the Android VM, go to **Wi-Fi Settings > VirtWifi > Network details**, and edit (pencil icon):
   - **IP settings:** `Static`
   - **IP address:** `10.0.0.9`
   - **Gateway:** `10.0.0.1`
   - **Network prefix length:** `24`
   - **DNS 1:** `8.8.8.8`
   - **DNS 2:** `10.0.0.1`
4. Click **Save**.

---

## 🔧 Troubleshooting & Common Issues

### 1. Kali Linux Has No Internet Connection (VirtualBox v7+ & Kali 2026.1+)
In newer versions of VirtualBox v7 and Kali Linux (`2026.1` and higher), IPv4 Duplicate Address Detection (`dad-timeout`) can prevent the static IP connection from coming up properly on a `NATNetwork`.

**Fix:** Open the Kali terminal and run the following three commands:
```bash
sudo nmcli connection modify "Wired connection 1" ipv4.dad-timeout 0
sudo nmcli connection down "Wired connection 1"
sudo nmcli connection up "Wired connection 1"
```
**Additional Checks:**
1. Verify that `NatNetwork` is properly configured with `10.0.0.0/24`.
2. Ensure no other running VM on the same `NatNetwork` is using `10.0.0.2`.
3. If DNS resolution fails with `8.8.8.8`, change the DNS server to `10.0.0.1` in **IPv4 Settings**.
4. Restart your Virtual Machines and your Host OS if the virtual network adapter hangs.

### 2. Browser / Windows Defender Blocks the Kali Linux Download ("Virus Detected")
Because Kali Linux comes pre-installed with legitimate penetration testing payloads and security tools (e.g., *Metasploit*, *Mimikatz*, *Webshells*), host antivirus software often flags the `.7z` archive.
- **Solution:** Create a dedicated folder on your host (e.g., `C:\CyberLab_VMs`), add it to your **Windows Security > Virus & threat protection > Exclusions** list, and enable *"Ask where to save each file before downloading"* in your browser (`chrome://settings/downloads`) to save and extract the archive directly inside the excluded folder.

### 3. Permission Denied When Accessing Shared Folders in Kali
If your `/downloads` or `/media/sf_Downloads` directory requires root privileges to open:
```bash
sudo usermod -aG vboxsf $USER
```
Log out and log back in (or reboot the Kali VM) to apply the group permissions.

---

## ✅ Verification Checklist

Run the following commands in your **Kali Linux terminal** to verify that your lab meets all **WK1-PM1** requirements:

```bash
# 1. Verify Static IP Address (Should display 10.0.0.2/24 on eth0)
ip -br a

# 2. Verify Default Gateway Connectivity
ping -c 4 10.0.0.1

# 3. Verify Full Internet Access & DNS Resolution
ping -c 4 google.com

# 4. Verify Shared Folder Access
ls -la /downloads

# 5. Test Connectivity to Target VMs (Phase 2)
ping -c 4 10.0.0.10
```