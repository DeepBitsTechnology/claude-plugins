# Deepbits Cyber Assistant Plugin for Claude Code

The Plugin equips Claude Code with advanced binary analysis capabilities for tasks such as incident response, malware investigation, and vulnerability assessment. It connects to the remote Dr. Binary MCP server over HTTP — no local server to install — and combines that with local system tools. To analyze a local file, Claude calls `prepare_upload` to obtain a one-time `curl` command, runs it to stream the file into a remote workspace, and then analyzes it with `inspect_binary` (Rizin triage), `run_sandbox` (`rizin -qc` and a full reverse-engineering toolkit), and `dump_data` (Ghidra decompilation). The plugin also exposes an Android/AOSP kernel CVE database for vulnerability research. Together with local Windows system scanning, browser hijacking detection, and registry/network monitoring, it transforms Claude Code into a powerful AI-assisted workspace for comprehensive system and binary security analysis.

## Overview

The Claude Code Security Analysis Plugin extends Claude Code with advanced cybersecurity and binary-analysis capabilities, enabling developers and analysts to perform in-depth system investigations directly within their coding environment.

This plugin seamlessly integrates with both cloud-based analysis platforms and local security tools via the Model Context Protocol (MCP), creating a unified workspace for intelligent, AI-assisted security analysis.

Designed for incident response, malware forensics, and vulnerability research, the plugin empowers users to:

- 🧩 Investigate compromised systems to identify indicators of compromise (IoCs) and attack traces.

- 🦠 Analyze malware samples to uncover behaviors, persistence methods, and payloads.

- 🛡️ Perform vulnerability and exploit analysis, including Android/AOSP kernel CVE research and patch-status assessment.

- ⚙️ Combine cloud automation with local expertise, integrating Deepbits’ agentic binary-analysis capabilities into Claude Code.

Specialized Cybersecurity Capabilities

This plugin provides Claude Code with specialized cybersecurity features, including:

- 💻 Local Windows system scanning for malware, configuration weaknesses, and security issues.

- 🌐 Browser hijacking detection to identify malicious extensions or modified settings.

- 🧮 Windows Registry analysis to reveal persistence mechanisms or misconfigurations.

- 🧾 Suspicious file detection through behavioral and signature-based analysis.

- 🔗 Network connection monitoring for unusual or unauthorized communications.

- 🧠 Remote binary file analysis powered by Rizin/radare2, Ghidra, angr, qiling, and other advanced analysis frameworks.

Together, these capabilities transform Claude Code into a comprehensive cybersecurity co-pilot—bridging the gap between code intelligence, system defense, and binary analysis.

## Features

### 🛡️ Security Scanning
- Comprehensive system security assessments
- Browser hijacking detection across Chrome, Firefox, Edge, and IE
- Windows Registry malware persistence detection
- Suspicious file system scanning
- Active network connection monitoring

### 🔍 Binary Analysis
- Upload local files to the remote workspace via a one-time `curl` (bytes never pass through the model context)
- Compare binaries with DeepDiff through the remote Dr. Binary MCP server
- Lightweight Rizin (rz-bin) triage with `inspect_binary`
- Deep, sandboxed analysis with `run_sandbox` + `rizin -qc` and a full RE toolkit (radare2, binwalk, angr, qiling, qemu, apktool, jadx, …)
- Full Ghidra decompilation with `dump_data`
- Malware classification, threat assessment, and IoC extraction

### 🧬 Android Kernel CVE Research
- Look up individual CVEs and their affected version ranges, fixes, and bug-inducing commits
- Find CVEs by AOSP kernel version, build date, or branch
- Identify unpatched and exploitable vulnerabilities for a branch at a point in time

### 🤖 Specialized Agent
The **Cyber Security Analyst** agent provides expert-level security analysis with:
- Structured threat assessment workflow
- Evidence-based reporting
- Risk prioritization (Critical/High/Medium/Low)
- Actionable remediation steps

## Installation

The plugin connects directly to the remote Dr. Binary MCP server (`https://mcp.deepbits.com/mcp`) over HTTP — **there is no local MCP server to install or run.**

1. Run Claude Code:
   ```bash
   claude
   ```
