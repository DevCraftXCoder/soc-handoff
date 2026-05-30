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

### SOC Handoff Template — Full Security Reports

**`soc-handoff-template-blank.html`** — the production SOC report template used by the automated pipeline and the `/soc-report` skill.

This template accepts a `project-data` JSON payload injected at build time. When opened in a browser it runs fully client-side — all state, sign-offs, notes, and evidence fields persist to localStorage, and the report can be handed off as a standalone HTML file.

**Features:**

| Feature | Detail |
|---------|--------|
| Score circle | Priority-weighted 0–100 live scoring ring — P0 failures penalise 4× a P3 failure |
| P0 / P1 / P2 / P3 counters | Live verified/total counts per tier, updated as controls are checked |
| Interactive sign-off | **✎ SIGN OFF** header button opens a modal — name, role, secondary engineer; auto-triggers at 100% score; **SIGN OFF & EXPORT** signs and prints in one click |
| Editable signoff cells | Analyst name + role are inline-editable directly in the signoff section |
| Sign-off prompt bar | Persistent clickable banner above the signoff block — shows completion status, flips to "✓ Signed Off" after signing |
| Fingerprint | Report ID field auto-populated with project name, date, score, and harness result summary |
| Growth delta | Controls added, attack coverage delta, open gaps, and active threat count populated from run data |
| Week-over-week navigation | ◄ PREV / NEXT ► buttons navigate a rolling 12-week snapshot history |
| Print tip | Export guidance banner appears on click — "Uncheck Headers and footers for a cleaner PDF" |
| Analyst notes + evidence | Per-control notes and evidence fields, persisted to localStorage |
| Dark-mode PDF | `print-color-adjust: exact` — dark theme renders correctly when exported to PDF |
| Zero dependencies | Single HTML file, no server, no build step, any modern browser |

---

## Automated Weekly Reports

The system supports fully automated weekly SOC report generation — no analyst input required at run time. A scheduled runner executes the project's safety harness, maps test results to controls, computes the weighted security score, generates a pre-signed HTML report, and delivers it to a Discord channel as a rich embed with the report file attached.

See the [soc-report](https://github.com/DevCraftXCoder/soc-report) repository for the full automated pipeline documentation.

---

### Posture thresholds

| Score | Posture | Verdict |
|-------|---------|---------|
| 95–100 | CURRENT BUILD HARDENED | GO |
| 85–94 | STRONG — MINOR GAPS | REVIEW |
| 70–84 | SOLID — TRACK THE GAPS | REVIEW |
| 50–69 | GAPS NEED WORK | ATTENTION |
| < 50 | CRITICAL | BLOCK |

---

## Frameworks Referenced

| Framework | Application in reports |
|-----------|----------------------|
| NIST SP 800-61 | Incident lifecycle (detection → containment → recovery → lessons) |
| MITRE ATT&CK | Attack technique mapping in `attackMapping[]` fields |
| OWASP Top 10 | Application security control categories |
| CIS Controls v8 | Maturity stage model and control prioritization |

---

*3SixtyCo. — building practical security infrastructure for operators who need it to work.*
