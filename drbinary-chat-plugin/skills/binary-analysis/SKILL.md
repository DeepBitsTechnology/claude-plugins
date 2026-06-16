---
name: binary-analysis
description: Analyze binary files (exe, dll, sys, bin, ocx, scr, cpl, drv, elf, so, macho, apk) to assess if they are malicious, perform decompilation, extract strings/imports/exports, detect malware, and provide threat assessment. Use this skill when user asks to analyze, examine, check, or assess any binary file, asks if a file is malicious/suspicious/safe, or provides a file path to a binary. Trigger for phrases like "Is [file] malicious?", "Analyze [file]", "What does [binary] do?", or any request involving binary file analysis.
---

# Binary Analysis

This skill performs deep analysis of suspicious binaries using the remote Dr. Binary MCP server. The server runs analysis remotely and has no access to your local filesystem, so a local file must first be uploaded into the remote workspace before any analysis tool can read it.

The upload is done with a short-lived `curl` command that you (Claude) run from your own shell via the Bash tool — the file's bytes stream directly to the server and never pass through the model context.

## When to Use This Skill

Use this skill when you need to:
- Analyze suspicious executable files (.exe, .dll, .sys, ELF, Mach-O, APK)
- Decompile binaries to understand their behavior
- Extract strings, imports, and exports from files
- Identify malware capabilities and techniques
- Perform static analysis on unknown binaries
- Investigate potential trojans, ransomware, or other malware
- Generate threat assessment reports

## Workflow

### Step 1: Upload the local file

Call `prepare_upload` with the name to store the file under. It returns a ready-to-run `curl` command containing a single-use upload link (valid for 10 minutes), with an `<ABSOLUTE_LOCAL_PATH>` placeholder.

```
prepare_upload(file_name="suspicious.exe")
```

Run the returned command **yourself with the Bash tool**, substituting `<ABSOLUTE_LOCAL_PATH>` with the absolute path to the local file. Do not print the command for the user to run — execute it directly. For example:

```bash
curl -f -F 'file=@/Users/me/Downloads/suspicious.exe' 'https://chat.deepbits.com/api/workspace/upload?upload_token=...'
```

On success the file is stored in the remote workspace under the chosen filename. The sandbox/analysis CWD is the workspace root, so you can refer to the file by its bare filename (e.g. `suspicious.exe`) in subsequent tool calls.

If the upload fails (expired or already-used token), call `prepare_upload` again to mint a fresh link.

### Step 2: Triage with `inspect_binary`

Call `inspect_binary` with the stored filename for fast, lightweight triage (powered by rz-bin / Rizin). It returns bounded file info, entrypoints, sections, imports, exports, linked libraries, symbol-derived functions, and strings — without full decompilation.

```
inspect_binary(filepath="suspicious.exe")
```

Use this first to understand what the file is before deciding where to dig deeper.

### Step 3: Deep analysis with `run_sandbox` + Rizin

For focused, deeper analysis, use `run_sandbox` to execute Rizin commands against the workspace file:

```
run_sandbox(command="rizin -qc '<rizin commands>' suspicious.exe")
```

Useful `rizin -qc` command strings (separate multiple with `;`):
- `aaa; afl` — analyze all, then list functions
- `aaa; pdf @ main` — decompile/disassemble the `main` function
- `iz` — strings in data sections; `izz` — strings in the whole file
- `ii` — imports; `iE` — exports; `is` — symbols
- `ie` — entrypoints; `iS` — sections; `iI` — binary info

The sandbox image also includes radare2, binwalk, xxd, clang, python3 (with angr, pwntools, qiling), qemu (for `qemu-{arch} -strace ...`), apktool, jadx, and more — use `run_sandbox` for any of these when triage points to a specific need. `run_sandbox` takes an optional `timeout` (default 120s, max 600s).

### Step 4 (optional): Full decompilation with `dump_data`

For broad Ghidra-based decompilation across all functions, call `dump_data(filepath="suspicious.exe")`. It returns a folder path of dumped decompiled/disassembled output, which you can then explore with `list_files` and `read_file`. This is heavier than `inspect_binary` and targeted `rizin` runs — reach for it when you need wide decompilation coverage.

### Step 5: Read artifacts

Analysis tools write text artifacts (decompiled `.c` files, JSON reports) into the workspace. Use `list_files` to see them and `read_file` to read their contents.

### Step 6: Generate Report

Provide a comprehensive analysis including:
- File metadata (size, hash, compilation timestamp)
- Identified capabilities (network, file system, registry, process manipulation)
- Suspicious indicators
- Malware classification (if applicable)
- Recommended actions

## Available Tools (remote MCP)

- `prepare_upload(file_name, size?)` — mint a one-time `curl` upload command
- `inspect_binary(filepath)` — lightweight rz-bin triage
- `run_sandbox(command, timeout?)` — run shell/Rizin/analysis commands in the sandbox
- `dump_data(filepath)` — full Ghidra decompilation dump
- `list_files(folder?)` / `read_file(filepath)` — inspect workspace artifacts

## Analysis Techniques

### Static Analysis
- PE/ELF/Mach-O header examination
- Section analysis (.text, .data, .rdata, .rsrc)
- Import Address Table (IAT) review
- String artifact extraction
- Code signature verification

### Behavioral Indicators
Look for:
- Anti-debugging techniques
- Obfuscation/packing
- Suspicious API calls (CreateRemoteThread, WriteProcessMemory, etc.)
- Network communication patterns
- Persistence mechanisms
- Privilege escalation attempts

### Malware Classification
Common categories:
- Trojan/RAT (Remote Access Trojan)
- Ransomware
- Adware/PUP (Potentially Unwanted Program)
- Rootkit
- Worm
- Spyware
- Browser hijacker

## Safety Considerations

- **Never execute** the binary on the local system
- All analysis occurs in the remote sandbox environment
- Files are automatically isolated in a per-conversation workspace
- Use static analysis tools; if dynamic techniques are needed, run them inside `run_sandbox` only
- Document all findings for incident response

## Output Format

```markdown
## Binary Analysis Report

**File Information**
- Name: [filename]
- Size: [bytes]
- MD5: [hash]
- SHA256: [hash]

**Analysis Summary**
[Brief overview of findings]

**Detailed Findings**
1. [Finding category]
   - Evidence: [specific data]
   - Significance: [what it means]

**Threat Assessment**
- Severity: [Critical/High/Medium/Low]
- Classification: [malware type]
- Confidence: [High/Medium/Low]

**Recommendations**
1. [Action item]
2. [Action item]
```

## Example Usage

User: "I found a suspicious file called setup_installer.exe. Can you analyze it?"

Response:
1. `prepare_upload(file_name="setup_installer.exe")`, then run the returned `curl` (with the file's absolute path) via Bash
2. `inspect_binary(filepath="setup_installer.exe")` for triage
3. `run_sandbox(command="rizin -qc 'aaa; izz; ii; afl' setup_installer.exe")` and decompile suspicious functions as needed
4. Identify malicious behavior (if any)
5. Provide a detailed report with recommendations
