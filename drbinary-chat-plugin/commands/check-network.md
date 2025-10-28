---
name: check-network
description: Monitor active network connections and detect suspicious network activity
---

# Network Security Check

Analyze active network connections, listening ports, and detect suspicious network activity that may indicate malware communication.

## Network Analysis Tasks

### 1. Active Network Connections
```powershell
# All active TCP connections with process info
netstat -ano | findstr ESTABLISHED

# Get detailed connection info with process names
Get-NetTCPConnection | Select-Object LocalAddress, LocalPort, RemoteAddress, RemotePort, State, OwningProcess
```

### 2. Listening Ports
```powershell
# All listening ports
netstat -ano | findstr LISTENING

# Services listening on all interfaces (0.0.0.0)
Get-NetTCPConnection -State Listen | Where-Object {$_.LocalAddress -eq '0.0.0.0'}
```

### 3. Process-to-Network Mapping
```powershell
# Map connections to process names
Get-NetTCPConnection | ForEach-Object {
    $process = Get-Process -Id $_.OwningProcess -ErrorAction SilentlyContinue
    [PSCustomObject]@{
        LocalAddress = $_.LocalAddress
        LocalPort = $_.LocalPort
        RemoteAddress = $_.RemoteAddress
        RemotePort = $_.RemotePort
        State = $_.State
        ProcessId = $_.OwningProcess
        ProcessName = $process.ProcessName
        ProcessPath = $process.Path
    }
}
```

### 4. DNS Cache
```powershell
# View DNS resolution cache
Get-DnsClientCache | Select-Object Entry, Data, TimeToLive
```

### 5. Firewall Rules
```powershell
# Check for suspicious firewall rules
Get-NetFirewallRule | Where-Object {$_.Enabled -eq $true -and $_.Direction -eq 'Inbound'}
```

## Suspicious Indicators

### High-Risk Connections

#### 1. Unusual Remote Ports
- Common malware ports: 1337, 31337, 4444, 5555, 6666, 6667
- IRC ports: 6660-6669
- RAT ports: 1080 (socks), 3389 (RDP), 5900 (VNC)
- Tor: 9001, 9030, 9050, 9051

#### 2. Suspicious Remote IPs
- Connections to unexpected countries
- Known malicious IP ranges
- Dynamic DNS domains
- Recently registered domains

#### 3. Unusual Process Behavior
- **svchost.exe** - Should not make external connections (usually)
- **rundll32.exe** - Suspicious if connecting to internet
- **regsvr32.exe** - Should not have network activity
- User processes from temp directories with network access
- Unsigned processes with active connections

#### 4. Suspicious Patterns
- Multiple connections to same IP
- Beaconing behavior (periodic connections)
- Large data transfers
- Connections during odd hours
- P2P activity (if unexpected)

### Port Analysis

#### Well-Known Ports to Monitor
- 80, 443 - HTTP/HTTPS (normal but check destination)
- 21 - FTP (data exfiltration)
- 22 - SSH (remote access)
- 23 - Telnet (insecure, rarely legitimate)
- 25 - SMTP (spam/botnet)
- 53 - DNS (DNS tunneling)
- 135-139, 445 - SMB (lateral movement)
- 3389 - RDP (unauthorized remote access)

## Analysis Workflow

1. **Baseline Identification**
   - Identify legitimate system processes (Windows Update, Microsoft services)
   - Known applications with network access (browsers, chat apps)

2. **Anomaly Detection**
   - Compare against baseline
   - Flag unexpected processes with network access
   - Check process digital signatures
   - Verify process paths (beware of impersonation)

3. **Threat Assessment**
   - Prioritize by risk level
   - Correlate with file system and registry scans
   - Check process parent-child relationships

4. **Evidence Collection**
   - Document suspicious connections
   - Capture connection timeline
   - Note associated processes and files

## Commands to Execute

```powershell
# Comprehensive network analysis
$connections = Get-NetTCPConnection | Where-Object {$_.State -eq 'Established'} | ForEach-Object {
    $process = Get-Process -Id $_.OwningProcess -ErrorAction SilentlyContinue
    $signature = Get-AuthenticodeSignature -FilePath $process.Path -ErrorAction SilentlyContinue

    [PSCustomObject]@{
        RemoteIP = $_.RemoteAddress
        RemotePort = $_.RemotePort
        LocalPort = $_.LocalPort
        ProcessName = $process.ProcessName
        ProcessPath = $process.Path
        PID = $_.OwningProcess
        Signed = ($signature.Status -eq 'Valid')
    }
}

# Listening services
$listening = Get-NetTCPConnection -State Listen | ForEach-Object {
    $process = Get-Process -Id $_.OwningProcess -ErrorAction SilentlyContinue
    [PSCustomObject]@{
        LocalPort = $_.LocalPort
        ProcessName = $process.ProcessName
        ProcessPath = $process.Path
        PID = $_.OwningProcess
    }
}

# Recent DNS queries
Get-DnsClientCache | Where-Object {$_.TimeToLive -gt 0}
```

## Output Format

```markdown
## Network Security Analysis Report

### Executive Summary
- Active Connections: [count]
- Listening Ports: [count]
- Suspicious Activity Detected: Yes/No
- Critical Threats: [count]

### Critical Findings

#### 1. Suspicious Connection
- **Process**: [name] (PID: [id])
- **Path**: [full path]
- **Signed**: Yes/No
- **Remote IP**: [IP address]
- **Remote Port**: [port]
- **Reason**: [why flagged]
- **Risk**: Critical/High/Medium/Low

### Active Connections

#### Legitimate Connections
| Process | Remote IP | Remote Port | Local Port | Purpose |
|---------|-----------|-------------|------------|---------|
| [name]  | [IP]      | [port]      | [port]     | [desc]  |

#### Suspicious Connections
| Process | Remote IP | Remote Port | Signed | Risk | Reason |
|---------|-----------|-------------|--------|------|--------|
| [name]  | [IP]      | [port]      | No     | High | [desc] |

### Listening Ports

#### System Services (Expected)
- Port [X]: [Process] - [Description]

#### Suspicious Listeners
- Port [X]: [Process] - **INVESTIGATE**: [Reason]

### Process Analysis
[Detailed breakdown of processes with network activity]

### DNS Activity
- Recent queries to suspicious domains
- Unusual DNS lookups

### Firewall Analysis
- Unauthorized inbound rules
- Suspicious exceptions

### Recommendations

#### Immediate Actions
1. Kill suspicious processes (provide command)
2. Block suspicious IPs in firewall
3. Disconnect from network if critical threat

#### Investigation
1. Upload suspicious binaries for analysis
2. Check associated files and registry keys
3. Review process creation timeline

#### Remediation
1. Remove malware and associated files
2. Reset network settings if hijacked
3. Update firewall rules
4. Clear DNS cache
```

Begin the network security check now.
