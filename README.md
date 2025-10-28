# Deepbits Cyber Assistant Plugin for Claude Code

A comprehensive Claude Code plugin for local system security analysis, malware detection, and binary analysis developed by Deepbits Technology Inc.

## Overview

This plugin provides Claude Code with specialized cyber security capabilities including:
- Local Windows system scanning for malware and security issues
- Browser hijacking detection
- Windows Registry analysis
- Suspicious file detection
- Network connection monitoring
- Remote binary file analysis via Ghidra tools

## Features

### 🛡️ Security Scanning
- Comprehensive system security assessments
- Browser hijacking detection across Chrome, Firefox, Edge, and IE
- Windows Registry malware persistence detection
- Suspicious file system scanning
- Active network connection monitoring

### 🔍 Binary Analysis
- Upload suspicious files to remote sandbox
- Ghidra-powered static analysis
- Malware classification and threat assessment
- Indicator of Compromise (IoC) extraction
- Detailed decompilation and code analysis

### 🤖 Specialized Agent
The **Cyber Security Analyst** agent provides expert-level security analysis with:
- Structured threat assessment workflow
- Evidence-based reporting
- Risk prioritization (Critical/High/Medium/Low)
- Actionable remediation steps

## Installation

1. Clone or download this plugin to your local machine
2. Run claude code
3. Add marketplace:
   ```bash
   /plugin marketplace add DeepBitsTechnology/claude-plugins
   ```
4. Install the plugin:
   ```bash
   /plugin install drbinary-chat-plugin@DeepBitsTechnology
   ```
5. Connect MCP server:
   ```bash
   /mcp
   ```

## Plugin Structure

```
drbinary-chat-plugin/
├── .claude-plugin/
│   └── plugin.json           # Plugin manifest
├── .mcp.json                 # MCP server configuration
├── agents/
│   └── cyber-security-analyst.md    # Specialized security analyst agent
├── skills/
│   └── binary-analysis/
│       └── SKILL.md          # Binary analysis skill (with YAML frontmatter)
├── commands/
│   ├── scan-system.md
│   ├── scan-registry.md
│   ├── analyze-binary.md
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
1. Obtains access token from MCP server
2. Uploads file to remote sandbox
3. Performs Ghidra static analysis
4. Generates comprehensive threat report

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
   - Get access token via `workspace_get_access_token` MCP tool
   - Upload file using curl to https://mcp.deepbits.com/workspace/upload
   - File is saved to `/sandbox/[filename]` on remote server
   - Perform Ghidra analysis (decompilation, string extraction, etc.)
   - Generate comprehensive threat report

3. **Analysis includes:**
   - File metadata and hashes
   - PE structure analysis
   - Imported/exported functions
   - String artifacts
   - Behavioral indicators
   - Malware classification
   - Remediation recommendations

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
- `workspace_get_access_token` - Obtain authorization token
- Ghidra analysis tools (via remote sandbox)
- Binary decompilation services
- Malware analysis capabilities

## Security Considerations

### Safe Analysis
- All binary analysis occurs in remote sandbox environment
- No suspicious files are executed locally
- Analysis uses static analysis techniques only
- Files are automatically isolated

### Privacy
- Local scans only access data on your system
- Binary uploads are securely transmitted via HTTPS
- Access tokens are temporary and session-based

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
- **License**: MIT

### Contributing
For issues, feature requests, or contributions, contact Deepbits Technology Inc.

## Support

For support and questions:
- Email: support@deepbits.com
- Website: https://deepbits.com

## License

MIT License - See LICENSE file for details

---

**Developed by Deepbits Technology Inc.**
*Empowering secure computing through AI-assisted cyber security analysis*
