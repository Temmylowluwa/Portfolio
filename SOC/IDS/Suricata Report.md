# **Deployment and Evaluation of Suricata Intrusion Detection System**

## Table of Contents

1. Executive Summary
2. Key Findings
3. Introduction
4. Project Objectives
5. Environment Overview
6. Suricata Installation and Configuration
7. Writing Detection Rules
8. Testing and Results
9. Conclusion
10. Appendix
11. References

---

## Key Findings

- Suricata detected all simulated reconnaissance and attack activities (ICMP, Nmap, hping3, SSH) in real time.
- Custom rules successfully produced meaningful alerts with manageable false positives.
- Recommended next steps: enable EVE JSON logging, integrate alerts with a SIEM, and centralize rule sets for easier maintenance.

## **1\. Executive Summary**

This report documents the deployment, configuration, and evaluation of the **Suricata Intrusion Detection System (IDS)** within a controlled virtual laboratory environment. The purpose of this project was to gain a clear and practical understanding of how an IDS operates, how Suricata can be configured for traffic monitoring, and how it detects suspicious network activities such as ICMP pings, Nmap scans, hping3 traffic, and SSH-based attacks.

The lab environment consisted of three virtual machines: an Ubuntu system running Suricata as the IDS sensor, a Kali Linux system acting as the attacker, and a Metasploitable 2 system serving as the vulnerable target. Custom Suricata rules were written and deployed to detect reconnaissance and attack patterns. Controlled attack simulations were then carried out to validate the effectiveness of the IDS.

The results show that Suricata successfully detected all simulated attack activities in real time and generated meaningful alerts without disrupting network traffic. This project demonstrates how Suricata can be effectively used as a passive monitoring and alerting tool in real-world organizational environments.

## **2\. Introduction**

Modern computer networks are constantly exposed to threats such as scanning, probing, and unauthorized access attempts. These attacks often occur silently before any real damage is done. An **Intrusion Detection System (IDS)** helps security teams detect these early warning signs by monitoring network traffic and raising alerts when suspicious behavior is observed.

This report explains how Suricata, an open-source IDS was deployed and tested in a virtual environment. The goal was to understand how Suricata works, how detection rules are created, and how it identifies common network attacks such as ICMP pings, Nmap scans, hping3 traffic, and SSH-based reconnaissance.

All activities were conducted in a controlled and authorized virtual lab to safely simulate real-world attack scenarios.

## **3\. Project Objectives**

The main objectives of this project were to:

1. Install and configure Suricata as an Intrusion Detection System  
2. Understand how IDS tools monitor traffic passively without blocking it  
3. Write custom Suricata detection rules  
4. Detect common reconnaissance and attack techniques  
5. Gain hands-on experience applicable to real-world network security operations

## **4\. Environment Overview**

### **4.1 Virtual Infrastructure**

Three virtual machines were used in this project, each with a specific role:

| Machine | Operating System | Role | IP Address |
| :---- | :---- | :---- | :---- |
| IDS server | Ubuntu | Suricata IDS  | 192.168.89.129 |
| Attacker | Kali Linux | Attacks | 192.168.89.131 |
| Target | Metasploitable 2 | Vulnerable host | 192.168.89.130 |

All machines were connected to the same virtual network, allowing the Suricata system to observe traffic between the attacker and the target, similar to how an IDS operates in a real enterprise network.

Virtual Network (192.168.89.0/24)  
\------------------------------------------------

┌────────────────────┐  
│     Attacker        │  
│     Kali Linux      │  
│ 192.168.89.131      │  
│ (Nmap, hping3, SSH) │  
└─────────┬──────────┘  
          │  Scan / Attack Traffic  
          ▼  
┌────────────────────┐  
│      Target         │  
│  Metasploitable 2   │  
│ 192.168.89.130      │  
│ (Vulnerable Host)   │  
└─────────┬──────────┘  
          │  Traffic observed  
          ▼  
┌────────────────────┐  
│        IDS          │  
│  Ubuntu \+ Suricata  │  
│ 192.168.89.129      │  
│ (Traffic Monitoring)│  
└────────────────────┘

### **4.2 Network Configuration**

1. Network Type: Internal / Host-only virtual network  
2. IP Address Range: 192.168.89.0/24  
3. IDS Interface: Configured to monitor traffic using AF\_PACKET

## **5\. Suricata Installation and Configuration**

### **5.1 Reference to Official Documentation**

