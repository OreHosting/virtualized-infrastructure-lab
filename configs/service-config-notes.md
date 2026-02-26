# Service Configuration Notes

This document records architectural decisions, service placement rationale, access models, and operational considerations for services deployed within the homelab environment.
The purpose of this file is to preserve configuration intent and reduce ambiguity during troubleshooting, rebuilds, or future expansion.

---

## Virtualization Platform

### Proxmox
Host: 192.168.20.99  
VLAN: SERVER (192.168.20.0/24)

Purpose:
Primary virtualization platform hosting lab services and infrastructure VMs.

Access Model:
- Web UI accessible from MGMT VLAN
- Limited access from SERVER VLAN for internal service administration
- No WAN exposure

Design Notes:
- Hosted in SERVER VLAN to maintain separation between management and service hosting.
- Default TCP 8006 retained for documentation consistency and reduced complexity.
- Management plane access is intentionally restricted to reduce attack surface.

Operational Considerations:
- Critical dependency for all hosted workloads.
- Configuration backups should be automated in future improvements.
- Consider restricting UI access exclusively to MGMT VLAN.

---

## Storage Platform

### TrueNAS
Host: 192.168.20.133  
VLAN: SERVER (192.168.20.0/24)

Purpose:
Centralized storage provider for SMB shares and lab service data.

Access Model:
- SMB (TCP 445) accessible from LAN, SERVER, and MGMT VLANs.
- Web UI accessible from SERVER and MGMT VLANs.
- WAN exposure limited to specific application ports (see firewall documentation).

Design Notes:
- Positioned in SERVER VLAN to logically group storage with compute resources.
- Serves as shared storage backbone for lab workloads.
- SMB exposure limited to necessary VLANs only.

Operational Considerations:
- Currently hosts Minecraft world data.
- Future improvements include snapshot strategy expansion and off-host replication.
- Recovery procedures should be documented in a dedicated runbook.

---

## Application Host

### Debian Host (CasaOS / Crafty Controller)
Host: 192.168.20.193  
VLAN: SERVER (192.168.20.0/24)

Purpose:
Consolidated application host for containerized and service-based workloads.

Hosted Services:
- CasaOS (Web UI: TCP 50000)
- Crafty Controller (Web UI: TCP 8111)
- Minecraft Server Ports (TCP 25500–25600, UDP 40000)

Access Model:
- Web UIs accessible from MGMT and SERVER VLANs.
- Minecraft service ports exposed via WAN port forwarding.
- No general WAN access to host services.

Design Notes:
- Consolidation reduces VM sprawl and resource overhead.
- WAN exposure limited strictly to required application ports.
- Service separation maintained at application level.

Operational Considerations:
- Daily Minecraft world backup automated via scheduled task.
- Represents highest external attack surface in environment.
- Future improvement: evaluate reverse proxy or VPN-based access model instead of direct port forwarding.

---

## Network Infrastructure Devices

### Router
IP: 192.168.10.1  
VLAN: MGMT

Purpose:
Core routing and firewall platform for VLAN segmentation and WAN connectivity.

Access Model:
- Web UI accessible only from MGMT VLAN.
- No LAN or WAN administrative access permitted.

Design Notes:
- Positioned in dedicated MGMT VLAN to protect control plane.
- Serves as enforcement point for inter-VLAN security policy.

Operational Considerations:
- Firewall rule changes should be documented and versioned.
- Configuration backups should be automated.

---

### Smart Switch
IP: 192.168.10.2  
VLAN: MGMT

Purpose:
Layer 2 VLAN segmentation and trunking for lab network.

Access Model:
- Web UI accessible only from MGMT VLAN.

Design Notes:
- Management plane isolated to reduce lateral movement risk.
- No exposure to LAN or SERVER networks.

---

### Access Point
IP: 192.168.10.3  
VLAN: MGMT

Purpose:
Wireless access bridge for LAN clients.

Access Model:
- Web UI accessible only from MGMT VLAN.

Design Notes:
- Administrative interface isolated from user traffic.
- SSID mapped to LAN VLAN for endpoint access.

---

## General Architectural Principles

- Default deny posture for all inter-VLAN traffic.
- Service exposure minimized to only required ports and VLANs.
- Management plane separated from service and user networks.
- WAN exposure limited to explicit application ports.
- No direct WAN access to infrastructure management interfaces.

---

## Future Service-Level Improvements

- Implement reverse proxy for controlled application exposure.
- Introduce VPN-based administrative access model.
- Expand automated backup coverage beyond Minecraft data.
- Centralize logging for application and system logs.
- Document disaster recovery workflow for critical services.
