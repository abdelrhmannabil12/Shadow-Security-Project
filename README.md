🛰️ Shadow OT Security Lab — Defense in Depth Implementation
🔒 Overview

The Shadow OT Security Lab demonstrates a multi-zone, defense-in-depth network architecture integrating:

Active Directory domain infrastructure

Cisco FTD firewall enforcing segmentation and NAT

DMZ-based WSUS and Trellix servers

Acronis offline backup management

Advanced GPO hardening and endpoint lockdown policies

This project emulates a realistic OT/IT convergence environment aligned with Purdue Model and ISA/IEC-62443 security principles.

🧭 Network Architecture
Zone	Purpose	IP Range	Internet Access
Inside	AD, Acronis, and local management	192.168.100.0/24	❌ No direct access
DMZ	Update, backup, and endpoint management servers	192.168.2.0/24	⚙️ Limited (update-only)
Outside	Internet uplink	192.168.1.0/24	✅
Management	Out-of-band management for FTD/FMC	192.168.10.0/24	🔐 Admin-only

Cisco FTD sits between Inside, DMZ, and Outside zones — enforcing NAT, Access Control Policies, and traffic inspection.

Segmentation follows Purdue Level 3 (Inside) → Level 3.5 (DMZ) → Level 4 (Outside).

⚙️ Core Components
🧱 Cisco FTD (Routed Mode)

Managed locally using FDM.

Interfaces:

g0/0 → Outside (192.168.1.200/24)

g0/1 → Inside (192.168.100.1/24)

g0/2 → DMZ (192.168.2.1/24)

eth0 → Management (192.168.10.1/32)

Access Control Policies:

Inside → DMZ → Allow (Acronis, WSUS, Trellix)

Inside → Internet → Deny

DMZ → Internet → Allow (limited)

Default → Deny all

🧩 Active Directory Setup

Primary DC (192.168.100.10): Hosts AD DS, DNS, and DHCP.

Secondary DC (192.168.100.20): Replicates AD/DNS, configured as failover for DHCP.

Domain: Shadow.Project

DHCP Failover Mode: Hot Standby.

🗂️ Organizational Units (OU)
Shadow.Project
├── Clients
│   ├── Admins
│   │   ├── OT_Admins
│   │   └── IT_Admins
│   └── Non-Admins
│       ├── Operators
│       └── Engineers

🧠 GPO Hardening (OT Focus)

Implemented strict security baselines for non-admin clients, focusing on Kiosk-style lockdown.

Control Area	Description
CMD & PowerShell	Disabled via SRP/AppLocker
Password & Account Policies	Enforced strong password & lockout rules
Registry & Control Panel	Access restricted
USB Devices	Deny read/write/execute
Wi-Fi/Bluetooth	Disabled through device installation restrictions
Desktop & Taskbar	Minimal, clean interface (no icons, no right-click)
Auto-launch App	Calculator opens automatically at logon (can be replaced with SCADA/HMI app)
Search Bar Removal	Implemented via Registry preference
🧱 DMZ Services
🌐 WSUS (Standalone Mode)

IP: 192.168.2.10

Fetches updates from Microsoft Update.

Serves clients in Inside zone via GPO (http://192.168.2.10:8530).

Not domain-joined → reduces AD exposure.

Automatic approvals for critical/security updates.

🛡️ Trellix (ePO 5.10 + ENS 10.6.1)

Deployment Model: ePO-managed centralized solution.

Components:

Trellix Agent

ENS Modules: Threat Prevention, Web Control

Rogue System Detection (RSD)

Device Control (DLP)

Solidcore (Application & Change Control)

Integrated with SQL Server 2014 SP3 (TLS 1.2 compliant).

Centralized policy and DAT file management.

💾 Acronis Backup

Installed as offline enterprise server in Inside zone.

Activation via offline registration file.

Custom Protection Plan created for agent-based backups.

Agent Deployment: manual and discovery-based.

Backup + Anti-malware testing validated.

🔐 Defense-in-Depth Summary
Layer	Control	Implementation
Network Segmentation	Inside, DMZ, Outside, Management	Cisco FTD zoning
Access Control	Least privilege	FMC/FDM ACPs
Update Isolation	WSUS & Trellix in DMZ	Controlled traffic flows
Backup Resilience	Acronis offline mode	No Internet dependency
AD Security	Multi-DC replication & GPO hardening	Restricted privilege model
🧰 Tools & Technologies

Cisco FTD / FDM

Windows Server 2019

Active Directory, DNS, DHCP

Group Policy Management

Acronis Cyber Protect

Trellix ePO / ENS / Solidcore

WSUS

SQL Server 2014 SP3

Windows 10 Clients

📊 Project Goals

Demonstrate secure data flow and zone isolation.

Apply ISA/IEC-62443 segmentation and least privilege design.

Practice server hardening and endpoint lockdown in domain environment.

🧩 Lessons Learned

Always allocate sufficient resources and storage before multi-server deployment.

DMZ isolation is critical — avoid domain-joining external-facing servers.

Validate GPOs in test OUs before production linking.

Implement redundant DHCP & DNS for resilience.

Maintain offline activation methods for OT environments.

Monitor synchronization (AD, WSUS, Acronis, Trellix) regularly for consistency.

