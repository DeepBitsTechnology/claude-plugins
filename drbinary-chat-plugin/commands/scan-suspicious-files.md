---
name: scan-suspicious-files
description: Scan file system for suspicious files in common malware locations
---

# Suspicious File System Scan

Scan the local file system for suspicious files, malware indicators, and unauthorized modifications.

## Target Directories

### High-Risk Locations
```
%TEMP%                                    - Temporary files directory
%TMP%                                     - Alternative temp directory
%APPDATA%                                 - Application data
%LOCALAPPDATA%                            - Local application data
%PROGRAMDATA%                             - Program data (shared)
%USERPROFILE%\Downloads                   - Downloads folder
%USERPROFILE%\Desktop                     - Desktop
C:\Users\Public                           - Public user directory
C:\Windows\Temp                           - System temp directory
C:\Windows\System32\Tasks                 - Scheduled tasks
```

### Additional Locations
```
%APPDATA%\Microsoft\Windows\Start Menu\Programs\Startup
%PROGRAMDATA%\Microsoft\Windows\Start Menu\Programs\Startup
C:\Windows\System32\drivers                - Kernel drivers
C:\Windows\SysWOW64                        - 32-bit on 64-bit system
```

## Scan Criteria

### Suspicious File Characteristics

#### 1. File Extensions
Look for executables and scripts:
- `.exe`, `.dll`, `.sys`, `.scr`
- `.bat`, `.cmd`, `.ps1`, `.vbs`, `.js`
- `.com`, `.pif`
- Double extensions (e.g., `document.pdf.exe`)

#### 2. Recently Modified Files
- Files created/modified in last 7 days
- Unexpected modification times (e.g., 3 AM)

#### 3. Hidden or System Attributes
```powershell
Get-ChildItem -Hidden -System -Recurse
```

#### 4. Suspicious Names
- Random character names (e.g., `asdfjkl.exe`)
- System name impersonation (e.g., `svch0st.exe`, `chr0me.exe`)
- Generic names (e.g., `update.exe`, `install.exe`, `setup.exe`)

#### 5. Unsigned Executables
```powershell
Get-AuthenticodeSignature *.exe | Where-Object {$_.Status -ne 'Valid'}
```

#### 6. Large Files in Temp
- Executables >10MB in temp directories
- Archives in unusual locations

## Analysis Process

### Step 1: Directory Enumeration
For each target directory:
1. List all files recursively
2. Filter by suspicious criteria
3. Check file metadata (size, dates, attributes)

### Step 2: File Inspection
For suspicious files:
1. Calculate hash (MD5, SHA256)
2. Check digital signature
3. Extract metadata (PE info, version info)
4. Review file strings
5. Check file reputation (VirusTotal hash lookup)

### Step 3: Behavioral Indicators
Look for:
- Multiple files from same source
- Files dropped in sequence
- Common naming patterns
- Associated registry entries
- Network indicators

### Step 4: Prioritization
Rank findings by:
- **Critical**: Active malware, known bad hashes
- **High**: Unsigned in system directories, recent suspicious activity
- **Medium**: Questionable files in user directories
- **Low**: Old files, potential false positives

## File Analysis Commands

```powershell
# Recent executables in temp
Get-ChildItem -Path $env:TEMP -Recurse -Include *.exe,*.dll -ErrorAction SilentlyContinue |
  Where-Object {$_.LastWriteTime -gt (Get-Date).AddDays(-7)}

# Hidden files
Get-ChildItem -Path $env:APPDATA -Recurse -Hidden -ErrorAction SilentlyContinue

# Unsigned executables
Get-ChildItem -Path $env:LOCALAPPDATA -Recurse -Include *.exe -ErrorAction SilentlyContinue |
  Get-AuthenticodeSignature | Where-Object {$_.Status -ne 'Valid'}

# Large files in temp
Get-ChildItem -Path $env:TEMP -Recurse -File -ErrorAction SilentlyContinue |
  Where-Object {$_.Length -gt 10MB}
```

## Output Format

```markdown
## Suspicious File Scan Report

### Scan Summary
- Directories Scanned: [count]
- Files Analyzed: [count]
- Suspicious Files Found: [count]
- Critical Threats: [count]

### Critical Findings

#### 1. [Filename]
- **Path**: [full path]
- **Size**: [bytes]
- **Created**: [timestamp]
- **Modified**: [timestamp]
- **MD5**: [hash]
- **SHA256**: [hash]
- **Signed**: Yes/No
- **Reason**: [why flagged]
- **Risk Level**: Critical/High/Medium/Low
- **Recommendation**: [action]

### Suspicious Files by Location

#### %TEMP%
- [List of files with basic info]

#### %APPDATA%
- [List of files with basic info]

#### [Other Locations]
- [List of files with basic info]

### File Clusters
[Group related files that appear to be from same source]

### Recommendations
1. **Immediate Actions**
   - Quarantine critical threats
   - Disconnect from network if active malware detected

2. **Analysis**
   - Upload suspicious binaries for deep analysis
   - Check process list for running threats

3. **Remediation**
   - Remove confirmed malware
   - Clear temp directories
   - Run full antivirus scan

4. **Prevention**
   - Enable Windows Defender real-time protection
   - Keep system updated
   - Avoid running unknown executables
```

Begin the suspicious file scan now.
