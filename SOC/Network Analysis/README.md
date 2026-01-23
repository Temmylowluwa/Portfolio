# Network Analysis Project

## Project Overview
This project focused on analyzing network traffic using **Wireshark**, a powerful packet capture and analysis tool. The aim was to understand how data flows across a network, identify anomalies, and gain practical insights into network security. Through hands-on analysis of packet capture files, I investigated real-world network incidents, including brute-force attacks, lateral movement, and command-and-control communications.

## Tools and Technologies
- **Wireshark**: Primary tool for packet capture, filtering, and deep inspection of network protocols.
- **Packet Capture Files (.pcap)**: Analyzed provided captures (Networkcapture2.pcap and Networkcapture3.pcap) to identify threats.

## Methodology
1. **Data Collection**: Utilized existing packet capture files as the data source.
2. **Traffic Analysis**: Applied filters to isolate specific protocols (HTTP, TCP, UDP, ICMP, FTP) and suspicious activities.
3. **Incident Investigation**: Interpreted packet headers, payloads, and sequences to detect anomalies such as brute-force attempts and unauthorized scanning.
4. **Framework Application**: Evaluated incidents using the CyBOK Incident Response Model and compared with SANS guidelines for comprehensive analysis.

## Key Findings
- Identified critical vulnerabilities, including successful credential compromise via insecure FTP.
- Detected internal lateral movement and potential command-and-control channels.
- Assessed multiple attack vectors originating from internal sources, highlighting the risks of legacy protocols.

## Skills Developed
- Proficiency in Wireshark for real-world traffic analysis and troubleshooting.
- Ability to interpret packet-level data to diagnose network issues and detect threats.
- Understanding of protocol interactions and attacker exploitation techniques.
- Practical application of incident response frameworks for threat mitigation.
- Enhanced confidence in applying network analysis to cybersecurity scenarios.

## Conclusion
Working with Wireshark provided valuable exposure to packet analysis and network security fundamentals. This project not only strengthened my technical skills but also deepened my appreciation for proactive threat detection and incident response. For detailed findings and analysis, refer to the [full report](Report.md).
