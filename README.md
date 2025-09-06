# Manual EAPOL handshake packet capture on a network with hidden SSID
Manual Eapol handshake packet capture using Wireshark on a network with hidden SSID


# 📡 WPA2 4-Way Handshake Capture on Hidden 2.4GHz Wi-Fi (Manual Method)

---

## 🧠 **Project Overview**

This project demonstrates how to manually capture a **WPA2 4-way handshake** using **Linux CLI tools only** (no airmon-ng, no aircrack-ng), on a hidden 2.4GHz network.

Tools used:

- **Parrot OS** (Debian-based penetration testing distro)
- **WiFi Nation WN-H3 USB Adapter** (supports monitor mode)
- **Wireshark** (for packet capture and analysis)
- **Android phone** (as the Wi-Fi client)
- **Router with hidden SSID**, broadcasting on 2.4GHz

The goal is to **capture all 4 EAPOL packets** by forcing a full reconnection from the client device (phone) to the router.

---

## 🔧 **Setup Details**

| Component       | Details                            |
|----------------|------------------------------------|
| OS             | Parrot OS (Linux)                  |
| Wi-Fi Adapter  | WiFi Nation WN-H3 (monitor-capable)|
| Capture Tool   | Wireshark                          |
| Router         | 2.4GHz, hidden SSID (`asdfgh`)     |
| Test Device    | Android phone                      |

---

## 🛰️ **Manual Workflow: Step-by-Step with Linux CLI**

This section documents the exact terminal commands used to discover, configure, and prepare the wireless adapter for packet capture — in **logical order**.

---

### 🔍 1. **Scan Wi-Fi Networks and Identify Router Channel**


- nmcli dev wifi list
Lists visible SSIDs and their channels.

Hidden SSIDs appear as blank but show BSSID and channel info.


- sudo iw dev <your_interface_name_used_to_scan_wifi> scan | grep -A20 "SSID: asdfgh"
  
Searches for a specific SSID and prints extended info (channel, signal, encryption, etc.).

Run multiple times if it doesn’t show results on the first try.

**🔧 2. Prepare Adapter for Monitor Mode**

- sudo ip link set wlan1 down  
- sudo iw wlan1 set monitor control
- sudo ip link set wlan1 up
Brings the interface down.

Enables monitor mode (control allows management frames).

Brings the interface back up.

Replace wlan1 with your actual adapter name (found via iw dev).

**📻 3. Lock Interface to Target Wi-Fi Channel**

- sudo iw dev wlan1 set channel 11
Locks the adapter to the router’s channel (in this case, channel 11).

Prevents missing frames due to channel hopping.

**🔎 4. Verify Adapter Mode and Status (Optional)**

- iw dev
- ip link show wlan1
Confirms monitor mode is enabled and interface is UP.

**📥 5. Start Packet Capture with Wireshark**

- sudo wireshark &
Start Wireshark with root privileges.

Select the monitor-mode interface (wlan1) to begin capturing.

Apply filters like:


- eapol
- wlan.fc.type_subtype <= 0x0c
- wlan.addr == <your_phone_mac>

---

**📲 Force Full WPA2 Handshake from Phone**

To ensure you capture all 4 EAPOL packets, you must trigger a complete re-authentication from the client (your phone):

🔁 Steps on the Phone (Client):
Go to Wi-Fi settings.

Tap on the connected hidden network.

Select "Forget Network".

Reconnect by manually entering the SSID and password.

This forces the phone to:

Send Probe Requests to find the hidden SSID.

Perform full Authentication and Association.

Initiate the 4-Way EAPOL Handshake with the router.

---

**📸 Screenshots of Captured Handshake**
All sensitive data (MAC addresses, SSIDs) have been blurred for privacy.

Screenshot	Description
eapol_packet_capture.png	Overview of all 4 EAPOL packets captured

eapol_packet_capture_1of4.png	EAPOL Packet 1 — AP to Client (ANonce)

eapol_packet_capture_2of4.png	EAPOL Packet 2 — Client to AP (SNonce + MIC)

eapol_packet_capture_3of4.png	EAPOL Packet 3 — AP to Client (GTK + MIC)

eapol_packet_capture_4of4.png	EAPOL Packet 4 — Client to AP (Handshake Done)

---

**🧠 Key Learnings**
Manually configuring monitor mode without airmon-ng

Capturing management and EAPOL frames using CLI and Wireshark

Understanding the full WPA2 authentication process

Best practices for capturing from hidden SSID networks

**🔐 Privacy & Security Notice**
This project was done in a controlled environment on my own network with personal devices. No unauthorized or unethical capturing was performed.

MAC addresses and SSIDs have been blurred in all shared materials.

---

**📚 Resources**
Wireshark Display Filters

iw Command Reference

802.11 Frame Types Explained

**#MakeNetworkGreatAgain 🔧**

---

