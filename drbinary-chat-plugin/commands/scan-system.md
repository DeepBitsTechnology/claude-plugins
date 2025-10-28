---
name: scan-system
description: Perform comprehensive system security scan for malware, hijacking, and suspicious activity
---

# System Security Scan

Perform a comprehensive security assessment of the local Windows system. Check for:

## 1. Browser Hijacking Detection
- Check browser homepage and search engine settings
- Scan for unauthorized browser extensions
- Review browser shortcut targets for hijacking

## 2. Registry Analysis
Use PowerShell to check these critical registry locations:
- `HKCU:\Software\Microsoft\Windows\CurrentVersion\Run`
- `HKLM:\Software\Microsoft\Windows\CurrentVersion\Run`
- `HKCU:\Software\Microsoft\Windows\CurrentVersion\RunOnce`
- `HKLM:\Software\Microsoft\Windows\CurrentVersion\RunOnce`
- `HKCU:\Software\Microsoft\Internet Explorer\Main`
- `HKCU:\Software\Microsoft\Edge\Main`
- Browser policy keys

## 3. Suspicious Processes
- List running processes with unusual characteristics
- Check for processes running from temp directories
- Identify unsigned or hidden processes

## 4. Startup Programs
- Enumerate all startup programs and services
- Check Task Scheduler for suspicious scheduled tasks
- Review startup folder contents

## 5. File System Scan
- Check common malware locations:
  - `%TEMP%`
  - `%APPDATA%`
  - `%LOCALAPPDATA%`
  - `C:\Users\Public`
- Look for recently modified suspicious files
- Scan for hidden or system files in user directories

## 6. Network Connections
- List active network connections
- Identify suspicious listening ports
- Check for unexpected remote connections

## Output Format
Provide a structured report with:
- **Summary**: Overall security status
- **Findings**: List each issue with severity rating
- **Evidence**: File paths, registry keys, process names
- **Recommendations**: Specific remediation steps

Begin the scan now.
