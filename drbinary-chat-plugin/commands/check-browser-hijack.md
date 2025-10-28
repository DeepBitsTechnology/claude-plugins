---
name: check-browser-hijack
description: Detect browser hijacking including homepage changes, search engine modifications, and malicious extensions
---

# Browser Hijacking Detection

Scan all installed browsers for signs of hijacking, unauthorized modifications, and malicious extensions.

## Browsers to Check
- Google Chrome
- Mozilla Firefox
- Microsoft Edge
- Internet Explorer
- Brave
- Opera

## Detection Areas

### 1. Browser Settings Files
Check for unauthorized modifications in:

#### Chrome
- `%LOCALAPPDATA%\Google\Chrome\User Data\Default\Preferences`
- `%LOCALAPPDATA%\Google\Chrome\User Data\Default\Secure Preferences`
- Look for: homepage_url, default_search_provider, homepage

#### Firefox
- `%APPDATA%\Mozilla\Firefox\Profiles\*.default*\prefs.js`
- Look for: browser.startup.homepage, browser.search.defaultenginename

#### Edge
- `%LOCALAPPDATA%\Microsoft\Edge\User Data\Default\Preferences`
- Similar structure to Chrome

### 2. Browser Extensions
Scan extension directories:

#### Chrome Extensions
- `%LOCALAPPDATA%\Google\Chrome\User Data\Default\Extensions\`
- Check for unknown or suspicious extension IDs
- Review extension manifest.json files for:
  - Excessive permissions
  - Unknown developers
  - Suspicious URLs

#### Firefox Add-ons
- `%APPDATA%\Mozilla\Firefox\Profiles\*.default*\extensions\`
- Review extensions.json

#### Edge Extensions
- `%LOCALAPPDATA%\Microsoft\Edge\User Data\Default\Extensions\`

### 3. Browser Shortcuts
Check desktop and taskbar shortcuts for hijacking:
```powershell
# Check Chrome shortcut target
Get-ItemProperty "C:\Users\*\Desktop\*.lnk" | Select-Object Target
```
Look for modified targets with:
- Suspicious command line arguments
- URLs appended to shortcuts
- Redirects to unwanted sites

### 4. Registry Browser Settings
```powershell
# IE/Edge HomePage
HKCU:\Software\Microsoft\Internet Explorer\Main -> "Start Page"

# Default search providers
HKCU:\Software\Microsoft\Internet Explorer\SearchScopes

# Browser policies
HKCU:\Software\Policies\Microsoft\Edge
HKCU:\Software\Policies\Google\Chrome
```

### 5. Proxy Settings
Check for unauthorized proxy configurations:
```powershell
HKCU:\Software\Microsoft\Windows\CurrentVersion\Internet Settings
```
Look for:
- ProxyEnable = 1 (when not expected)
- ProxyServer settings
- AutoConfigURL (PAC file hijacking)

### 6. Hosts File
Check for redirections:
- `C:\Windows\System32\drivers\etc\hosts`
- Look for suspicious entries redirecting popular sites

## Analysis Steps

1. **Inventory all installed browsers**
2. **Check each browser's configuration files**
3. **Scan extension directories**
4. **Verify shortcut targets**
5. **Review registry settings**
6. **Check proxy and DNS settings**
7. **Examine hosts file**

## Indicators of Hijacking

### Critical Signs
- Homepage changed to unknown search engine
- Default search provider modified
- Extensions installed without user knowledge
- Shortcuts modified with appended URLs
- Proxy settings enabled unexpectedly
- Hosts file redirections

### Common Hijacker Behaviors
- Redirect to ad-filled search pages
- Pop-up advertisements
- New tabs opening to specific sites
- Search queries redirected
- Browser toolbars added

## Output Format

```markdown
## Browser Hijacking Scan Report

### Executive Summary
[Status: Clean / Hijacked / Suspicious Activity Detected]

### Browsers Analyzed
- [x] Google Chrome - [Status]
- [x] Mozilla Firefox - [Status]
- [x] Microsoft Edge - [Status]
- [x] Internet Explorer - [Status]

### Findings

#### Critical Issues
1. **[Browser Name] - [Issue Type]**
   - Location: [file path or registry key]
   - Evidence: [specific finding]
   - Severity: Critical/High
   - Recommendation: [action]

#### Suspicious Extensions
1. **Extension Name** (Browser)
   - ID: [extension ID]
   - Location: [path]
   - Permissions: [list concerning permissions]
   - Recommendation: Remove/Investigate

#### Modified Settings
- Homepage: [current vs expected]
- Search Engine: [current vs expected]
- Proxy: [enabled/disabled - details]

### Clean Items
[List verified legitimate configurations]

### Remediation Steps
1. [Step-by-step removal instructions]
2. Reset browser settings to defaults
3. Remove malicious extensions
4. Clear browser cache and cookies
5. Verify shortcuts
6. Check for associated malware

### Prevention Recommendations
- Install reputable ad blocker
- Avoid clicking suspicious links
- Keep browser updated
- Review extension permissions regularly
```

Begin the browser hijacking scan now.
