# Secure Perimeter Network Design

## Overview
This repository describes a secure perimeter network architecture using layered defenses. The design illustrates how perimeter controls protect internal resources and support SOC monitoring.

## Table of Contents
- Architecture Summary
- Components
- Network Zones & Traffic Flow
- Security Controls
- Deployment / Implementation Notes
- Diagram
- Future Improvements

## Architecture Summary
A multi-layer perimeter design with distinct zones (Internet, Border, DMZ, Internal) and controls including stateless and stateful firewalls, IDS/IPS, and segmentation using a Layer 3 switch.

## Components
- ISP Router: upstream connectivity
- Border (Stateless) Firewall: initial filtering for inbound/outbound traffic
- IDS/IPS (Suricata): network detection and prevention in-line or mirror
- Layer 3 Switch: inter-VLAN routing and segmentation
- DMZ: public-facing services (web, DNS, file server as needed)
- Internal Firewall (Stateful): enforces east-west and north-south policies
- Internal Network Segmentation: VLANs and ACLs to limit lateral movement

## Network Zones & Traffic Flow
- Internet → ISP Router → Border Firewall → DMZ or Internal
- DMZ hosts serve public traffic with strict access rules to Internal
- IDS/IPS inspects relevant traffic (mirrored or in-line) and alerts/blocks accordingly

## Security Controls
- Defense in Depth: multiple control layers to reduce single-point failures
- Network Segmentation: VLANs and ACLs on the Layer 3 switch
- Traffic Inspection: Suricata for threat detection and signatures
- Access Control Enforcement: stateful firewall and least-privilege rules

## Deployment / Implementation Notes
- IDS: Suricata configured with rule sets appropriate to services in the DMZ
- Firewalls: border firewall for coarse filtering; internal firewall for finer policy enforcement
- High-availability and redundancy should be considered for production

## Diagram
See the architecture image: ![Secure Perimeter Design](Secure%20Perimeter%20Design.png)

## Future Improvements
- High-availability firewall cluster
- Redundant border routers and links
- SIEM integration for centralized log aggregation and alerting
- Automated configuration management (Ansible / IaC)

---
If you'd like, I can:
- add a short deployment checklist
- produce a printable diagram (PNG/PDF)
- include example Suricata and firewall configs

(file: Secure Perimeter Design/READ.md)