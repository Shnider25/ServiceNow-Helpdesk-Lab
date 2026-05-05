# ServiceNow-Helpdesk-Lab
Hands-on ServiceNow ITSM lab — incident ticketing, priority classification, and full ticket lifecycle management using a free Personal Developer Instance (PDI). Based on real homelab infrastructure scenarios.


# 🎫 ServiceNow Helpdesk Lab — SB Studio IT Service Desk

**Platform:** ServiceNow Personal Developer Instance (PDI)  
**Instance:** dev307832 | **Release:** Australia (Latest)  
**Project Type:** IT Service Management (ITSM) — Helpdesk Simulation  
**Status:** ✅ Active

---

## 📋 Project Overview

This project simulates a real-world IT helpdesk environment built inside a **free ServiceNow Personal Developer Instance (PDI)**. The fictional company is **SB Studio** — a recording studio with a self-hosted homelab infrastructure. All tickets are based on real technical scenarios encountered while building and maintaining the homelab.

**Skills demonstrated:**
- Incident lifecycle management (Create → Assign → Resolve)
- Priority and urgency classification
- Root cause analysis and resolution documentation
- Assignment group routing
- ITSM best practices used in enterprise environments

---

## 🖥️ Incident Queue

<p>
<img src="" height="80%" width="80%" alt="ServiceNow Incident Queue — All 5 SB Studio Tickets"/>
</p>

---

## 🏗️ Lab Infrastructure

| Device | Role | OS |
|---|---|---|
| HP ProDesk 600 G1 | Proxmox node (pve-main) | Proxmox VE |
| HP 15 Laptop | Proxmox node (pve2) | Proxmox VE |
| Dell Laptop | Proxmox node (pve) | Proxmox VE |
| TrueNAS SCALE VM | Network Attached Storage | TrueNAS SCALE |
| Ubuntu Docker VM | Self-hosted services | Ubuntu + Docker |
| Mac Mini | Primary workstation | macOS |
| Canon PIXMA MG5320 | Network printer | N/A |

**Self-hosted services:** Nextcloud · Portainer · Tailscale · Nginx Proxy Manager

---

## 🎫 Incident Tickets

### INC0010001 — Canon PIXMA MG5320 Printer Not Responding on Network

| Field | Value |
|---|---|
| **Category** | Hardware |
| **Priority** | 4 - Low |
| **Urgency** | 2 - Medium |
| **State** | ✅ Resolved |
| **Resolved by** | Shnider Baptiste |

**Description:**  
Printer at SB Studio is connected to the 2.4GHz network but is not showing as available from the Mac Mini or any other device. Need to verify network connectivity and assign a static IP reservation on the router.

**Resolution:**  
Verified Canon PIXMA MG5320 is connected to the 2.4GHz network. Printer was assigned a dynamic IP that changed after a router restart. Resolved by logging into the router and creating a static DHCP reservation tied to the printer's MAC address. Printer is now consistently reachable on the network.

---

### INC0010002 — TrueNAS SCALE VM Unreachable

| Field | Value |
|---|---|
| **Category** | Network |
| **Priority** | 🔴 1 - Critical |
| **Urgency** | 1 - High |
| **Impact** | 1 - High |
| **Assignment Group** | Network |
| **State** | ✅ Resolved |
| **Resolved by** | Shnider Baptiste |

**Description:**  
TrueNAS SCALE VM hosted on HP ProDesk (pve-main) is not responding. SMB shares for sb-studio, sb-pictures, and sb-backup are all inaccessible from the Mac Mini. Need to verify VM is running in Proxmox and check network passthrough configuration.

**Resolution:**  
Verified TrueNAS SCALE VM was in a stopped state in Proxmox. Started the VM from the Proxmox web UI on pve-main. TrueNAS came back online and all SMB shares are now accessible from the Mac Mini. Root cause was the VM did not auto-start after a Proxmox host reboot. Enabled the auto-start option on the VM to prevent recurrence.

<p>
<img src="" height="80%" width="80%" alt="INC0010002 — Critical Ticket Resolved with Resolution Notes"/>
</p>

<p>
<img src="" height="80%" width="80%" alt="INC0010002 — Activity Log Showing Full Ticket Lifecycle"/>
</p>

---

### INC0010003 — Proxmox Node pve2 Not Joining Cluster

| Field | Value |
|---|---|
| **Category** | Hardware |
| **Priority** | 3 - Moderate |
| **Urgency** | 2 - Medium |
| **Impact** | 2 - Medium |
| **Assignment Group** | Hardware |
| **State** | ✅ Resolved |
| **Resolved by** | Shnider Baptiste |

