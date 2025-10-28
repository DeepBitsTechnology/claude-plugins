---
name: analyze-binary
description: Upload and analyze a suspicious binary file using remote Ghidra tools
---

# Binary File Analysis

Analyze a suspicious binary file using the remote MCP server's Ghidra analysis tools.

## Usage

Provide the full path to the suspicious file you want to analyze.

## Analysis Process

### Step 1: Get Access Token
Use the `workspace_get_access_token` MCP tool to obtain authorization credentials.

### Step 2: Upload Binary
Upload the file to the remote sandbox environment:
```bash
curl -X POST -F "file=@/path/to/suspicious/file.exe" \
  -H "Authorization: Bearer <ACCESS_TOKEN>" \
  https://mcp.deepbits.com/workspace/upload
```

### Step 3: Ghidra Analysis
Once uploaded to `/sandbox/[filename]`, perform:

1. **Basic File Information**
   - File size and type
   - PE header analysis
   - Compilation timestamp
   - Hash values (MD5, SHA256)

2. **Static Analysis**
   - Decompile key functions
   - Extract strings
   - Analyze imports (API calls)
   - Examine exports
   - Review sections (.text, .data, .rsrc)

3. **Behavioral Analysis**
   - Identify suspicious API calls
   - Check for network capabilities
   - Look for file system operations
   - Registry manipulation functions
   - Process injection techniques

4. **Malware Indicators**
   - Anti-debugging tricks
   - Code obfuscation
   - Packing/encryption
   - Suspicious behavior patterns

### Step 4: Generate Report
Provide comprehensive findings with:
- File metadata
- Capabilities identified
- Malware classification
- IoCs (Indicators of Compromise)
- Risk assessment
- Remediation recommendations

## What to Look For

### High-Risk API Calls
- `CreateRemoteThread` - Process injection
- `WriteProcessMemory` - Memory manipulation
- `VirtualAllocEx` - Memory allocation in other processes
- `SetWindowsHookEx` - Keylogging capability
- `URLDownloadToFile` - Download additional payloads
- Registry functions - Persistence mechanisms
- Network functions - C2 communication

### Suspicious Patterns
- Hardcoded IP addresses
- Base64 encoded strings
- XOR encryption loops
- Anti-VM checks
- Sandbox detection
- Privilege escalation attempts

## Output Format

```markdown
## Binary Analysis Report

### File Information
- Filename: [name]
- Path: [original path]
- Size: [bytes]
- MD5: [hash]
- SHA256: [hash]
- Type: [PE32/PE64/DLL/etc]

### Analysis Summary
[1-2 paragraph overview]

### Capabilities Detected
- [ ] Network Communication
- [ ] File System Access
- [ ] Registry Modification
- [ ] Process Manipulation
- [ ] Keylogging
- [ ] Screenshot Capture
- [ ] Persistence Mechanism

### Detailed Findings
1. **[Category]**
   - Evidence: [specific code/strings/API calls]
   - Significance: [explanation]
   - Risk: [High/Medium/Low]

### Threat Assessment
- Classification: [Trojan/Adware/RAT/Ransomware/etc]
- Severity: Critical/High/Medium/Low
- Confidence: High/Medium/Low

### Indicators of Compromise
- File hashes
- Mutex names
- Registry keys
- URLs/IPs
- File paths

### Recommendations
1. [Immediate actions]
2. [Remediation steps]
3. [Prevention measures]
```

Begin the analysis with the provided file path.
