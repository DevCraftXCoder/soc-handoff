# 3SIXTYCO. SOC Handoff Report System

**Built by DevxCoder — 3SixtyCo.**

A security operations handoff and audit reporting system built to the standards used by national-level security organizations. Designed for analysts, engineers, and teams who need structured, verifiable, and transferable security posture documentation.

---

Incident response without structured handoffs loses context. Audits without traceability lose accountability. This system solves both — producing reports that are complete at the time of generation, readable without tooling, and built on frameworks that define how serious organizations manage security events.

The reporting standard is grounded in **NIST SP 800-61** (incident handling), **MITRE ATT&CK** (adversary behavior mapping), **OWASP** (application security controls), and **CIS Controls v8**. These are the same frameworks used by federal agencies, financial institutions, and critical infrastructure operators.

Every report produced by this system covers:

- Threat categorization and severity classification
- Control-by-control verification status with analyst accountability
- Timeline reconstruction from detection through containment
- Attack technique mapping against the MITRE ATT&CK matrix
- Risk acceptance tracking with owner and deadline
- Maturity trajectory across a 5-stage security posture model
- Formal analyst sign-off with role attribution

Reports are designed to be handed between teams, reviewed by leadership, or presented to external auditors without modification. Nothing is estimated or approximated — a control is either verified or it is not.

---

