# Enterprise Virtual Lab Architecture

---

## Overview
This lab simulates an **enterprise-grade virtual infrastructure** designed to reflect real-world production environments. The primary focus areas include **network segmentation, security, centralized management, monitoring, backup, and disaster recovery**.

The environment is built on **VMware ESXi**, utilizing clustered hosts for production workloads and a dedicated **Disaster Recovery (DR) host**. Strict access controls, VLAN isolation, and centralized firewalling ensure strong security boundaries.

This lab is designed for:
- Enterprise infrastructure design practice  
- Network and security segmentation testing  
- Monitoring, backup, and disaster recovery validation  
- Hands-on experience with production-style controls  

---
<img width="804" height="800" alt="Enterprise Virtual Lab Architecture Diagram" src="https://raw.githubusercontent.com/suraj-1325/Isolate-Enterprise-Architecture/main/lab.drawio.png"/>

---

## High-Level Architecture
- **Compute:** VMware ESXi cluster with a standalone DR host  
- **Networking:** Cisco Catalyst switch to create a connected network 
- **Security:** OPNsense firewall, strict inter-VLAN filtering and act as Vlan Gateway 
- **Identity:** Microsoft Active Directory and AD Client
- **Monitoring:** Wazuh SIEM  
- **Backup & Storage:** Veeam Backup & Replication, TrueNAS Scale  
- **Access Control:** Bastion (Jump Server) architecture  

---

## Infrastructure Design

### Hypervisors
- **Production ESXi Cluster**
  - `10.100.100.100`
  - `10.100.100.115`
  - Clustered and managed via **vCenter**
  - Supports availability and workload distribution testing

- **Disaster Recovery ESXi Host**
  - `10.100.200.200`
  - Dedicated DR-only host
  - Isolated management network (VLAN 200)
  - Used for replication and recovery testing

---

### Virtualization Management
- **vCenter Server**
  - Centralized management of ESXi hosts and virtual machines
  - Enables clustering, monitoring, and lifecycle operations

---

## Networking

### Physical Network
- **Cisco Catalyst Switch**
  - Single trusted access/Trunk port
  - Segregated management and workload traffic

### VLAN Design
| VLAN | Purpose | Description |
|----|----|----|
| 10 | Core Services | Shared infrastructure services |
| 20 | Security Testing | Vulnerable and offensive security tooling |
| 30 | Identity | Authentication and directory services |
| 40 | Monitoring / SIEM | Centralized logging and alerting |
| 50 | Backup & Storage | Backup orchestration and NAS |
| 60 | Secure Access | Jump (Bastion) Server |
| 100 | Management | ESXi and vCenter management |
| 200 | Disaster Recovery | DR ESXi management network |

Each VLAN is fully isolated, with inter-VLAN traffic strictly controlled by firewall rules.

---

## VLAN Segmentation & Services

| VLAN | Services |
|-----|---------|
| 10 | OPNsense, Web Server, DNS, Mail Server |
| 20 | DVWA, Kali Linux |
| 30 | Active Directory Domain Controller, AD Client |
| 40 | Wazuh Manager |
| 50 | Veeam Backup Server, TrueNAS Scale |
| 60 | Jump Server (Bastion Host) |
| 100 | ESXi Management Interfaces, vCenter |
| 200 | DR ESXi Host |

---

## Gateway, Routing & Internet Access
- **OPNsense** acts as:
  - Default gateway for all VLANs
  - Central firewall and routing platform
- **WAN Access**
  - Bridged to a VoIP device acting as an ISP simulator
- **Routing Model**
  - No direct internet access from VMs
  - All traffic passes through OPNsense for inspection and filtering

---

## Access Control & Security

### Physical Controls
- Only **one physical switch port** has access to all VLANs
- Port is restricted using:
  - MAC address binding
  - Single authorized laptop

### Logical Controls
- All administrative access is routed through:
  - **Jump Server (VLAN 60)**
- Direct access to servers, ESXi hosts, and management networks is blocked

This enforces a **bastion-host access model** commonly used in enterprise environments.

---

## Identity & Authentication
- **Active Directory (VLAN 30)**
  - Centralized authentication
  - Domain-joined systems
  - Foundation for role-based access control and GPOs

---

## Monitoring & Logging
- **Wazuh SIEM (VLAN 40)**
  - Centralized log aggregation
  - Security event monitoring
  - Host-based intrusion detection (HIDS)

Logs are collected from critical infrastructure and services for visibility and alerting.

---

## Backup, Storage & Data Protection
- **Veeam Backup & Replication**
  - VM-level backups
  - Scheduled and automated jobs
- **TrueNAS Scale**
  - Centralized storage
  - Backup repositories
  - Replicated datasets

Supports restore testing, backup validation, and data integrity checks.

---

## Disaster Recovery & Replication
The **DR ESXi host (VLAN 200)** is used to replicate and recover:

- OPNsense configuration
- DNS services
- Web and mail servers
- Active Directory
- TrueNAS datasets

### DR Capabilities
- Isolated recovery environment
- Non-disruptive recovery testing
- Service continuity validation

---

## Security Principles Demonstrated
- Defense in depth  
- Least privilege access  
- Strong network segmentation  
- Bastion-based administration  
- Centralized monitoring and logging  
- Backup and disaster recovery planning  

---

## Key Objectives
- Enterprise-style VLAN segmentation
- Centralized firewall and routing
- Secure administrative access
- Monitoring and SIEM integration
- Backup, restore, and DR testing
- Realistic VMware enterprise design

---

## Conclusion
This lab demonstrates a **secure, segmented, enterprise-grade virtual infrastructure** using VMware ESXi. It includes clustered compute, centralized firewalling, strict access controls, identity management, monitoring, backup, and disaster recovery.

The architecture closely mirrors real-world enterprise environments and is suitable for hands-on learning, security testing, and professional portfolio demonstration.