**Description:**  
HP 15 laptop running Proxmox VE is not joining the 3-node cluster. Node shows as offline in the Proxmox web UI. Need to verify KVM/AMD-V is enabled in BIOS and check cluster quorum status.

**Resolution:**  
Logged into HP 15 laptop (pve2) BIOS and confirmed AMD-V virtualization was disabled. Enabled AMD-V and rebooted. Node successfully rejoined the 3-node Proxmox cluster. Verified cluster quorum is healthy with all 3 nodes online. Added `nomodeset` boot parameter to prevent kernel panic on next restart.

---

### INC0010004 — Nextcloud Trusted Domain Error After Ubuntu VM IP Change

| Field | Value |
|---|---|
| **Category** | Software |
| **Priority** | 3 - Moderate |
| **Urgency** | 2 - Medium |
| **Impact** | 2 - Medium |
| **Assignment Group** | Software |
| **State** | ✅ Resolved |
| **Resolved by** | Shnider Baptiste |

**Description:**  
Nextcloud instance on Ubuntu Docker VM is throwing a trusted domain error after the VM was rebuilt and received a new IP address. Users cannot access Nextcloud from the browser.

**Resolution:**  
Ubuntu Docker VM was rebuilt and assigned a new IP address. Nextcloud was blocking access due to an untrusted domain error. Resolved by running the following command inside the Nextcloud container:

```bash
php occ config:system:set trusted_domains 1 --value=[VM-IP]
```

Reconfigured the external SFTP storage connection pointing to TrueNAS. Nextcloud is now accessible and external storage is reconnected.

<p>
<img src="" height="80%" width="80%" alt="INC0010004 — Nextcloud Software Ticket Resolved"/>
</p>

---

### INC0010005 — Mac Mini Unable to Mount SMB Share from TrueNAS

| Field | Value |
|---|---|
| **Category** | Network |
| **Priority** | 4 - Low |
| **Urgency** | 3 - Low |
| **Impact** | 3 - Low |
| **Assignment Group** | Network |
| **State** | ✅ Resolved |
| **Resolved by** | Shnider Baptiste |

**Description:**  
Mac Mini cannot connect to SMB shares on TrueNAS. Shares sb-studio and sb-pictures are not mounting. Need to verify SMB service is running on TrueNAS and check credentials.

**Resolution:**  
Verified SMB service was running on TrueNAS SCALE. Issue was caused by cached incorrect credentials on the Mac. Resolved by opening Keychain Access on Mac Mini, removing old TrueNAS credentials, and reconnecting via Finder using the TrueNAS admin account. Shares now mounting correctly and configured to reconnect at login.

---

## 📊 Summary

<p>
<img src="" height="80%" width="80%" alt="Final Incident Queue — All 5 Tickets Resolved"/>
</p>

| Ticket | Description | Priority | Category | Status |
|---|---|---|---|---|
| INC0010001 | Printer not responding on network | 4 - Low | Hardware | ✅ Resolved |
| INC0010002 | TrueNAS VM unreachable | 🔴 1 - Critical | Network | ✅ Resolved |
| INC0010003 | Proxmox node not joining cluster | 3 - Moderate | Hardware | ✅ Resolved |
| INC0010004 | Nextcloud trusted domain error | 3 - Moderate | Software | ✅ Resolved |
| INC0010005 | Mac Mini SMB share not mounting | 4 - Low | Network | ✅ Resolved |

**Total Tickets:** 5 &nbsp;|&nbsp; **Resolved:** 5 &nbsp;|&nbsp; **Resolution Rate:** 100%

---

## 🛠️ Skills Demonstrated

- **Incident Management** — Full ticket lifecycle from creation to resolution
- **Priority & Impact Classification** — Correctly categorized tickets by urgency and business impact
- **Root Cause Analysis** — Identified underlying causes, not just symptoms
- **ITSM Best Practices** — Assignment groups, resolution codes, detailed notes
- **Cross-platform Troubleshooting** — macOS, Linux (Proxmox, TrueNAS, Ubuntu), networking
- **Virtualization** — Proxmox VE cluster management and VM configuration
- **Network Storage** — TrueNAS SCALE, SMB shares, NAS connectivity
- **Self-hosted Services** — Nextcloud, Docker, Portainer

---

## 📈 Next Steps

- [ ] Add homelab devices to ServiceNow CMDB as Configuration Items
- [ ] Build incident dashboard with ticket metrics
- [ ] Create SLA rules for ticket response times
- [ ] Set up Flow Designer automation for ticket routing
- [ ] Pursue ServiceNow Certified System Administrator (CSA) certification

---

*Built by Shnider Baptiste · SB Studio Homelab · 2026*