Integrated with [SIC](https://github.com/DevCraftXCoder/SIC) — a security scanning framework with 150+ tools across reconnaissance, exploitation, and analysis. SIC scan output ingests directly into the report system: findings are automatically mapped to controls, severity is classified, and the report is generated without manual data entry. Human review closes the loop.

---

## Templates

### P0–P3 Handoff Template — Start Here

**[`3sixtyco-p0-p3-handoff-template.html`](./3sixtyco-p0-p3-handoff-template.html)**

The fastest way to use this system. Download the file, open it in any browser — no server, no setup, no dependencies.

A weekly checklist with 24 blank rows across four priority tiers. Fill in your own controls, check them off as they're resolved, and sign off when the report is complete. Everything saves automatically to localStorage.

**How to use:**
1. Download `3sixtyco-p0-p3-handoff-template.html`
2. Open in any browser
3. Click any item row to type in a control name, notes, and reference
4. Check items off as they're resolved — the score ring updates live
5. Click **✓ PROD QA** to run a production readiness check
6. Click **✎ SIGN OFF** to sign the report with your name and role
7. Hit **⬇ EXPORT** to print or save as PDF

**What's in it:**

| Feature | Detail |
|---------|--------|
| Scoring ring | Weighted 0–100 (P0×4, P1×3, P2×2, P3×1) with grade and posture text |
| 24 blank rows | 6 per tier — fill in any project's items inline |
| Priority filters | ALL / P0 / P1 / P2 / P3 + keyword search |
| Prod QA check | One-click modal — evaluates P0/P1 resolution, score threshold, untitled items |
| Analyst sign-off | Modal with name + role, auto-triggers at 100%, locks into the footer |
| localStorage | All edits, checkboxes, and sign-off persist across sessions |
| PDF export | Dark-mode accurate — use browser print → Save as PDF |
| Zero dependencies | Single HTML file, fully offline, any browser |

---

### SOC Handoff Templates — Full Security Reports

For structured incident response and security audit reporting mapped to NIST SP 800-61, MITRE ATT&CK, OWASP, and CIS Controls. See the full pipeline documentation in the [SIC integration repo](https://github.com/DevCraftXCoder/SIC).

---

## Automated Weekly Reports

The system supports fully automated weekly SOC report generation — no analyst input required at run time. A scheduled runner executes the project's safety harness, maps test results to controls, computes the weighted security score, generates a pre-signed HTML report, and delivers it to a Discord channel as a rich embed with the report file attached.

This pipeline is production-verified against the DropStream project (a Cloudflare Workers edge application). The same pattern applies to any project with a structured test suite and a defined control-to-test mapping.

### How it works

```
Scheduled job (Monday 09:00)
        |
        v
safety-harness.mjs  (project test suite — N tests, PASS/FAIL stdout)
        |
        v
harnessMap  (test name → control ID)
        |
        v
Weighted score  W = { P0: 4, P1: 3, P2: 2, P3: 1 }
score = round(earned / max * 100)
        |
        v
Verdict  ≥95 GO · 85–94 REVIEW · 70–84 REVIEW · 50–69 ATTENTION · <50 BLOCK
        |
        v
HTML report  (pre-signed, localStorage pre-seeded, opens ready to read)
        |
        v
Discord  (rich embed + HTML file attached, week-over-week delta)
```

The scoring formula and posture thresholds mirror the template's own `recalc()` and `sg()` functions exactly — the number in the Discord embed is the same number the analyst sees when they open the report.

### Harness map

The harness map is a static dictionary that links each test name (as printed to stdout by the safety harness) to the control ID it covers in the SOC template. Controls not covered by automated tests remain in their default state. Controls covered by multiple tests take the lowest result — one FAIL overrides any other PASS for the same control.

Example mapping:

```js
{
  'roomState requires version 1':                    'dp03',
  'roomState deduplicates tile UIDs':                'rt04',
  'unsafe URL schemes are dropped or normalized':    'sp01',
  'Incognito clears persistence and blocks recents': 'sp02',
  'app source has no prompt/confirm regressions':    'rt11',
  // ...
}
```

Unmatched tests (tests that pass but have no control mapping) are logged as informational — they do not affect the score and do not indicate a configuration problem. They represent deeper unit tests that go beyond the SOC control surface.

### Discord output

Each weekly run posts one message to the configured webhook:

- **Embed** — score, verdict, PASS/FAIL count, failed control names, week-over-week delta
- **Attachment** — the full signed HTML report for the current week

The embed verdict drives the message color: green for GO, amber for REVIEW, orange for ATTENTION, red for BLOCK. If the runner itself fails (harness crash, filesystem error, network error), an error embed is posted in place of the normal report so the failure is always visible in the channel.

### Posture thresholds

| Score | Posture | Verdict |
|-------|---------|---------|
| 95–100 | CURRENT BUILD HARDENED | GO |
| 85–94 | STRONG — MINOR GAPS | REVIEW |
| 70–84 | SOLID — TRACK THE GAPS | REVIEW |
| 50–69 | GAPS NEED WORK | ATTENTION |
| < 50 | CRITICAL | BLOCK |

### Setup

**Requirements:** Node.js 20+, Bun (for TypeScript harness execution), a Discord webhook URL.

**Environment variables** (`.env` or system environment):

```
DROPSTREAM_SOC_DISCORD_WEBHOOK=https://discord.com/api/webhooks/...
SOC_ANALYST_NAME=Your Name
SOC_ANALYST_ROLE=Your Role
```

**Schedule** (Windows Task Scheduler):

```powershell
# Run once to register — Monday 09:00 AM, runs as soon as possible if missed
powershell -ExecutionPolicy Bypass -File scripts/register-dropstream-soc-task.ps1
```

**Manual run:**

```bash
# Dry run — full pipeline except Discord POST (logs payload to console)
node scripts/dropstream-weekly-soc.mjs --dry-run

# Full run — harness + report + Discord post
node scripts/dropstream-weekly-soc.mjs

# One-time forced post (for QA or catch-up)
node scripts/dropstream-weekly-soc.mjs --post-once
```

Reports are written to `_runs/soc-report-dropstream-YYYY-MM-DD.html`. Each report embeds a `<!--soc-score:N-->` meta comment at the top so the following week's run can compute the delta without opening the file.

### Adapting to another project

1. Write a safety harness that prints `PASS <test name>` / `FAIL <test name>` lines to stdout
2. Create a `harnessMap` linking each relevant test name to its control ID
3. Update `CONTROLS` to match the target template's control list and priority tiers
4. Point `TEMPLATE` at the project's blank SOC handoff HTML
5. Set the webhook URL and schedule the runner

The runner imports no framework dependencies — it is a single ESM script that reads one file and posts one HTTP request.

---

*3SixtyCo. — building practical security infrastructure for operators who need it to work.*
