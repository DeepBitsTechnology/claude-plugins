---
name: find-cves
description: Query the Android/AOSP kernel CVE database by CVE id, version, build date, or branch
---

# Find Android Kernel CVEs

Query the remote Dr. Binary CVE database for Android/AOSP kernel vulnerabilities. Interpret the user's request and route it to the right MCP tool.

## Usage

```
/find-cves <query>
```

The query is free-form. Examples:
- `/find-cves CVE-2024-12345` — look up a specific CVE
- `/find-cves CVEs in 5.10` — CVEs affecting kernel version 5.10
- `/find-cves unpatched in android13-5.15` — unpatched CVEs in a branch
- `/find-cves exploitable in android13-5.15 as of 2024-06-01` — exploitable CVEs for a branch at a date
- `/find-cves list all` — browse the database

## Routing

Choose the tool that matches the user's phrasing:

| User intent | Tool | Required args |
| --- | --- | --- |
| Names a specific CVE id | `get_cve_info` | `cve_id` |
| "in / affecting version `<v>`" | `query_cves_by_version` | `aosp_version` |
| "built on / as of `<date>`" (no branch) | `query_cves_by_date` | `commit_date` |
| "unpatched in `<branch>`" | `query_cves_by_branch` | `branch` |
| "exploitable in `<branch>` as of `<date>`" | `find_exploitable_cves` | `commit_date` **and** `branch` |
| "list / show all" | `list_all_cves` | — |

## Important

- `find_exploitable_cves` needs **both** a branch and a date. If the user asks for "exploitable" CVEs but supplies only one, **ask for the missing value** before calling the tool — do not guess.
- After listing results, offer to drill into any specific CVE with `get_cve_info`.

## Output

Summarize results as a concise table — CVE id, severity, subcomponent, and affected range / patch status — then a short narrative on the most significant findings. Call out any exploitable CVEs explicitly.
