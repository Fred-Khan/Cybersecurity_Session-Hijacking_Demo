# Cybersecurity_Session-Hijacking_Demo
## HTTP Session Hijacking Demo

Session hijacking is a critical security issue where an attacker takes over a user's session by stealing or guessing the session token. This demonstration should be performed in a controlled environment with clear ethical considerations, ensuring that it’s only done with explicit permission and for educational purposes.

### Prerequisites
- **Two Virtual Machines**: 
    - One with a **Web Browser** (FireFox, Chrome, etc..) pre-installed.
    - One with **Ettercap** & **WireShark** installed (Kali Linux).

- **A Simple Web Application**: 
    - A basic PHP web application with session handling (http://http.thetruthhurts.me/).

### Step-by-Step Guide

#### Step 1: Prepare Attacker Environment

##### Enable IPV4 Forwarding
Before you can capture the frames being sent from the victim to the web server, you need to configure the attacker machine to forward IPv4 frames before it can act as the middleman.

1. Open the terminal.
2. Execute `sysctl -w net.ipv4.ip_forward=1`.

##### Virtual Machines
Some hypervisor like **HyperV** block **MAC Spoofing** (if you arent using HyperV, skip this part), so you will need to configure the virtual machine to allow MAC spoofing.
1. Open the `Settings` for the attacker VM by right clicking the VM.
2. Expand the `Network Adapter` settings and click `Advanced features`.
3. Tick the `Enable MAC address spoofing` setting.
4. Click `OK`.

#### Step 2: ARP Poisoning (Ettercap)
To capture the frames between the victim and the web server, we need to send out Address Resolution Protocol (ARP) messages to both the victim and the gateway. To achieve this, you can use Ettercap which comes with Kali Linux to act as the middleman.

1. **Open Ettercap (Graphical)**
    - Search for `Ettercap` in Kali Linux and select the graphical version.
2. **Configure Ettercap**
    - Leave the settings are default, but ensure you select the correct network interface that is connected to the network.
    - Click the checkmark `✓` button in the titlebar of Ettercap to confirm the configuration.
3. **Scan for Hosts**
    - Click the **magnifying glass** icon in the titlebar of Ettercap to scan the subnet for connected hosts.
4. **Target Hosts**
    - Once the scanning has complete, click the **host list** icon next to the magnifying glass in the titlebar of Ettercap to show the scanned hosts.
    - Select the network address of the victim and click `Add to Target1`.
    - Select the network address of the gateway and click `Add to Target2`.
5. **Poison**
    - click the **globe icon** in the titlebar of Ettercap and select `ARP Poisoning...`.
    - Leave the options are default and click `OK`.
    - The attacker should now be poisoning the ARP cache of both the gateway and the victim.

#### Step 3: Demonstrate Session Hijacking

1. **Log in as a Victim:**
   - On the victim machine, log in to the web application (http://http.thetruthhurts.me/) using the credentials `danny/password1`. This will create a session.

2. **Capture the Session ID:**
   - Open Wireshark on the attacker machine and start capturing traffic. Apply a filter like `http` or `cookie` to capture HTTP packets.
   - On the victim machine, after logging in, note that the session ID is transmitted as a cookie in the HTTP headers.
   - The attacker, using Wireshark, captures the session ID from the victim's HTTP request.

3. **Hijack the Session:**
   - On the attacker machine, open the same web application (http://http.thetruthhurts.me/) and login using the credentials `hacker/hunter1`.
   - Navigate to the profile page.
   - Open the browser's developer tools (usually by pressing F12), and go to the "Application" or "Storage" tab, where cookies are listed.
   - Replace the attacker's session ID with the victim's session ID that was captured.
   - Refresh the page. The attacker now has access to the victim's session, effectively hijacking it.

#### Step 4: Impact and Mitigation

1. **Impact:**
   - An attacker can impersonate a user by hijacking the session. This can lead to unauthorised access to sensitive data, unauthorised transactions, or complete control of a user's account.

2. **Mitigation Techniques:**
   - **Use HTTPS**: Encrypts the session ID during transmission, making it harder for attackers to capture it.
   - **Regenerate Session IDs**: Regenerate the session ID upon login and at regular intervals.
   - **Use Secure and HttpOnly Flags**: Mark cookies as `Secure` to ensure they are only sent over HTTPS and `HttpOnly` to prevent access via JavaScript.
   - **Session Expiration**: Implement short session expiration times and automatic logouts.

#### Step 5: Ethical Considerations

- **Controlled Environment**: Ensure that this demonstration is performed in a controlled environment, such as a lab setup, where all participants are aware of and have consented to the activity.
- **Legal Compliance**: Never perform session hijacking or any other attacks on live or production systems without explicit permission.

### Summary

This demonstrates the vulnerabilities of session handling in web applications and emphasises the importance of implementing robust security measures to prevent session hijacking. By understanding both the attack and the defense mechanisms, one can gain a deeper understanding of the importance of secure session management in cybersecurity.

### Further Reading
If you are interested in reading more about this concepts you can visit the pages below, or do your own research (do your own research!)
- **ARP Spoofing**
    - https://www.crowdstrike.com/en-us/cybersecurity-101/social-engineering/arp-spoofing/

- **Session Hijacking**
    - https://www.kaspersky.com/resource-center/definitions/what-is-session-hijacking

If you are also interested in your own session hijacking demo done locally, you can clone [this repository](https://github.com/lewpar/SessionHijackDemo) and deploy it under a LAMP/WAMP stack.