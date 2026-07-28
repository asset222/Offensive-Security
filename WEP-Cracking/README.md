# WEP Cracking

### Objective
This task demonstrates the weaknesses of WEP wireless security in an authorised home lab environment. This project documents the setup, packet capture process, key recovery, essential findings, and lessons learned about why WEP is considered insecure.

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
      - ifconfig wlan0 hw ether 00:11:22:33:44
      - ifconfig wlan0 up
      - Ifconfig wlan0 hw ether 00:11:22:33:44:55
      - (give the screenshot of the command utput here with the new MAC) 
      - 

  
