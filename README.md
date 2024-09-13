# Wi-Fi Penetration Testing (Personal Network)

**Overview:**  
This project demonstrates the process I followed to ethically hack my own personal Wi-Fi network by exploiting a known vulnerability. The purpose of this exercise was to understand wireless security protocols, how vulnerabilities can be exploited, and to reinforce the importance of securing networks against unauthorized access. All activities were conducted with permission on my own network.

---

## Project Steps

### **1. Setting Up the Environment**

To perform this Wi-Fi penetration test, I used **Kali Linux** as my primary toolset. Kali Linux includes all the necessary utilities, such as **Aircrack-ng**, **Airmon-ng**, **Airodump-ng**, and **Aireplay-ng**.

- **Kali Linux Setup:**
  - Installed Kali Linux on my machine using a VirtualBox environment.
  - Configured the network adapter to be in **Bridged Mode** to allow network-level interactions.

**Tools Used:**
- **Aircrack-ng Suite**: A comprehensive suite for testing Wi-Fi network security.
- **Kali Linux**: A Linux distribution used for penetration testing and security auditing.

[Official Kali Linux Download](https://www.kali.org/get-kali/#kali-bare-metal)

---

### **2. Monitoring Network Traffic**

Once Kali Linux was set up, I began by placing my Wi-Fi adapter into **monitor mode** using **Airmon-ng**. Monitor mode allows the network adapter to capture all wireless traffic within range, rather than just traffic meant for my device.

**Steps:**

1. **Identify Wireless Interface:**
   ```bash
   iwconfig
   ```
   This command lists all network interfaces. I identified the wireless interface (e.g., `wlan0`).

2. **Enable Monitor Mode:**
   ```bash
   airmon-ng start wlan0
   ```
   This enabled monitor mode on my wireless interface.

3. **Monitor Networks with Airodump-ng:**
   ```bash
   airodump-ng wlan0mon
   ```
   This command displayed all nearby wireless networks, along with important details such as **BSSID**, **ESSID**, **Channel**, and **Signal Strength**.

**Sample Screenshot:**

![Airodump-ng in Action](https://www.aircrack-ng.org/doku.php?id=start&image=airodump_main.jpg)

---

### **3. Capturing the WPA Handshake**

To gain access to the network, I needed to capture a **WPA handshake**. This is a key part of the WPA/WPA2 authentication process and is essential for cracking the Wi-Fi password.

**Steps:**

1. **Target a Specific Network:**
   Once I identified my network from the Airodump-ng output (using the **BSSID**), I focused on it by running:
   ```bash
   airodump-ng --bssid [BSSID] --channel [Channel] -w capture wlan0mon
   ```
   - `--bssid`: The unique identifier of the Wi-Fi network.
   - `--channel`: The channel the network is using.
   - `-w capture`: This saves the captured data into a file for analysis.

2. **Deauthentication Attack:**
   To capture the WPA handshake, I initiated a deauthentication attack to force a connected device to reconnect, which triggers the handshake:
   ```bash
   aireplay-ng --deauth 10 -a [BSSID] wlan0mon
   ```
   - This command sends 10 deauthentication packets to the target, temporarily disconnecting clients. When they reconnect, the WPA handshake is captured.

3. **Capture Verification:**
   The WPA handshake was successfully captured, and I could verify this in the top-right corner of the Airodump-ng output.

---

### **4. Cracking the WPA/WPA2 Password**

With the handshake captured, I proceeded to crack the Wi-Fi password using **Aircrack-ng**. I used a wordlist to attempt cracking the password through brute force.

**Steps:**

1. **Running Aircrack-ng:**
   I used a wordlist (such as **rockyou.txt**) and fed the captured handshake file to Aircrack-ng:
   ```bash
   aircrack-ng -w /path/to/wordlist.txt -b [BSSID] capture-01.cap
   ```
   - `-w`: Specifies the wordlist file to use for the attack.
   - `-b`: Specifies the BSSID of the target network.
   - `capture-01.cap`: The file containing the captured WPA handshake.

2. **Password Cracked:**
   After running Aircrack-ng for some time, I successfully cracked the Wi-Fi password and gained full admin access to my personal network.

**Sample Output:**

```bash
KEY FOUND! [ password123 ]
```

---

### **5. Conclusion and Remediation**

After successfully cracking the Wi-Fi password, I reflected on the implications of this vulnerability. The WPA/WPA2 protocol, though secure when strong passwords are used, can be compromised with weak passwords, highlighting the need for robust security practices.

**Key Takeaways:**
- **Use Strong Passwords**: Ensuring that the Wi-Fi password is complex, long, and includes a mix of characters significantly increases its resilience against brute-force attacks.
- **Update Router Firmware**: Keeping the router’s firmware up-to-date ensures that it is protected against known vulnerabilities.
- **Enable WPA3 if Available**: The latest WPA3 standard offers enhanced security, making it harder to crack passwords.

**Remediation Steps:**
- I immediately updated my Wi-Fi password to a stronger one (over 16 characters, including special symbols and numbers).
- Enabled WPA3 on the router (where supported) and disabled WPS to prevent other vulnerabilities.
  
---

## **Tools Used:**

- **Kali Linux**: Linux distribution with tools for penetration testing.
- **Aircrack-ng**: Suite of tools for auditing wireless networks.
- **Airmon-ng**: Tool for enabling monitor mode on wireless interfaces.
- **Airodump-ng**: Tool for capturing wireless traffic.
- **Aireplay-ng**: Tool for performing deauthentication attacks.
- **Aircrack-ng**: Tool for cracking WPA/WPA2 passwords.

---

## **Lessons Learned:**

Through this project, I learned valuable skills in wireless network security, including:
- The importance of network reconnaissance and capturing network traffic.
- How deauthentication attacks work and their role in wireless penetration testing.
- The power of brute force attacks and the need for strong passwords and modern encryption standards like WPA3.

---

This README should serve as a record of the Wi-Fi penetration test I conducted on my personal network. It highlights the importance of securing wireless networks and provides insights into the steps attackers may take to exploit vulnerabilities in insecure Wi-Fi configurations.