2. Add the marketplace:
   ```
   /plugin marketplace add DeepBitsTechnology/claude-plugins
   ```
3. Install the plugin:
   ```
   /plugin install drbinary-chat-plugin@deepbits
   ```
4. Connect and authenticate:
   ```
   /mcp
   ```
   Connecting to the `drbinary` server opens a browser-based sign-in. Log in with **Google or GitHub SSO** — no manual account creation is required. Once authenticated, the binary-analysis and kernel-CVE tools are available.

### Important Configuration

#### MCP Timeout Setting

Some remote analysis steps — sandboxed Rizin runs, `dump_data` (Ghidra) decompilation of large or complex binaries — can take several minutes. If you hit MCP timeouts during deep analysis, increase Claude Code's `MCP_TOOL_TIMEOUT`:

```bash
export MCP_TOOL_TIMEOUT=600000
```

This sets the timeout to 600,000 milliseconds (10 minutes), giving long-running analysis enough time to complete. Note that the `run_sandbox` tool also has its own `timeout` parameter (default 120s, maximum 600s); both layers apply, so size each to your workload.

## Plugin Structure

```
drbinary-chat-plugin/
├── .claude-plugin/
│   └── plugin.json           # Plugin manifest
├── .mcp.json                 # MCP server configuration
├── agents/
│   └── cyber-security-analyst.md    # Specialized security analyst agent
├── skills/
│   ├── binary-analysis/
│   │   └── SKILL.md          # Binary analysis skill (with YAML frontmatter)
│   └── kernel-cve-analysis/
│       └── SKILL.md          # Android/AOSP kernel CVE skill
├── commands/
│   ├── scan-system.md
│   ├── scan-registry.md
│   ├── analyze-binary.md
│   ├── find-cves.md
│   ├── check-browser-hijack.md
│   ├── scan-suspicious-files.md
│   └── check-network.md
└── README.md
```

### Skills Format

The `binary-analysis` skill follows the proper Claude Code skill format with YAML frontmatter:

```yaml
---
name: binary-analysis
description: Analyze suspicious binary files using remote Ghidra tools...
---
```

This allows Claude to automatically recognize when to activate binary analysis capabilities.

## Available Commands

### `/scan-system`
Perform a comprehensive security scan of the local system including:
- Browser hijacking checks
- Registry analysis
- Process monitoring
- Startup program review
- File system scanning
- Network connection analysis

### `/scan-registry`
Deep scan of Windows Registry for:
- Malware persistence mechanisms
- Autostart locations
- Browser setting hijacks
- Policy restrictions
- Shell extensions

### `/analyze-binary <file-path>`
Upload and analyze a suspicious binary file:
1. Calls `prepare_upload` and runs the returned one-time `curl` to stream the file into the remote workspace
2. Triages the file with `inspect_binary` (Rizin)
3. Digs deeper with `run_sandbox` + `rizin -qc`, and `dump_data` for full decompilation when needed
4. Generates a comprehensive threat report

### `/find-cves <query>`
Query the Android/AOSP kernel CVE database. Routes your request to the right tool:
- Look up a specific CVE (`CVE-2024-12345`)
- Find CVEs by kernel version, build date, or branch
- Find unpatched or exploitable CVEs for a branch at a point in time

### `/check-browser-hijack`
Detect browser hijacking across all installed browsers:
- Homepage and search engine modifications
- Malicious extension detection
- Shortcut target verification
- Proxy setting analysis
- Hosts file checks

### `/scan-suspicious-files`
Scan file system for suspicious files in:
- Temp directories
- AppData folders
- Common malware locations
- Recently modified executables
- Unsigned binaries

### `/check-network`
Monitor network activity for suspicious behavior:
- Active TCP connections
- Listening ports
- Process-to-network mapping
- Suspicious remote connections
- Firewall rule analysis

## Using the Cyber Security Analyst Agent

The plugin includes a specialized agent for security analysis tasks:

```bash
# Launch the agent for comprehensive analysis
/agent cyber-security-analyst

# Or let Claude Code automatically invoke it for security tasks
"Analyze my system for malware"
```

The agent will:
1. Assess the security situation
2. Perform targeted scans
3. Collect and correlate evidence
4. Generate structured reports
5. Provide remediation guidance

