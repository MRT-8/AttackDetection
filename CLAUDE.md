# CLAUDE.md

This file provides guidance to Claude Code (claude.ai/code) when working with code in this repository.

## What this repo is

This is the **PT Research Attack Detection** repository — a curated collection of Suricata IDS/IPS rules from Positive Technologies' Attack Detection Team. It contains ~103 `.rules` files covering CVE exploits, APT techniques, malware, and security tool detection signatures. It is a pure content repository with no build system, CI, or scripts.

The repo is consumed as an upstream source by the parent dataset project at `../../` (via `scripts/build_ptresearch.py`), which pairs rules with PCAPs to build verified `{PCAP, Rule}` samples.

## Repository structure

Each attack lives in its own directory named after the CVE or attack (e.g., `CVE-2022-23131/`, `Log4Shell/`, `DarkHVNC/`). A typical directory contains:

- **`<name>.rules`** — Suricata detection rules (the primary asset)
- **`pcap.zip`** (optional) — password-protected traffic capture demonstrating the attack

Special directories:
- **`tools/`** — rules detecting security tools (e.g., Burp Suite)
- **`policy/`** — policy-violation rules (e.g., cleartext LDAP credentials)
- **`Suricon2018/`** — large ruleset from a conference presentation; PCAPs hosted externally

Root-level files:
- **`pt.rules.tar.gz`** + `.md5` — consolidated rules tarball
- **`LICENSE`** — Positive Technologies custom license (non-commercial use only)

## Rule conventions

- **SID range**: 10000000–11999999 (all rules must stay in this range)
- Rules include `reference:cve,YYYY-NNNNN` metadata, `classtype` labels, and creation/update timestamps
- Some rules detect TLS-encrypted traffic and require `encryption-handling: full` in `suricata.yaml`

## Working with rules

Validate a rule against a PCAP manually:

```bash
suricata -r <pcap_file> -S <rules_file> -c /path/to/suricata.yaml -l /tmp/out -k none
```

When the parent dataset project ingests this repo, it runs `scripts/build_ptresearch.py` which expects this repo at `sources/ptresearch-AttackDetection/` relative to the dataset root.

## Adding a new detection

1. Create a directory named after the CVE or attack
2. Add a `.rules` file with Suricata rules using SIDs in the 10000000–11999999 range
3. Optionally add a `pcap.zip` (password-protected) with traffic demonstrating the attack
4. Follow existing naming conventions (lowercase with underscores for rule files, directory names match the CVE/attack identifier)
