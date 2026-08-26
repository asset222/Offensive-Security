# WEP Cracking

## Objective

This project demonstrates the security weaknesses of **Wired Equivalent Privacy (WEP)** in an authorised home lab environment.

The lab focused on wireless traffic monitoring, packet capture, IV collection, WEP key recovery, and troubleshooting VMware and wireless adapter connectivity.

> **Disclaimer:** This project was performed only on my own authorised home lab network for cybersecurity learning and testing purposes.

---

## How WEP Encryption Works

WEP uses the **RC4 stream cipher** to encrypt wireless traffic.

For each packet:

```text
24-bit Initialization Vector (IV)
            +
      Shared WEP Key
            ↓
      RC4 Keystream
            ↓
Encrypted Wireless Packet
```

The IV is transmitted with each wireless packet. Because WEP uses a small **24-bit IV space**, IV values eventually repeat when enough traffic is generated.

Repeated or weak IVs can expose information about the shared encryption key, allowing statistical analysis to recover the key.

This fundamental design weakness is one of the main reasons WEP is considered insecure.

---

## Skills Demonstrated

- Wireless network security fundamentals
- VMware virtual machine networking
- NAT, Bridged, and Host-only networking
- Kali Linux network configuration
- Wireless adapter troubleshooting
- Wireless monitor mode configuration
- Wireless packet capture and analysis
- Packet injection testing
- Understanding WEP IV reuse
- WEP key recovery using Aircrack-ng
- Linux command-line troubleshooting
- Cybersecurity lab documentation

---

## Tools and Environment

| Tool / Technology | Purpose |
|---|---|
| VMware Workstation Pro | Virtualisation platform |
| Kali Linux VM | Wireless security testing system |
| Windows 10 VM | Client/test system |
| Realtek USB Wi-Fi Adapter | Monitor mode and packet capture |
| TP-Link Router | Authorised WEP lab network |
| Aircrack-ng Suite | Wireless traffic capture and analysis |

### Aircrack-ng Tools

| Tool | Purpose |
|---|---|
| `airmon-ng` | Configure monitor mode |
| `airodump-ng` | Capture wireless packets and IVs |
| `aireplay-ng` | Packet injection and traffic generation |
| `aircrack-ng` | Analyse captured packets and recover WEP keys |

---

## Project Workflow

The assessment followed the following workflow:

1. Configure the external wireless adapter.
2. Enable monitor mode.
3. Identify the authorised WEP network.
4. Capture wireless traffic.
5. Monitor IV and packet collection.
6. Generate authorised test traffic when required.
7. Analyse the captured traffic.
8. Recover and verify the WEP key.

<img width="456" height="79" alt="image" src="https://github.com/user-attachments/assets/9f81f32f-6b38-4da7-a54e-783ad70597b6" />

   
9. Document findings and troubleshooting steps.

Detailed commands and technical notes are available here:

➡️ **[View Detailed Lab Notes](LAB_NOTES.md)**

---

## Lab Results

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

The experiment demonstrated that an attacker within wireless range could potentially recover a WEP key after collecting enough network traffic.

No direct access to the router configuration or original password was required during the recovery process.

---

## Key Findings

- WEP relies on an outdated encryption design.
- WEP uses a small **24-bit Initialization Vector (IV)**.
- IV values eventually repeat when enough packets are transmitted.
- Repeated and weak IVs can reveal information about the shared key.
- The IV is transmitted with every wireless packet.
- Increased network activity can accelerate IV collection.
- A longer WEP password does not fix the underlying cryptographic weakness.
- WEP should not be used to protect modern wireless networks.

---

## Troubleshooting Experience

During the project, I also resolved several practical issues involving:

- VMware NAT connectivity
- VMware networking services
- USB Wi-Fi adapter passthrough
- Kali Linux network configuration
- Monitor mode configuration
- Insufficient IV collection

Detailed troubleshooting notes are documented in:

➡️ **[LAB_NOTES.md](LAB_NOTES.md)**

---

## Conclusion

This project demonstrated how weaknesses in WEP encryption can allow a shared wireless key to be recovered from captured network traffic.

The lab reinforced why **WEP is fundamentally insecure** and why modern wireless networks should use stronger security protocols such as **WPA2-AES or WPA3**.