## Binary Analysis Workflow

When you need to analyze a suspicious file:

1. **Use the command:**
   ```bash
   /analyze-binary C:\path\to\suspicious.exe
   ```

2. **The plugin will:**
   - Upload the file via `prepare_upload` + `curl` into the remote workspace
   - Triage with `inspect_binary`, then run targeted `rizin -qc` analysis in `run_sandbox` (and `dump_data` for full decompilation when needed)
   - Generate a comprehensive threat report

3. **Analysis includes:**
   - File metadata and hashes
   - PE structure analysis
   - Imported/exported functions
   - String artifacts
   - Behavioral indicators
   - Malware classification
   - Remediation recommendations

## DeepDiff Benchmark

The remote Dr. Binary MCP server provides DeepDiff for matching related functions across binaries. See [Benchmarking DeepDiff Against BinDiff on Stripped and Cross-Compiler Binaries](https://drbinary.ai/blog/DeepDiffVsBinDiffBenchmark) for a comparison of DeepDiff and Google BinDiff across 21 binary pairs.

## MCP Server Integration

The plugin connects to the Deepbits MCP server for remote analysis:

```json
{
  "mcpServers": {
    "drbinary": {
      "type": "http",
      "url": "https://mcp.deepbits.com/mcp"
    }
  }
}
```

### Available MCP Tools

**Binary analysis**
- `prepare_upload` — mint a one-time `curl` command to upload a local file into the workspace
- `inspect_binary` — lightweight rz-bin triage (entrypoints, sections, imports/exports, symbols, strings)
- `run_sandbox` — run `rizin -qc` and a full RE toolkit (radare2, binwalk, angr, qiling, qemu, apktool, jadx, …)
- `dump_data` — full Ghidra decompilation/disassembly dump
- `list_files` / `read_file` — browse and read workspace artifacts

**Android kernel CVE database**
- `get_cve_info`, `query_cves_by_version`, `query_cves_by_date`, `query_cves_by_branch`, `find_exploitable_cves`, `list_all_cves`

## Security Considerations

### Safe Analysis
- All binary analysis occurs in remote sandbox environment
- No suspicious files are executed locally
- Analysis uses static analysis techniques only
- Files are automatically isolated

### Privacy
- Local scans only access data on your system
- Binary uploads are securely transmitted via HTTPS, using a single-use upload link valid for 10 minutes
- File bytes are streamed directly to the server via `curl` and never pass through the model context
- Authentication uses Google/GitHub SSO via the remote MCP server

### Permissions Required
- **Local**: Read access to file system, registry (via PowerShell)
- **Remote**: Upload capability to MCP server for binary analysis

## Example Usage

### Quick System Check
```
User: "Check my system for malware"
Claude: [Runs comprehensive scan, checks registry, processes, network]
```

### Browser Hijack Investigation
```
User: "My Chrome homepage keeps changing"
Claude: [Runs browser hijack scan, identifies modifications, provides fix]
```

### Suspicious File Analysis
```
User: "I found a weird file called update.exe in my temp folder"
Claude: [Uploads to sandbox, runs Ghidra analysis, provides threat assessment]
```

### Registry Investigation
```
User: "Scan my registry for malware persistence"
Claude: [Deep registry scan, identifies suspicious entries, recommends removal]
```

## Reporting Format

All scans generate structured reports with:

1. **Executive Summary** - Brief overview of findings
2. **Detailed Findings** - Each issue with evidence and severity
3. **Risk Assessment** - Threat levels and confidence ratings
4. **Remediation Steps** - Clear, actionable instructions
5. **Prevention Recommendations** - Future security measures

## Development

### Plugin Metadata
- **Name**: deepbits-cyber-assistant
- **Version**: 1.0.0
- **Author**: Deepbits Technology Inc.
- **License**: Apache 2.0

### Contributing
For issues, feature requests, or contributions, contact Deepbits Technology Inc.

## Support

For support and questions:
- Email: support@deepbits.com
- Website: https://deepbits.com

## License

Apache License 2.0 - See LICENSE file for details

---

**Developed by Deepbits Technology Inc.**
*Empowering secure computing through AI-assisted cyber security analysis*
