---
name: analyze-binary
description: Upload and analyze a suspicious binary file using the remote Dr. Binary MCP tools
---

# Binary File Analysis

Analyze a suspicious binary file using the remote Dr. Binary MCP server (Rizin triage, sandboxed deep analysis, and Ghidra decompilation).

## Usage

Provide the full path to the suspicious file you want to analyze.

## Analysis Process

### Step 1: Upload the file to the remote workspace

The remote server cannot read your local filesystem, so the file must be uploaded first:

1. Call `prepare_upload(file_name="<name to store as>")`. It returns a one-time `curl` command (valid 10 minutes) with an `<ABSOLUTE_LOCAL_PATH>` placeholder.
2. Run that `curl` yourself with the Bash tool, substituting `<ABSOLUTE_LOCAL_PATH>` with the absolute path to the local file. The bytes upload directly to the server and never pass through the model context.
3. After a successful upload, refer to the file by its stored filename (the sandbox CWD is the workspace root) in all subsequent tool calls.

If the link has expired or was already used, call `prepare_upload` again for a fresh one.

### Step 2: Triage with `inspect_binary`

Call `inspect_binary(filepath="<filename>")` for fast rz-bin triage:

1. **Basic File Information** — file size and type, PE/ELF/Mach-O header, entrypoints
2. **Imports / Exports** — API calls and exported functions
3. **Sections** — .text, .data, .rsrc, etc.
4. **Strings & Symbols** — readable strings and symbol-derived functions

(For hashes like MD5/SHA256, compute them in `run_sandbox`, e.g. `sha256sum <filename>`.)

### Step 3: Deep analysis with `run_sandbox` + Rizin

Use `run_sandbox(command="rizin -qc '<commands>' <filename>")` for focused analysis:

- `aaa; afl` — analyze and list functions
- `aaa; pdf @ <func>` — decompile/disassemble a specific function
- `izz` — strings across the whole file; `ii` — imports; `iE` — exports

The sandbox also provides radare2, binwalk, xxd, angr/pwntools/qiling (python3), qemu (`qemu-{arch} -strace ...`), apktool, jadx, and more. `run_sandbox` accepts an optional `timeout` (default 120s, max 600s).

For broad decompilation across all functions, use `dump_data(filepath="<filename>")`, then `list_files` / `read_file` to read the output.

### Step 4: Behavioral & malware indicators
- Identify suspicious API calls
- Check for network capabilities
- Look for file system / registry operations
- Detect process injection, anti-debugging, packing/encryption

### Step 5: Generate Report
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
- Type: [PE32/PE64/DLL/ELF/Mach-O/etc]

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

Begin the analysis with the provided file path: upload it via `prepare_upload` + `curl`, then triage and dig in.