Before installing Suricata, the official documentation at [**https://suricata.io**](https://suricata.io/) was reviewed to understand the recommended installation and configuration steps. All procedures in this project followed the guidelines provided by the Suricata development team.

Using the official documentation ensured that Suricata was installed correctly and configured in a way that reflects real-world deployment practices.

**Figure 1:** Suricata official website (documentation screenshot).

### **5.2 Suricata Installation**

Suricata was installed using the **OISF-maintained Personal Package Archive (PPA)**, which provides the latest stable release for Ubuntu systems.

First, support for external repositories was installed, then the Suricata stable repository was added and the package index updated:

sudo apt install software-properties-common \-y

sudo add-apt-repository ppa:oisf/suricata-stable

sudo apt update

Suricata was then installed via the Ubuntu package manager:

sudo apt install suricata \-y

**Figure 2:** Suricata package installation via apt.

After installation, the installed version was checked to confirm successful deployment:

suricata \--version

**Figure 3:** Output of `suricata --version` confirming the installed version.

Installing through the official repository ensures access to the latest stable features, performance improvements, and security updates.

### **5.3 Core Configuration File**

Suricata uses a central configuration file to control its behavior, including network definitions, rule files, logging, and packet capture settings.

The main configuration file is located at:

/etc/suricata/suricata.yaml

**Figure 4:** Excerpt of the `suricata.yaml` core configuration file.

All major configuration edits made during this project were applied within this file.

### **5.4 Network Variable Configuration**

To help Suricata distinguish between internal and external traffic, the HOME\_NET variable was **manually set** to the lab network range:

vars:

  address-groups:

    HOME\_NET: "\[192.168.89.0/24\]"

    EXTERNAL\_NET: "any"

**Figure 5:** `HOME_NET` network variable set to the lab range (192.168.89.0/24).

By defining HOME\_NET, Suricata knows which IP addresses belong to the protected internal network. This improves detection accuracy and helps reduce false alerts by giving the system proper context for network activity.

### **5.5 Rule Configuration and Loading**

Suricata uses detection rules to identify suspicious or malicious activity. For this project, **custom detection rules** were implemented to monitor different types of network traffic and attacks.

A set of directories and rule files were **manually created** to organize these rules:

/etc/suricata/rules/local.rules

/etc/suricata/rules/icmp.rules

/etc/suricata/rules/nmap.rules

/etc/suricata/rules/hping3.rules

/etc/suricata/rules/ssh.rules

**Figure 6:** Rule files and directory layout under `/etc/suricata/rules`.

The main configuration file was updated to load all the custom rule files using their **absolute paths**:

rule-files:

  \- /etc/suricata/rules/local.rules

  \- /etc/suricata/rules/icmp.rules

  \- /etc/suricata/rules/nmap.rules

  \- /etc/suricata/rules/hping3.rules

  \- /etc/suricata/rules/ssh.rules

**Figure 7:** `rule-files` configuration referencing custom rule sets.

This structure ensures Suricata reliably loads each rule set at startup. Organizing rules into separate files improves clarity, maintainability, and scalability, making it easier to manage detection for specific types of network activity.

### **5.6 Packet Capture, Interface, and Flow Identification**

Suricata was configured to monitor network traffic using its built-in packet capture options. Both **AF-PACKET** and **PCAP** capture methods were set to the ens33 interface:

af-packet:

  \- interface: ens33

**Figure 8:** AF-PACKET capture configuration using `ens33`.

pcap:

  \- interface: ens33

**Figure 9:** PCAP capture configuration using `ens33`.

In addition, the **Community ID** feature was enabled:

community-id: true

**Figure 10:** `community-id` set to `true` to enable Community ID flow identifiers.

Enabling Community ID allows Suricata to assign a unique identifier to each network flow. This helps correlate events and alerts across different systems or log files, making it easier to analyze network activity over time.

No manual changes were made to the network interface itself; Suricata automatically handled the necessary interface settings during startup. This setup allows Suricata to operate as a passive Intrusion Detection System (IDS).

### **5.7 Configuration Validation**

Before running Suricata, the configuration and rules were tested using its built-in validation feature:

sudo suricata \-T \-c /etc/suricata/suricata.yaml

The validation completed successfully, confirming that the configuration was correct and the rules loaded properly. Some minor warnings were shown but did not affect operation.

**5.8 Running Suricata**

Suricata was started in IDS mode using the configured interface:

sudo suricata \-c /etc/suricata/suricata.yaml \-i ens33

Once running, it continuously monitored network traffic and generated alerts whenever activity matched the configured rules, providing real-time visibility into potential security threats in the lab environment.

## **6\. Writing Detection Rules**

Suricata uses signature-based rules to identify suspicious traffic. Custom rules were written using private SID ranges to avoid conflicts with default rule sets.

### **6.1 ICMP Detection Rules**

These rules detect ICMP echo request (ping) activity:

**Figure 11:** ICMP detection rule snippets.

### **6.2 Nmap Scan Detection Rules**

Nmap SYN scan activity was detected by monitoring TCP SYN packets:

**Figure 12:** Nmap SYN scan detection rule examples.

### **6.3 hping3 Detection Rules**

hping3 traffic was detected based on abnormal packet behavior and TCP flags:

**Figure 13:** hping3 rule examples showing abnormal flag detection.

### **6.4 SSH Detection Rules**

Several rules were implemented to detect SSH-related activity:

**Figure 14:** SSH rule examples for connection attempts and brute-force activity.

## **7\. Running Suricata**

Suricata was started in IDS mode using the following command:

sudo systemctl start suricata

Alerts were monitored using the fast.log file:

sudo tail \-f /var/log/suricata/fast.log

## **8\. Testing and Results**

**Summary Results Table**

| Test | Technique | Expected Detection | Result | Evidence |
|---|---|---|---|---|
| ICMP Detection | ICMP echo (ping) | Alert on echo requests | Detected | Figures 15–16 |
| Nmap Scan | Nmap SYN scan | Detect SYN-based reconnaissance | Detected | Figures 17–18 |
| hping3 Traffic | hping3 crafted packets | Detect abnormal TCP flags | Detected | Figures 19–20 |
| SSH Detection | SSH connection attempts and scans | Detect SSH-related activity | Detected | Figures 21–22 |

### **8.1 ICMP Detection Test**

**Description:** ICMP echo requests were sent from Kali Linux to the target system.

**Figure 15:** ICMP traffic captured during testing (ping requests).

**Result:** Suricata successfully generated alerts indicating ICMP ping activity, even when the target did not respond.

**Figure 16:** Example alert showing an ICMP detection in Suricata's logs.

### **8.2 Nmap Scan Detection Test**

**Description:** A TCP SYN scan was performed using Nmap against the target system.

**Figure 17:** Nmap SYN scan in progress (traffic capture).

**Result:** Suricata generated multiple alerts detecting SYN-based reconnaissance activity. **Figure 18:** Example alert showing Nmap detection in Suricata's logs.

### **8.3 hping3 Detection Test**

**Description:** Crafted packets were sent using hping3 to simulate abnormal network behavior.

**Figure 19:** hping3 traffic capture used to simulate abnormal TCP behavior.

**Result:** Suricata detected the crafted traffic and generated repeated alerts.

**Figure 20:** Example alert showing hping3 detection in Suricata's logs.

### **8.4 SSH Detection Test**

**Description:** SSH connection attempts and Nmap SSH scans were initiated from the attacker system.

**Figure 21:** SSH test traffic (connection attempts and scans).

**Result:** Suricata successfully detected SSH connection attempts, scans, and brute-force-like behavior.

**Figure 22:** Example alert showing SSH-related detections in Suricata's logs.

## **9\. Conclusion**

This project successfully demonstrated the deployment and evaluation of Suricata as an Intrusion Detection System. The IDS was able to detect multiple types of reconnaissance and attack traffic in real time using custom-written rules. The experience gained from this project reflects real-world IDS deployment scenarios and highlights the importance of continuous network monitoring in modern cybersecurity environments.

Future enhancements could include integration with a SIEM platform, enabling EVE JSON logging, and expanding detection coverage to additional attack techniques.

---

## Appendix

### A. Full rule files
- `/etc/suricata/rules/local.rules`  
- `/etc/suricata/rules/icmp.rules`  
- `/etc/suricata/rules/nmap.rules`  
- `/etc/suricata/rules/hping3.rules`  
- `/etc/suricata/rules/ssh.rules`  

### B. Commands used
- `sudo apt install suricata -y`  
- `suricata --version`  
- `sudo suricata -T -c /etc/suricata/suricata.yaml`  
- `sudo suricata -c /etc/suricata/suricata.yaml -i ens33`  
- `sudo systemctl start suricata`  
- `sudo tail -f /var/log/suricata/fast.log`

### C. Representative log excerpts
(See `/var/log/suricata/fast.log` and `/var/log/suricata/eve.json` for full alerts; representative snippets can be extracted as needed.)

### D. Figures
All figures are captioned inline where referenced in the main report for easy review.

---

## **10\. References**

1. Open Information Security Foundation (OISF). *Suricata User Guide and Documentation*.  
   [https://suricata.readthedocs.io](https://suricata.readthedocs.io/)  
2. Emerging Threats. *Emerging Threats Open Ruleset*.  
   [https://rules.emergingthreats.net](https://rules.emergingthreats.net/)  
3. Nmap Project. *Nmap Network Scanning Documentation*.  
   [https://nmap.org/docs.html](https://nmap.org/docs.html)  
4. Salusky, S. *hping3 Manual and Usage Guide*.  
   [https://linux.die.net/man/8/hping3](https://linux.die.net/man/8/hping3)  
5. Scapy Project. *Understanding Packet Crafting and Network Analysis*.  
   [https://scapy.net](https://scapy.net/)  
6. Ubuntu Documentation. *Ubuntu Server Networking and Security*.  
   [https://help.ubuntu.com](https://help.ubuntu.com/)