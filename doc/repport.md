# Deployment Report: SOC Infrastructure & Active Directory
**Project:** Enterprise Infrastructure & Security Operations Center (SOC)  
**Organization:** Securinets ENIT  
**Date:** 2026 
**Status:** Phase 2 Completed 

---

## 1. Introduction
This document serves as the technical validation report for **Phases 1 ,2 and 3**. It details the deployment of the virtualized infrastructure, network segmentation, and the configuration of the Active Directory identity core for the `securinetsenit.local` domain.

### 1.1 Project Team
* **Mouhamed Oussema Chelly** – Team Leader
* **Amir Khammar**
* **Rayen Mabsout**
* **Karim Dami**
* **Montaha Jmai**
* **Mariem Idoudi**

---

## 2. Phase 1: Infrastructure & Network Configuration
**Objective:** Establish strict segmentation between the external network (WAN) and the simulated enterprise network (LAN) using a firewall-based architecture.

### 2.1 Virtual Network Adapters
The infrastructure utilizes specific connection modes to ensure isolation and connectivity.

| Virtual Machine | Connection Mode |
| :--- | :--- |
| **OPNsense Firewall** | NAT / Internal Network |
| **Windows Server 2022** | Internal Network |
| **Windows 10 Client** | Internal Network |

### 2.2 OPNsense Interface Configuration
The OPNsense firewall acts as the central gateway.
* **WAN Interface:** Configured via **DHCP (IPv4)** to receive an upstream IP address for internet access (NAT).
* **LAN Interface:** Configured with a **Static IP** (`10.10.10.1`) to serve as the gateway.

*Credentials:*
* **User:** `root`
* **Password:** `opnsense`

**Proof of OPNsense Interface Configuration:**  
![OPNsense Interface Configuration](../images/49ac0e8b-0cdf-44f8-9e12-3322f43be283.jpg)

### 2.3 Internal IP Addressing (DHCP)
Instead of manual static assignment for every client, we configured a **DHCP Service** on the LAN interface.
* **Network Range:** `10.10.10.0/24`
* **DHCP Scope:** Assigns IP addresses automatically to all VMs connected to the LAN segment.
* **Gateway:** `10.10.10.1`

**Proof of IP Configuration (Server):**  
![Server IP Configuration](../images/servr_ip.jpg)

**Proof of IP Configuration (Client):**  
![Client IP Configuration](../images/client.jpg)

### 2.4 Connectivity Validation
To validate the routing and DHCP configuration, we performed cross-VM ping tests:
1.  **Inter-VM:** Windows 10 Client successfully pings Windows Server.
2.  **Gateway:** Both VMs successfully ping the OPNsense LAN IP (`10.10.10.1`).

**Proof of Connectivity (Ping Tests):**  
![Connectivity Test](../images/ping_vms.jpg)

---

## 3. Phase 2: Active Directory & GPO Strategy
**Objective:** Deploy the identity management system and enforce security baselines as per the specifications.

### 3.1 Domain Configuration
* **Root Domain Name:** `securinetsenit.local`
* **Forest Functional Level:** Windows Server 2022
![Active Directory domain name](../images/AD_domain_name.png)

### 3.2 Organizational Unit (OU) Structure
We implemented the OU hierarchy defined in the specification to facilitate granular GPO application.

**Proof of Active Directory Structure:**  
![Active Directory Structure](../images/3bf14c0f-ebab-45fd-9437-54e40439f1bc.jpg)


## 3.3 Group Policy Objects (GPO) Implementation (Completed)

The Group Policy strategy has been fully implemented and verified. All security baselines are now active across the `securinetsenit.local` domain.

### A. Default Domain Policy 
* **Target:** All Users / Computers  
* **Status:** **Applied** * **Key Settings:**
    * Password Complexity: Enabled
    * Min Password Length: 14 characters
    * Lockout Threshold: 5 invalid attempts
    * Kerberos Ticket Lifetime: 10 hours

**Proof of Default Domain Policy Configuration:** ![Default Domain Policy](../images/gpos_acc.png)

---

### B. User Standard Security & OU Structure
* **Target:** `USERS/Departments` (Finance, Marketing, IT)  
* **Status:** **Applied** * **Settings:**
    * Enforce screen lock after **10 minutes** idle.
    * Restricted access to Control Panel and CMD for non-IT users.
    * Application of branded desktop environment.

**Proof of Group Policy Management & OU Structure:** ![User-Standard Security GPO](../images/GPOs_1.png)

---

### C. Computer Hardening (Local & Domain Policies)
* **Target:** `COMPUTERS/Workstations`  
* **Status:** **Applied** * **Settings:**
    * Removal of local admin privileges.
    * Disabling of Guest accounts and USB Auto-run.
    * Secure channel signing enforcement.

**Proof of Local Group Policy Editor Configuration:** ![Local Policy Editor](../images/local_policy.png)

---

### D. Server Security Baseline & LDAP Hardening - Windows Defender with Advanced Security
As a critical hardening measure, we have configured **Windows Defender Firewall** via GPO to protect core services.

* **Configured Rules:** Specific Inbound/Outbound rules for **LDAP**, Domain Controller communication, and DNS resolution.
* **Objective:** Prevent unauthorized lateral movement and ensure only encrypted directory traffic is allowed.
* **Status:** **Applied** * **Settings:**
    * **LDAP Signing:** Enforced to secure directory traffic.
    * NTLMv2 enforcement and anonymous SID enumeration disabled.
    * Audit policies enabled for object access.

**Proof of Firewall Configuration (LDAP & Domain Rules):** ![Firewall Configuration](../images/firewall.png)

---



## 4. Conclusion
Phase 2 is now successfully completed. The infrastructure features a functional Active Directory domain with a robust security layer enforced by GPOs and advanced firewall rules.

**Status Summary:**
* [x] VMs & Network Configuration (Phase 1)
* [x] Domain Controller Promotion (Phase 2)
* [x] OU Structure & User Accounts (Phase 2)
* [x] **Full GPO & Firewall Hardening (Phase 2 - COMPLETED)**

**Next Steps:**
* Begin **Phase 3**: Perimeter Security (IDS/IPS) with OPNsense.
* Deploy and tune Zenarmor/Suricata rules.

