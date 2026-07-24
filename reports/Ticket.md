<div align="center">

# 🎫 Enterprise-Endpoint-Asset-Management

**Real-world IT support scenarios · Documented solutions · Enterprise environment**

![Hardware](https://img.shields.io/badge/Hardware-Inventory-FF6B35?style=for-the-badge&logo=dell&logoColor=white)
![Software](https://img.shields.io/badge/Software-Deployment-0078D6?style=for-the-badge&logo=windows&logoColor=white)
![PowerShell](https://img.shields.io/badge/PowerShell-Administration-5391FE?style=for-the-badge&logo=powershell&logoColor=white)
![Security](https://img.shields.io/badge/Windows-Security-D83B01?style=for-the-badge&logo=windows&logoColor=white)
![Status](https://img.shields.io/badge/Status-In_Progress-yellow?style=for-the-badge)

</div>

---

## 📌 Overview

This repository documents a series of **real-world IT support tickets** resolved in an enterprise Windows environment. Each ticket covers a distinct category of IT administration — from hardware inventory to security hardening — and includes the tools used, steps taken, and lessons learned.

---

## 📊 Ticket Summary

| Ticket ID | Category | Priority | Status |
|-----------|----------|----------|--------|
| [IT-302](#it-302--hardware-inventory) | Hardware Inventory | 🔴 High | 🟡 In Progress |
| [IT-303](#it-303--software-inventory) | Software Inventory | 🔴 High | 🟡 In Progress |
| [IT-304](#it-304--software-deployment) | Software Deployment | 🔴 High | 🟡 In Progress |
| [IT-305](#it-305--windows-administration) | Windows Administration | 🔴 High | 🟡 In Progress |
| [IT-306](#it-306--windows-security) | Windows Security | 🔴 High | 🟡 In Progress |
| [IT-307](#it-307--powershell-administration) | PowerShell Administration | 🔴 High | 🟡 In Progress |
| [IT-308](#it-308--windows-maintenance--troubleshooting) | Windows Maintenance & Troubleshooting | 🔴 High | 🟡 In Progress |

---

## 🎫 IT-302 — Hardware Inventory

| Field | Details |
|-------|---------|
| **Ticket ID** | IT-302 |
| **Category** | Hardware Inventory |
| **Priority** | High |
| **Status** | In Progress |

### Description
Conduct a full hardware inventory of all workstations and servers in the environment. Document CPU, RAM, storage, and NIC specifications for each device.

### Tools Used
- `msinfo32` — System Information
- `wmic` — Windows Management Instrumentation
- PowerShell `Get-WmiObject`
- Device Manager

### Steps
1. Run `msinfo32` to capture full system specs
2. Use `wmic cpu get name,numberofcores` for CPU details
3. Use `wmic memorychip get capacity` for RAM
4. Export results to CSV for tracking
5. Update asset inventory spreadsheet

---

## 🎫 IT-303 — Software Inventory

| Field | Details |
|-------|---------|
| **Ticket ID** | IT-303 |
| **Category** | Software Inventory |
| **Priority** | High |
| **Status** | In Progress |

### Description
Audit all installed software across managed workstations. Identify unauthorized, outdated, or unlicensed applications.

### Tools Used
- PowerShell `Get-ItemProperty`
- `wmic product get name,version`
- Windows Apps & Features

### Steps
1. Run PowerShell to list all installed programs
2. Export to CSV for review
3. Flag unauthorized or unlicensed software
4. Cross-reference with approved software list
5. Document findings and escalate if needed

```powershell
Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* |
Select-Object DisplayName, DisplayVersion, Publisher |
Export-Csv -Path "software_inventory.csv" -NoTypeInformation
```

---

## 🎫 IT-304 — Software Deployment

| Field | Details |
|-------|---------|
| **Ticket ID** | IT-304 |
| **Category** | Software Deployment |
| **Priority** | High |
| **Status** | In Progress |

### Description
Deploy approved software packages to target workstations using silent installation methods.

### Tools Used
- PowerShell
- MSI / MSIX packages
- Group Policy Software Installation
- `msiexec.exe`

### Steps
1. Obtain approved installer package
2. Test silent install on isolated VM
3. Deploy via GPO or PowerShell remoting
4. Verify installation on target machines
5. Document deployment and update inventory

```powershell
# Silent MSI install example
Start-Process msiexec.exe -ArgumentList '/i "C:\Packages\app.msi" /qn /norestart' -Wait
```

---

## 🎫 IT-305 — Windows Administration

| Field | Details |
|-------|---------|
| **Ticket ID** | IT-305 |
| **Category** | Windows Administration |
| **Priority** | High |
| **Status** | In Progress |

### Description
Perform routine Windows administration tasks including user account management, service configuration, and system settings.

### Tools Used
- Computer Management (`compmgmt.msc`)
- Services (`services.msc`)
- Local Users and Groups
- Registry Editor
- Event Viewer

### Steps
1. Review Event Viewer for errors and warnings
2. Manage local user accounts and permissions
3. Configure and verify services startup type
4. Apply system settings per IT policy
5. Document all changes made

---

## 🎫 IT-306 — Windows Security

| Field | Details |
|-------|---------|
| **Ticket ID** | IT-306 |
| **Category** | Windows Security |
| **Priority** | High |
| **Status** | In Progress |

### Description
Harden workstation security settings — disable unnecessary services, configure Windows Firewall, and enforce security policies.

### Tools Used
- Windows Defender
- Windows Firewall (`wf.msc`)
- Local Security Policy (`secpol.msc`)
- PowerShell

### Steps
1. Enable and configure Windows Defender
2. Review and tighten Windows Firewall rules
3. Disable SMBv1 and unnecessary legacy protocols
4. Configure account lockout policy
5. Audit local administrator accounts

```powershell
# Disable SMBv1
Set-SmbServerConfiguration -EnableSMB1Protocol $false -Force

# Check Windows Defender status
Get-MpComputerStatus | Select-Object AMRunningMode, RealTimeProtectionEnabled
```

---

## 🎫 IT-307 — PowerShell Administration

| Field | Details |
|-------|---------|
| **Ticket ID** | IT-307 |
| **Category** | PowerShell Administration |
| **Priority** | High |
| **Status** | In Progress |

### Description
Automate repetitive IT tasks using PowerShell scripts — user management, reporting, and system configuration.

### Tools Used
- PowerShell 5.1 / 7.x
- PowerShell ISE / VS Code
- Task Scheduler

### Scripts Used

```powershell
# List all local users and their status
Get-LocalUser | Select-Object Name, Enabled, LastLogon

# Get system uptime
(Get-Date) - (gcim Win32_OperatingSystem).LastBootUpTime

# Check disk space on all drives
Get-PSDrive -PSProvider FileSystem |
Select-Object Name, @{N='Used(GB)';E={[math]::Round($_.Used/1GB,2)}},
@{N='Free(GB)';E={[math]::Round($_.Free/1GB,2)}}
```

---

## 🎫 IT-308 — Windows Maintenance & Troubleshooting

| Field | Details |
|-------|---------|
| **Ticket ID** | IT-308 |
| **Category** | Windows Maintenance & Troubleshooting |
| **Priority** | High |
| **Status** | In Progress |

### Description
Perform routine maintenance and resolve reported issues — disk cleanup, Windows Update, system file repair, and performance troubleshooting.

### Tools Used
- `sfc /scannow` — System File Checker
- `DISM` — Deployment Image Servicing
- Disk Cleanup / Storage Sense
- Windows Update
- Task Manager / Resource Monitor

### Steps
1. Run Windows Update and install pending patches
2. Run `sfc /scannow` to check system file integrity
3. Run DISM to repair Windows image if needed
4. Clear temp files and run Disk Cleanup
5. Review Task Manager for resource issues

```powershell
# System File Checker
sfc /scannow

# DISM repair
DISM /Online /Cleanup-Image /RestoreHealth

# Clear temp files
Remove-Item -Path "$env:TEMP\*" -Recurse -Force -ErrorAction SilentlyContinue
```

---

## 💻 Skills Demonstrated

`Hardware Inventory` `Software Auditing` `Software Deployment` `Windows Administration`
`Windows Security Hardening` `PowerShell Scripting` `System Maintenance` `Troubleshooting`
`SMBv1 Hardening` `Event Viewer` `GPO` `MSI Deployment` `DISM` `SFC`

---

## 👨‍💻 Author

**Mohammad Salem Hassani**
IT Support | System Administration | PowerShell | Windows Security | Montreal, QC 🇨🇦

---

<div align="center">
⭐ Star this repo if it helped you prepare for your IT support role
</div>
