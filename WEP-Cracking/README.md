# WEP Cracking

### Objective
This task demonstrates the weaknesses of WEP wireless security in an authorised home lab environment. This project documents the setup, packet capture process, key recovery, essential findings, and lessons learned about why WEP is considered insecure.

### How WEP Encryption Works

- WEP uses the RC4 encryption algorithm to encrypt wireless traffic.

- For every packet, WEP combines:
  24-bit Initialization Vector (IV) + Shared WEP Key --> generates RC4 Keystream --> which is then used to Encrypt Wireless Packet.

- The Initialization Vector (IV) is transmitted in plaintext so that the receiving device can decrypt the packet.
  WEP uses a small 24-bit IV, and uses a different IV to encrypt every packet, the same IVs eventually repeat. Capturing enough packets with repeated or weak IVs allows statistical analysis to be used to recover the shared WEP key.
  This design weakness is one of the main reasons WEP is no longer considered secure.

 ### Skills Learned
- Understanding VMware virtualization process
- understanding Vmware Network Adapter settings: NAT, Bridged, Host-only
- Troubleshooting wireless adapter: (Host Machine and multi-VM connectivity issues)
- Configuring an external wireless adapter in monitor mode 
- Identifying nearby wireless networks and their security settings
- Getting familiar with several comamnd-line tools, like : airmon-ng, airodump-ng, aireplay-ng, aircrack-ng
- Understanding how WEP encryption and IV reuse create security weaknesses
- Recovering a WEP key from captured packets using aircrack-ng
- Documenting the lab setup, commands, results, and key findings
- Understanding why WEP is obsolete and should be replaced with WPA2 or WPA3

### Tools and Environment

 - VMware Workstation Pro – Used to run and manage the virtual machines.
 - Kali Linux VM – Used as the wireless security testing machine.
 - Windows 10 VM – Used as the client device connected to the test wireless network.
 - Realtek USB Wireless Adapter – Used for monitor mode, packet capture, and packet injection.
 - TP-Link Router – Configured as the authorised WEP test network.
 - Aircrack-ng Suite – Used to monitor, capture, generate, and analyse wireless traffic.
     - airmon-ng – Enabled monitor mode on the wireless adapter.
     - airodump-ng – Captured wireless packets and IVs.
     - aireplay-ng – Generated traffic and performed packet injection.
     - aircrack-ng – Analysed the captured IVs and recovered the WEP
       
### Key Learnings
- How to change the MAC address of my external Wifi Adapter:
   - Commands used:
      - ifconfig wlan0 down
      - ifconfig wlan0 hw ether 00:11:22:33:44:55
      - <img width="580" height="203" alt="image" src="https://github.com/user-attachments/assets/8426a2d5-1474-407c-8d00-9c40b63ba684" />

      - The MAC address of my enternal wireless adapter has been changed successfully. This technique may be useful in more advanced wireless security assessments.
    








### Project Workflow
- ##### Step 1: Enable Monitor Mode for the wireless Adapter:
   - iwconfig ; this will display the default mode of the wireless adapter
     - <img width="463" height="84" alt="image" src="https://github.com/user-attachments/assets/732e8ac0-6b57-4c35-abc5-384e274513cd" />
   - Now, to change the Mode=Monitor:
      - ifconfig wlan0 down ; disable the wlan0 interface
      - airmon-ng check kill ; kill any process that might block the Monitor Mode
      - iwconfig wlan0 mode monitor ; activating monitor mode
      - airmon-ng start wlan0 ; enable the wlan0 interface
      - iwconfig ; now, wlan0 should have mode=monitor
          - <img width="516" height="95" alt="image" src="https://github.com/user-attachments/assets/cbae5319-ee3b-444e-943a-80f162efd57b" />

      - Note: Not all wireless adapter supports monitor mode. We need to buy the ones that support monitor mode.
    
 - ##### Step 2:



     


  
