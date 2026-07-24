<div align="center">

# 📦 Enterprise Endpoint & Asset Management Lab

**A complete workstation deployment simulation for Northwind Technologies**

![Windows 11](https://img.shields.io/badge/Windows_11-Enterprise-0078D6?style=for-the-badge&logo=windows&logoColor=white)
![Windows Server](https://img.shields.io/badge/Windows_Server-2022-0078D6?style=for-the-badge&logo=windows&logoColor=white)
![PowerShell](https://img.shields.io/badge/PowerShell-Administration-5391FE?style=for-the-badge&logo=powershell&logoColor=white)
![VirtualBox](https://img.shields.io/badge/VirtualBox-Lab-183A61?style=for-the-badge&logo=virtualbox&logoColor=white)
![Status](https://img.shields.io/badge/Status-Complete-success?style=for-the-badge)

</div>

---

## 📌 Overview

This project simulates a **full enterprise workstation deployment** for a new employee at Northwind Technologies — from hardware verification through final validation.

Built to replicate the real-world responsibilities of an IT Support Technician: inventory, software deployment, security review, PowerShell reporting, and Windows maintenance — all documented with screenshots and generated reports.

---

## 📊 At a Glance

| | |
|---|---|
| 🖥️ **Client OS** | Windows 11 Enterprise |
| 🖧 **Server OS** | Windows Server 2022 |
| 🌐 **Domain** | northwind.local |
| 🔧 **Hypervisor** | VirtualBox |
| 📋 **Phases** | 9 |
| 📄 **Reports generated** | 6 |

---

## 🏢 Business Scenario

> Northwind Technologies has hired a new employee. Before delivering the workstation, the IT department must verify hardware, document software, install required applications, review Windows security, validate system health, and produce deployment documentation.

---

## 📂 Deployment Phases

| Phase | Name | Key Tasks |
|-------|------|-----------|
| 1 | **Computer Inventory** | System info · hostname · specs baseline |
| 2 | **Hardware Inventory** | CPU · RAM · storage · BIOS · Device Manager |
| 3 | **Software Inventory** | List installed apps · audit versions · flag unauthorized |
| 4 | **Software Deployment** | Silent install · verify · document |
| 5 | **Windows Features** | Enable/disable features · optional components |
| 6 | **Windows Security** | Defender · Firewall · SMBv1 · account lockout |
| 7 | **PowerShell Administration** | Reports: services · processes · network · users |
| 8 | **Windows Maintenance** | SFC · DISM · CHKDSK · Event Viewer · Disk Cleanup |
| 9 | **Final Validation** | Full checklist · sign-off · handover documentation |

---

## 🛠️ Phase Details

### Phase 1 — Computer Inventory
```powershell
# Full system information
Get-ComputerInfo | Select-Object CsName, WindowsVersion, OsArchitecture,
    CsProcessors, CsTotalPhysicalMemory | Format-List

# Basic system info
systeminfo | findstr /C:"Host Name" /C:"OS Name" /C:"Total Physical Memory"
```

---

### Phase 2 — Hardware Inventory
```powershell
# CPU
Get-WmiObject Win32_Processor |
    Select-Object Name, NumberOfCores, NumberOfLogicalProcessors, MaxClockSpeed

# RAM
Get-WmiObject Win32_PhysicalMemory |
    Select-Object Manufacturer, Capacity, Speed |
    ForEach-Object { $_.Capacity = "$([math]::Round($_.Capacity/1GB,2)) GB"; $_ }

# Storage
Get-WmiObject Win32_DiskDrive |
    Select-Object Model, @{N='Size(GB)';E={[math]::Round($_.Size/1GB,2)}}

# BIOS
Get-WmiObject Win32_BIOS |
    Select-Object Manufacturer, Name, Version, ReleaseDate

# Network Adapters
Get-NetAdapter | Select-Object Name, InterfaceDescription, Status, MacAddress
```

---

### Phase 3 — Software Inventory
```powershell
# All installed software
Get-ItemProperty HKLM:\Software\Microsoft\Windows\CurrentVersion\Uninstall\* |
    Select-Object DisplayName, DisplayVersion, Publisher, InstallDate |
    Where-Object { $_.DisplayName -ne $null } |
    Sort-Object DisplayName |
    Export-Csv -Path ".\reports\Software_Inventory.csv" -NoTypeInformation

# Windows Store apps
Get-AppxPackage | Select-Object Name, Version, Publisher |
    Export-Csv -Path ".\reports\Store_Apps.csv" -NoTypeInformation
```

---

### Phase 4 — Software Deployment
```powershell
# Silent MSI installation
Start-Process msiexec.exe `
    -ArgumentList '/i "C:\Packages\application.msi" /qn /norestart /log C:\Logs\install.log' `
    -Wait -NoNewWindow

# Verify installation
Get-Package -Name "ApplicationName" | Select-Object Name, Version, Source

# Using winget (Windows Package Manager)
winget install --id Microsoft.VisualStudioCode --silent --accept-package-agreements
```

---

### Phase 5 — Windows Features
```powershell
# List all optional features and their state
Get-WindowsOptionalFeature -Online |
    Select-Object FeatureName, State |
    Sort-Object State, FeatureName

# Enable a feature
Enable-WindowsOptionalFeature -Online -FeatureName "TelnetClient" -NoRestart

# Disable SMBv1 (security best practice)
Disable-WindowsOptionalFeature -Online -FeatureName "SMB1Protocol" -NoRestart
```

---

### Phase 6 — Windows Security
```powershell
# Windows Defender status
Get-MpComputerStatus |
    Select-Object AMRunningMode, RealTimeProtectionEnabled,
    AntivirusSignatureLastUpdated, QuickScanAge

# Firewall status on all profiles
Get-NetFirewallProfile | Select-Object Name, Enabled, DefaultInboundAction

# Disable SMBv1
Set-SmbServerConfiguration -EnableSMB1Protocol $false -Force

# Account lockout policy
net accounts

# List local administrators
Get-LocalGroupMember -Group "Administrators" |
    Select-Object Name, PrincipalSource, ObjectClass

# Check for accounts with no password required
Get-LocalUser | Where-Object { $_.PasswordRequired -eq $false } |
    Select-Object Name, Enabled
```

---

### Phase 7 — PowerShell Administration
```powershell
# ── Services Report ──────────────────────────────────────────
Get-Service |
    Select-Object Name, DisplayName, Status, StartType |
    Sort-Object Status |
    Export-Csv -Path ".\reports\Services_Report.csv" -NoTypeInformation

# ── Processes Report ─────────────────────────────────────────
Get-Process |
    Select-Object Name, Id,
        @{N='CPU(s)';E={[math]::Round($_.CPU,2)}},
        @{N='Memory(MB)';E={[math]::Round($_.WorkingSet/1MB,2)}} |
    Sort-Object 'Memory(MB)' -Descending |
    Export-Csv -Path ".\reports\Processes_Report.csv" -NoTypeInformation

# ── Network Report ───────────────────────────────────────────
Get-NetAdapter | Select-Object Name, Status, MacAddress, LinkSpeed |
    Export-Csv -Path ".\reports\Network_Report.csv" -NoTypeInformation

Get-NetIPAddress | Select-Object InterfaceAlias, IPAddress, PrefixLength |
    Export-Csv -Path ".\reports\IP_Report.csv" -NoTypeInformation

# ── Local Users Report ───────────────────────────────────────
Get-LocalUser |
    Select-Object Name, Enabled, LastLogon, PasswordRequired, PasswordLastSet |
    Export-Csv -Path ".\reports\LocalUsers_Report.csv" -NoTypeInformation

# ── Disk Space Report ────────────────────────────────────────
Get-PSDrive -PSProvider FileSystem |
    Select-Object Name,
        @{N='Used(GB)';E={[math]::Round($_.Used/1GB,2)}},
        @{N='Free(GB)';E={[math]::Round($_.Free/1GB,2)}},
        @{N='Total(GB)';E={[math]::Round(($_.Used+$_.Free)/1GB,2)}} |
    Export-Csv -Path ".\reports\DiskSpace_Report.csv" -NoTypeInformation
```

---

### Phase 8 — Windows Maintenance
```powershell
# System File Checker
sfc /scannow

# DISM — Check and repair Windows image
DISM /Online /Cleanup-Image /CheckHealth
DISM /Online /Cleanup-Image /ScanHealth
DISM /Online /Cleanup-Image /RestoreHealth

# CHKDSK — Check disk for errors
chkdsk C: /f /r /x

# Clear temp files
Remove-Item -Path "$env:TEMP\*" -Recurse -Force -ErrorAction SilentlyContinue
Remove-Item -Path "C:\Windows\Temp\*" -Recurse -Force -ErrorAction SilentlyContinue

# Windows Update via PowerShell
Install-Module PSWindowsUpdate -Force
Get-WindowsUpdate
Install-WindowsUpdate -AcceptAll -AutoReboot

# Check system uptime
(Get-Date) - (Get-CimInstance Win32_OperatingSystem).LastBootUpTime
```

---

### Phase 9 — Final Validation
```powershell
# ── Full Validation Report ───────────────────────────────────
$report = @"
========================================
  NORTHWIND TECHNOLOGIES
  Workstation Deployment Report
  Date: $(Get-Date -Format 'yyyy-MM-dd HH:mm')
========================================

SYSTEM
  Hostname   : $env:COMPUTERNAME
  Domain     : $env:USERDOMAIN
  OS         : $((Get-CimInstance Win32_OperatingSystem).Caption)
  Uptime     : $([math]::Round(((Get-Date)-(Get-CimInstance Win32_OperatingSystem).LastBootUpTime).TotalHours,1)) hours

HARDWARE
  CPU        : $((Get-CimInstance Win32_Processor).Name)
  RAM        : $([math]::Round((Get-CimInstance Win32_ComputerSystem).TotalPhysicalMemory/1GB,1)) GB
  Disk(C:)   : $([math]::Round((Get-PSDrive C).Used/1GB,1)) GB used / $([math]::Round(((Get-PSDrive C).Used+(Get-PSDrive C).Free)/1GB,1)) GB total

SECURITY
  Defender   : $((Get-MpComputerStatus).RealTimeProtectionEnabled)
  Firewall   : $((Get-NetFirewallProfile -Profile Domain).Enabled)
  SMBv1      : $((Get-SmbServerConfiguration).EnableSMB1Protocol)

STATUS     : READY FOR DELIVERY ✅
========================================
"@

$report | Out-File -FilePath ".\reports\Deployment_Report.txt"
Write-Host $report
```

---

## 📄 Generated Reports

| Report | Description |
|--------|-------------|
| `Hardware_Report.txt` | CPU, RAM, storage, BIOS, NIC details |
| `Software_Inventory.csv` | All installed applications with versions |
| `Services_Report.csv` | All services — name, status, start type |
| `Processes_Report.csv` | Running processes with CPU and memory |
| `Network_Report.csv` | Adapters, IP addresses, DNS config |
| `Deployment_Report.txt` | Final sign-off summary for handover |

---

## 💻 Skills Demonstrated

### Windows Administration
`Windows 11 Enterprise` `Windows Server 2022` `Local Users` `Services` `Windows Features`

### Hardware Inventory
`CPU` `RAM` `Storage` `BIOS` `Device Manager` `WMI` `Get-WmiObject`

### Software Management
`Software Auditing` `Silent Deployment` `MSI` `winget` `Get-Package`

### PowerShell
`Get-ComputerInfo` `Get-Service` `Get-Process` `Get-Package` `Get-NetAdapter` `Get-LocalUser` `Export-Csv` `Get-WmiObject`

### Windows Security
`Windows Defender` `Firewall` `SMBv1 Hardening` `Account Lockout` `Local Admins Audit`

### Windows Maintenance
`SFC` `DISM` `CHKDSK` `Event Viewer` `Disk Cleanup` `Windows Update` `Task Scheduler`

---

## 📁 Repository Structure

```
Enterprise-Endpoint-Asset-Management/
│
├── README.md
├── LICENSE
├── .gitignore
│
├── docs/
│   └── deployment-guide.md
├── screenshots/
│   ├── phase-1/ through phase-9/
├── reports/
│   ├── Hardware_Report.txt
│   ├── Software_Inventory.csv
│   ├── Services_Report.csv
│   ├── Processes_Report.csv
│   ├── Network_Report.csv
│   └── Deployment_Report.txt
├── diagrams/
│   └── lab-topology.png
├── scripts/
│   ├── hardware-inventory.ps1
│   ├── software-inventory.ps1
│   ├── security-check.ps1
│   ├── maintenance.ps1
│   └── final-validation.ps1
└── assets/
```

---

## 🚀 Future Improvements

- [ ] Microsoft Intune — MDM policy management
- [ ] Microsoft Entra ID — cloud identity
- [ ] Windows Autopilot — zero-touch deployment
- [ ] Microsoft 365 administration
- [ ] PowerShell automation scripts for full deployment

---

## 📜 License

This project is licensed under the [MIT License](LICENSE).

---

## 👨‍💻 Author

**Mohammad Salem Hassani**
IT Support | System Administration | PowerShell | Active Directory | Cybersecurity
🇨🇦 Montreal, Quebec, Canada

[![LinkedIn](https://img.shields.io/badge/LinkedIn-Connect-0077B5?style=flat&logo=linkedin)](https://linkedin.com/in/mohammad-salem-hassani)

---

<div align="center">
⭐ Star this repo if it helped you prepare for your IT support role
</div>
