---
name: scan-registry
description: Deep scan of Windows Registry for malware persistence and unauthorized modifications
---

# Windows Registry Security Scan

Perform a detailed analysis of Windows Registry for security threats and unauthorized modifications.

## Registry Locations to Check

### Autostart Locations
```powershell
# User autostart
HKCU:\Software\Microsoft\Windows\CurrentVersion\Run
HKCU:\Software\Microsoft\Windows\CurrentVersion\RunOnce
HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\User Shell Folders
HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\Shell Folders

# System autostart
HKLM:\Software\Microsoft\Windows\CurrentVersion\Run
HKLM:\Software\Microsoft\Windows\CurrentVersion\RunOnce
HKLM:\Software\Microsoft\Windows\CurrentVersion\RunServices
HKLM:\Software\Microsoft\Windows\CurrentVersion\RunServicesOnce
```

### Browser Settings
```powershell
# Internet Explorer
HKCU:\Software\Microsoft\Internet Explorer\Main
HKCU:\Software\Microsoft\Internet Explorer\SearchScopes

# Microsoft Edge
HKCU:\Software\Microsoft\Edge\Main
HKCU:\Software\Policies\Microsoft\Edge

# Chrome
HKCU:\Software\Google\Chrome\PreferenceMACs
HKCU:\Software\Policies\Google\Chrome

# Firefox
HKCU:\Software\Mozilla\Firefox
```

### Policy Restrictions
```powershell
HKCU:\Software\Microsoft\Windows\CurrentVersion\Policies
HKLM:\Software\Microsoft\Windows\CurrentVersion\Policies
HKCU:\Software\Policies
HKLM:\Software\Policies
```

### Shell Extensions and BHO
```powershell
HKLM:\Software\Microsoft\Windows\CurrentVersion\Explorer\Browser Helper Objects
HKLM:\Software\Microsoft\Windows\CurrentVersion\Shell Extensions\Approved
HKCU:\Software\Microsoft\Windows\CurrentVersion\Explorer\FileExts
```

### Services and Drivers
```powershell
HKLM:\System\CurrentControlSet\Services
```

## Analysis Tasks

1. **Read each registry location** using PowerShell
2. **Identify suspicious entries**:
   - Unknown or obfuscated program paths
   - References to temporary directories
   - Unsigned or unusual executables
   - Recently added entries (if timestamps available)
   - Suspicious URLs in browser settings

3. **Document findings** with:
   - Registry key path
   - Value name and data
   - Why it's suspicious
   - Severity level

4. **Check for common malware signatures**:
   - Cryptomining software
   - Adware/browser hijackers
   - Trojans and RATs
   - Ransomware persistence

## Output Format

```markdown
## Registry Scan Report

### Executive Summary
[Brief overview of findings]

### Critical Findings
1. **[Registry Key Path]**
   - Value: [name] = [data]
   - Issue: [description]
   - Severity: Critical/High/Medium/Low
   - Recommendation: [action]

### Suspicious Entries
[List all questionable registry modifications]

### Baseline Normal Entries
[Document legitimate entries for comparison]

### Remediation Steps
1. [Step-by-step instructions]
```

Begin the registry scan now.
