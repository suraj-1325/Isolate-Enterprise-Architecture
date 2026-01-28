# Isolate-Enterprise-Architecture


Isolated Virtualized Practice Lab – Detailed Explanation

Overall Summary

This lab is a well-architected, isolated enterprise simulation that closely mirrors real corporate environments.
It provides hands-on experience with networking, security operations, virtualization, and disaster recovery, while remaining completely safe for experimentation.

Design Philosophy

The design intentionally includes segmentation, controlled exposure, monitoring, and disaster recovery, allowing you to practice:

	• Enterprise VLAN design
	• Firewall and routing
	• Red team / blue team security scenarios
	• SIEM monitoring and alerting
	• Backup, replication, and DR testing

All systems run as virtual machines, and failures, attacks, or misconfigurations are contained inside the lab.

Physical and Virtual Infrastructure-ESXi Hosts
	
	• Server1 – 10.100.100.100
		○ Primary ESXi host
		○ Hosts critical infrastructure VMs including OPNsense
		○ Has a direct physical connection to a VoIP device
		○ Serves as the Internet entry point for the lab
	• Server2 – 10.100.100.115
		○ Secondary ESXi host
		○ Hosts internal services and workloads
		○ Cluster with Server1 via Vcenter 
	• DR ESXi – 10.100.200.200
		○ Dedicated disaster recovery host
		○ Located in a separate DR management network
		○ Receives replicated copies of critical services
		
All ESXi management IPs are static to maintain consistency and avoid management-plane instability.

Internet Access and Firewall Architecture
VoIP-Based Internet Access
	
	• The VoIP device acts like an ISP handoff
	• It provides DHCP, but static is recommended.
	• A WAN IP is assigned directly to the OPNsense firewall VM
	• This connection is physically attached to Server1, via Vswitch.
	
OPNsense Firewall
	
	• Runs as a virtual machine on Server1
	• Receives its WAN IP via DHCP/Static from the VoIP device
	• Acts as:
		○ Default gateway for all VLANs
		○ Internal firewall
		○ Inter-VLAN router
	• Controls:
		○ North–south traffic (VLANs ↔ Internet)
		○ East–west traffic (VLAN ↔ VLAN)
		
The Cisco switch never sees Internet traffic, which mirrors many real-world enterprise designs where the firewall sits outside the core switching fabric.

4. Core Switching and Internal Networking
Cisco Catalyst Switch
	
	• Serves as the internal backbone
	• Responsible for:
		○ VLAN trunking
		○ VLAN isolation
		○ MAC binding (restricting access to authorized devices only)
	• Connects:
		○ Server1 (internal NIC)
		○ Server2
		○ DR ESXi
		○ Administrative laptop

The switch enforces Layer 2 separation, while OPNsense enforces Layer 3 and security policy.

VLAN Segmentation (With IDs)

Each VLAN simulates a real enterprise security zone:
VLAN Name	VLAN ID	Description
Red Team	10	Kali Linux and DVWA for offensive security testing
DMZ	20	Public-facing services: Web, DNS, Mail
Office	30	Active Directory server and domain-joined client
Backup	40	Veeam backup server and TrueNAS storage
Access	50	Jump server for controlled external access
Security	60	Wazuh SIEM for logging and alerting
DC Management	100	vCenter and ESXi host management
DR Management	200	Disaster recovery ESXi and replicated services

Each VLAN is logically isolated, and communication between them is only allowed if explicitly permitted by firewall rules.

Virtual Machines and Their Roles
Infrastructure & Services
	
	• Active Directory (VLAN 30)
Centralized identity, authentication, and domain services
	• Web / DNS / Mail (VLAN 20 – DMZ)
Simulates externally accessible enterprise services
	• Veeam & TrueNAS (VLAN 40)
Backup, snapshotting, and data protection
	• vCenter (VLAN 100)
Centralized ESXi management and orchestration
	
Security & Monitoring
	
	• Wazuh (VLAN 60)
Collects logs, detects threats, and generates alerts across the environment
	
Access & Testing
	
	• Jump Server (VLAN 50)
Controlled entry point used to simulate external or third-party access
	• Kali Linux & DVWA (VLAN 10)
Used to perform attack simulations, vulnerability testing, and lateral movement exercises

Disaster Recovery Strategy

The DR ESXi host (VLAN 200) replicates critical services, including:
	• OPNsense
	• Active Directory
	• Web, DNS, and Mail servers
	• TrueNAS storage

This allows you to:
	• Practice failover scenarios
	• Test backup integrity
	• Simulate site outages
	• Validate recovery procedures

All DR operations remain fully isolated from production networks.

Security and Isolation Model
	
	• No production systems are connected
	• All testing is contained within VMs
	• MAC binding prevents rogue devices
	• VLAN isolation limits lateral movement
	• Firewall rules enforce least privilege
	• SIEM provides visibility and detection

This makes the lab safe for aggressive testing, misconfigurations, and attack simulations.




![Uploading image.png…]()
