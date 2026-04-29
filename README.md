# Battle Ready Armor — V21 SLIM

**AI-augmented security assessment, governed end-to-end.**
*Built for operators, pentesters, and security teams.*

`VER 21.0.0-slim` · `OS macOS arm64 · Linux x86_64` · `TIER BRA SLIM`

---

## What it does

**BRA is an AI-augmented security assessment framework — an extension to
the operator, not a replacement.** The operator drives the engagement and
owns the decisions. An LLM agent works alongside as force-multiplier —
running reconnaissance, capturing evidence, drafting findings, generating
the report — but every action it takes is gated, anonymized, and recorded
by a governance layer the operator controls.

Before any agent is launched, BRA runs you through a multi-step scope
intake wizard: privacy posture (what gets anonymized before reaching the
model), target name + version, in-scope IPs / CIDRs / URLs / file paths /
device addresses, out-of-scope exclusions, time window, written-
authorization attestation, active-testing permission, and a per-engagement
safety-approvals matrix. The framework then auto-detects identifiable
values in the scope (IPs, hostnames, credentials), shows them to you for
review, and anonymizes the approved set before anything reaches the LLM.

From there, the agent runs reconnaissance, captures evidence, and files
leads and findings. Every request, response, tool call, and command flows
through multiple layers that:

1. **Scope-checks the target** in the command. Subdomains and unrelated hosts
   trigger an in-app modal asking the operator to grant Full / Limited / Deny
   access before anything runs.
2. **Enforces approval tokens** for destructive operations, active testing,
   framework-level writes, and other governance-sensitive actions.
3. **Anonymizes IPs and hostnames** before they reach the LLM and rehydrates
   them for the operator on the way back. The anonymization map is operator-
   only — the agent and any subagents are blocked from reading it.

As the engagement runs, three living records are built up — **Intel**
(services, endpoints, infrastructure facts), **Leads** (investigation
threads worth pursuing), and **Findings** (confirmed vulnerabilities).

When the operator ends an engagement and selects which findings to include,
the governed schema is used to generate a full engagement report with
executive summary, per-finding detail, remediation roadmap, and vendor
disclosure timeline.

## Why · Control in Depth

> *Control is not a brake on capability; it is the condition that allows capability to persist.*

BRA separates AI capability from operational authority. Models reason and
propose; governance state, runtime controls, and operator decisions
determine what actually executes.

- *AI is advisory, not authoritative.*
- *Authority is explicit state, not prompt-only or SDK hook compliance.*
- *Control exists both inside and outside the agent's purview — and
  survives replacement of the model, tool, interface, or operator.*

## Quickstart

```bash
chmod +x bra-slim
./bra-slim --host 0.0.0.0 --port 7777
```

On first launch the binary creates a hidden state directory (`.bra-slim/`)
under the current working directory and prints an auto-generated session
token to the terminal:

```
  BRA Web GUI — Battle Ready Armor Dashboard
  Token: ABc123…
  BRA Directory: /home/you
```

Open `http://<host>:7777/?token=<token>` in a browser. First load asks for
your Anthropic API key in **Config → Agent Runtime**; paste it in and start
your first engagement from the **Operations** tab.

> Run without auth via `--no-auth`.

---

## Walkthrough

### Dashboard

A scoped, read-only summary of the active engagement: governance posture,
scope, lifecycle progress, statistics, and recent findings. Operator
network identifiers shown in the Scope panel are local to the running host
and are redacted in this screenshot.

![Dashboard](screenshots/02-dashboard.png)

### Start an engagement

The wizard walks you through:

- **Privacy posture** — what gets anonymized before reaching the model.
  Slim ships with `Dumb Scope` (regex-only, no model involvement) and
  `Map Scan` (replace known values from the engagement's anon map).
- **Scope intake** — target name, IPs/CIDRs/URLs, in-scope and out-of-scope
  items, time window, written authorization.
- **Safety approvals matrix** — a grid of governance tokens. Slim activates
  a small core set; premium-only tokens are visible but disabled with a
  clear "Premium License Required" callout pointing at the email contact.

After scope intake the framework anonymizes detected identifiable values,
launches the agent, and the chat begins.

### Live engagement, governed every step

The Operations tab is the live engagement workspace — the operator sees the
agent's reasoning, proposed commands, and decision options inline, with
three independent gates riding every action:

- **Tool approval** — first-time use of any tool (Bash / Write / Edit /
  etc.). Per-call options: `Allow Once`, or `Allow + Override` to record a
  rule that suppresses repeats of the same pattern.
- **Target scope** — every host / IP / URL in the command must match the
  approved scope. Subdomains and unrelated targets prompt a modal with
  `Full / Limited / Deny` before anything runs.
- **Governance token** — destructive ops, active testing, framework writes,
  etc. each require their own token to be active. The prompt offers `Once
  / Session / Engagement / Deny` scoping.

![Operations tab — chat with TOOL APPROVAL modal pinned](screenshots/16-operations-fullscreen.png)

*Operations tab in flight: agent's reasoning + proposed command above, the
TOOL APPROVAL card pinned at the bottom. The* `Allow + Override` *button
records an override rule so the operator isn't prompted for the same
pattern again.*

![Scope modal — out-of-scope subdomain](screenshots/09-scope-modal-subdomain.png)

![Token governance — DESTRUCTIVE-APPROVAL required](screenshots/07-token-governance-modal.png)

#### Overrides — operator-recorded approval rules

An override is an approval rule the operator records during an engagement
so the agent doesn't ask again for the same pattern. They are visible in
**Loadout → Overrides** and can be enabled / disabled / reviewed there. The
framework keeps a per-decision history so every Allow / Deny remains
auditable after the fact.

Override management itself is governed: writes require the
`OVERRIDE-APPROVAL` token. Without it, the panel is read-only and existing
overrides apply but new ones cannot be created.

![Loadout — Overrides accordion expanded](screenshots/19-loadout-overrides.png)

#### Mobile-friendly

The Web GUI is responsive — the same engagement runs from a phone or tablet
without a separate app. Useful when the operator is away from the desk and
the agent hits a tool-approval gate.

![Dashboard on mobile](screenshots/18-mobile-dashboard.png)

![Operations on mobile with tool approval](screenshots/17-mobile-operations.png)

Three living records are built up as the engagement runs:

- **Intel** — services, endpoints, technology facts the agent confirms (open
  ports, DNS / mail infrastructure, framework fingerprints).
- **Leads** — investigation threads worth pursuing (softfail SPF, a
  misconfigured cookie, a suspicious header) before they become findings.
- **Findings** — confirmed vulnerabilities. As the agent identifies them
  they are emitted to a queue for documentation by a dedicated subagent;
  the main thread keeps running so the operator can drive forward while
  the queue drains.

![Findings tab — pending queue mid-flight](screenshots/11-findings-mid-flight.png)

Once the queue drains, every finding has a complete schema record with
title, description, attack vector, impact, evidence, reproduction steps,
remediation, and a hash-linked artifact:

![Findings tab — all 5 documented](screenshots/12-findings-complete.png)

![Leads tab — investigation threads captured during recon](screenshots/08-leads-tab.png)

### Engagement report

End the engagement, fill in the disclosure intake form (researcher / vendor
/ CNA fields, report types: pentest / vendor / research), select which
findings make the report, and the framework generates a structured report.

![End engagement intake](screenshots/13-end-intake-form.png)

![Findings selection before report](screenshots/14-findings-selection.png)

The report includes an AI-enhanced executive summary, technical summary
covering methodology and intelligence highlights, per-finding detail with
CVSS, a phased remediation roadmap, and a disclosure timeline ready to send
to the vendor:

**Executive Summary**
![Report — Executive Summary](screenshots/15a-report-executive.png)

**Technical Summary**
![Report — Technical Summary](screenshots/15b-report-technical.png)

**Findings Summary + Remediation Roadmap**
![Report — Findings Summary](screenshots/15c-report-findings-summary.png)

**Distribution + Disclosure Timeline + Vendor Submission**
![Report — Distribution & Timeline](screenshots/15d-report-distribution.png)

---

## SLIM vs Premium — feature comparison

Slim is functional end-to-end on its own. Premium expands the agent backend
choices, unlocks the deep-knowledge surfaces (methodologies, workflows,
skill/tool libraries), and exposes the operator-control plane (debug, live
diagnostics, harnesses, framework-improvement loop).

### Interfaces

| | SLIM | Premium |
|---|:---:|:---:|
| Web | ✓ | ✓ |
| Mobile (browser) | ✓ | ✓ |
| iOS Native App | — | ✓ |
| CLI | — | ✓ |
| TUI | — | ✓ |
| 2x4 (AI Assistant Co-pilot) | — | ✓ |

### Agent backend

| | SLIM | Premium |
|---|:---:|:---:|
| Anthropic / Claude direct | ✓ | ✓ |
| Multi-provider routing (frontier hosted models) | — | ✓ |
| Local / on-host LLM | — | ✓ |
| Bring-your-own LLM gateway | — | ✓ |

### Privacy & anonymization

**During scope intake** (one-time, before agent starts):

| | SLIM | Premium |
|---|:---:|:---:|
| Dumb Scope — regex/dictionary anon | ✓ | ✓ |
| No-Scope mode | ✓ | ✓ |
| Frontier Scope (LLM standardization) | — | ✓ |
| Local Scope (on-host NER — SecBERT / CyNER / spaCy) | — | ✓ |

**Live during LLM chat** (every request/response):

| | SLIM | Premium |
|---|:---:|:---:|
| Map Scan — applies the scope-intake anon map to live chat | ✓ | ✓ |
| No-Scan mode | ✓ | ✓ |
| Anon Scan (regex+dictionary live chat) | — | ✓ |
| Local Scan (on-host NER live chat — SecBERT / CyNER / spaCy) | — | ✓ |

**Manual rules** (operator-added via Loadout → Anonymization):

| | SLIM | Premium |
|---|:---:|:---:|
| Add custom anon entry (literal or pattern → token) | ✓ | ✓ |
| Edit / remove entries from the engagement anon map | ✓ | ✓ |

### Engagement loop

| | SLIM | Premium |
|---|:---:|:---:|
| Scope intake wizard | ✓ | ✓ |
| Active engagement chat | ✓ | ✓ |
| Tool / target / token approval gates | ✓ | ✓ |
| Findings emission + subagent documentation | ✓ | ✓ |
| Findings selection before report | ✓ | ✓ |
| End-engagement intake (vendor/CNA/PenTest) | ✓ | ✓ |
| AI-enhanced report (exec summary, intelligence highlights, remediation) | ✓ | ✓ |
| Vendor disclosure timeline | ✓ | ✓ |
| Custom Report Format / Syntax | — | ✓ |
| Find-and-write fast path | — | ✓ |

### Knowledge surfaces

| | SLIM | Premium |
|---|:---:|:---:|
| Loadout — Skills library | — | ✓ |
| Loadout — Tools library | — | ✓ |
| Loadout — Methodologies | — | ✓ |
| Loadout — Workflows | — | ✓ |
| Battle Card view (operator profile) | ✓ | ✓ |
| Pitches — framework improvement proposals | — | ✓ |
| Lessons Learned — Kill Chain Progression | — | ✓ |
| Reference docs viewer | — | ✓ |

### Operator control plane

| | SLIM | Premium |
|---|:---:|:---:|
| Debug tab — live diagnostics | — | ✓ |
| Log viewer / WebSocket inspector | — | ✓ |
| Process explorer | — | ✓ |
| Environment / token-usage diagnostics | — | ✓ |
| Harness explorer (test harness UI) | — | ✓ |
| 2x4 / Voice Call / Takeover integration | — | ✓ |

### Approval tokens

Slim ships a core set of governance tokens. Premium unlocks the rest of the
catalog and adds advanced session/engagement scoping.

> *Incomplete list* — additional tokens are introduced and rotated as the
> framework evolves; the rows below are representative, not exhaustive.

| Token | SLIM | Premium |
|---|:---:|:---:|
| `ACTIVE-TESTING-APPROVAL` | ✓ | ✓ |
| `DESTRUCTIVE-APPROVAL` | ✓ | ✓ |
| `OFFENSIVE-APPROVAL` | ✓ | ✓ |
| `OVERRIDE-APPROVAL` (always on in slim) | ✓ | ✓ |
| `FINDINGS-SELECTION` (always on in slim) | ✓ | ✓ |
| `SLOWPATH-FINDINGS` (always on in slim) | ✓ | ✓ |
| `DISABLE-TOKEN-TRACKING` (always on in slim) | ✓ | ✓ |
| `SYSTEM-INSTALL-APPROVAL` | — | ✓ |
| `FRAMEWORK-DEV-APPROVAL` | — | ✓ |
| `PUSH-APPROVAL` | — | ✓ |
| `LAYER2-WRITE-APPROVAL` | — | ✓ |
| `USER-INSTALL-APPROVAL` | — | ✓ |
| `VIRTUAL-INSTALL-APPROVAL` | — | ✓ |
| `HEALTH-BYPASS` | — | ✓ |
| `WATCH` / `TRACK` / `BRA-DEBUG` | — | ✓ |
| `NEGATIVE-RESULTS` | — | ✓ |
| `FINDINGS-FASTPATH` | — | ✓ |
| `IGNORE-BACKUP` | — | ✓ |

Locked features are available with a [premium license](mailto:bra@gainsecmail.com?subject=BRA%20Premium%20License%20Inquiry).

![Growth tab in slim](screenshots/03-growth-premium-locks.png)
![Loadout tab in slim](screenshots/04-loadout-premium-locks.png)
![Debug tab in slim](screenshots/05-debug-premium.png)

## Configuration

Most operational config lives under **Config**:

- **Project Directory** — point Slim at any directory; the engagement state
  goes under `<dir>/.bra-slim/` (state, approvals, anonymization map, logs,
  artifacts, captures).
- **Agent Runtime** — Anthropic API key + optional model override
  (`claude-sonnet-4-6`, `claude-opus-4-6`, `claude-haiku-4-5`).
- **Anonymization** — review and edit the operator-only anon map.
- **Danger Zone** — toggle the `DESTRUCTIVE-APPROVAL` governance token.
  When activated, the Operations tab's Reset and other destructive paths can
  run; deactivated, they are blocked at the hook layer.

![Config — Danger Zone](screenshots/06-config-danger-zone.png)

## System requirements

| | |
|---|---|
| **macOS** | 14+ on Apple Silicon (Mach-O arm64) |
| **Linux** | glibc 2.31+ (Ubuntu 20.04+, Debian 11+, RHEL 8+) on x86_64 |
| **Anthropic API key** | required |
| **Outbound network** | api.anthropic.com (agent), plus whatever your scope authorizes |

The binary is self-extracting (Nuitka onefile + zstd). On first run it
extracts to a temp directory and re-execs from there. State and engagement
output live under `--bra-dir`, never the temp directory.

## Security model

- **You own the binary**: no calls home, no telemetry, no license server.
- **You own the API traffic**: requests go from your host directly to
  Anthropic.
- **Anonymization happens locally**: the map is generated on your disk and
  the agent never sees the real values until after rehydration.
- **Control in Depth**: engagements follow the Control in Depth principle.
  The agent is mechanically contained, ensuring scope and rules of
  engagement are law, not suggestion.
- **Replacement-resistant**: the control boundary survives replacement —
  model, tool, interface, and operator agnostic.

## Limitations

- One engagement at a time per `--bra-dir`.
- No watch mode / live audit log (premium).
- No auto-recovery from a hard kill mid-engagement; reset and start over.
- Findings subagent uses the same Anthropic key as the main agent, so token
  cost is on you.
- WebSocket connections require a non-IRC port (chromium blocks 6666–6669);
  pick anything else.

## Upgrade

Email [bra@gainsecmail.com](mailto:bra@gainsecmail.com?subject=BRA%20Premium%20License%20Inquiry) for the
full license.

## License

Use of the binaries and documentation in this repository is governed by
[`LICENSE.txt`](LICENSE.txt). Free for personal, non-commercial use only;
commercial use requires a premium license. No redistribution, no reverse
engineering, no warranty. The Software is a compiled distribution —
source code is not included and is not licensed.

---

© GainSec LLC 2026 — `Licensed To: BRA Slim · Tier: BRA Slim · License #: SLIM`
