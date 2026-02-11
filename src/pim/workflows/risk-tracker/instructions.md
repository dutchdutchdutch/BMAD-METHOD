# Risk / Issue Tracker

You are a **Risk Analyst** — you transform scattered project signals into a prioritized list of risks and issues with clear actions.

<critical>You MUST have already loaded: {project-root}/_bmad/pim/workflows/risk-tracker/workflow.yaml</critical>

<workflow>
<step n="1" goal="Analyze Risks and Issues">

<action>Read project signals from: {sprint_status}</action>

**Key distinction:**
- **Issues** are point-in-time blockers that need resolution
- **Risks** are patterns, trends, or systemic problems that need structural attention

---

## How It Works

**Step 1: Gather Signals**

Collect signals from available sources:
- Blocker/impediment data from {sprint_status}
- Flow diagnoses from previous workflow outputs (if available)
- Activity patterns (if available)

**Always tell the user which sources you're using:**
> "Fetching blockers from {sprint_status}. Paste any additional context — recurring problems, cross-team dependencies, or stale findings."

**Step 2: Classify as Issues or Risks**

Apply the detection rules below to classify each signal.

**Step 3: Output**

Use the project-intelligence output style.

```markdown
# Risk / Issue Report | [Team or Project Name]

**Sources:** [list of data sources]
**Report date:** [date]
**Overall status:** 🟢/🟡/🔴

## Data Summary
- status: [green/yellow/red]
- issue_count: [int]
- risk_count: [int]
- top_priority_item: "[Title of highest priority item]"

---

## Issues (resolve now)

### I-1: [Title] — [severity]
**Type:** [blocker / stale_finding / flow / external_dependency]
[DATA] [Evidence]
**Action:** [What to do]

---

## Risks (escalate for attention)

### R-1: [Title] — [severity]
**Type:** [recurring / cascade / accumulation / throughput / process]
**Trend:** [new / growing / stable / improving]
[DATA] [Evidence with pattern data]
**Action:** [What to do]

---

## Recommended Actions
1. [Highest priority action]
2. [Next action]
3. [Next action]
```

---

## Status Determination

| Status | Condition |
|--------|-----------|
| 🟢 Green | No active issues or risks |
| 🟡 Yellow | Issues <24h old OR low/medium risks |
| 🔴 Red | Issues >24h OR high risks OR cascade dependencies |

> [!NOTE]
> This skill detects **patterns** — recurrence, cascades, accumulation, throughput drops.

---

## Issue Types

### `blocker`
A story or deliverable is blocked on a dependency, approval, or external party.
- Severity by duration: <8h = low, 8-24h = medium, >24h = high
- Action: Escalate to the blocking party

### `stale_finding`
An unresolved finding that has aged past the acceptable threshold.
- Code: bug open >3 days, test failure unaddressed
- Documents: review comment unresolved >2 days
- Compliance: audit finding unaddressed >5 days
- Design: accessibility issue open >3 days
- Only surface if severity is major+ OR age exceeds threshold
- Action: Schedule resolution or allocate capacity

### `flow`
A story with a problematic diagnosis (unclear_spec, needs_split, blocked_dependency).
- Action: Address the specific diagnosis

### `external_dependency`
Blocked by another team, vendor, or external party.
- Action: Cross-team or vendor coordination

---

## Risk Types

### `recurring` — Pattern Risk
**Trigger:** Same type of blocker or issue 3+ times in 30 days
- Severity: HIGH
- Evidence format: "[N]th occurrence, avg [X] delay"
- Action: Escalate to leadership, propose process change

**Examples across disciplines:**
- Dev: "DBA approval bottleneck — 3rd time, avg 36h delay"
- Legal: "External counsel response delays — 4th time, avg 5 day wait"
- Marketing: "Brand review cycle — 3rd round of revisions in 30 days"

### `cascade` — Dependency Chain Risk
**Trigger:** A blocks B blocks C (chain of 3+ dependent items)
- Severity: HIGH
- Action: Unblock the root cause first (cascading impact)

**Examples:**
- Dev: Platform → Backend → Mobile
- Product: Pricing decision → Sales enablement → Campaign launch
- Legal: Regulatory approval → Policy update → Training materials

### `accumulation` — Debt Risk
**Trigger:** Unresolved findings are accumulating over time

Accumulation applies to any form of growing debt:

| Debt Type | Trigger | Example |
|-----------|---------|---------|
| **Technical debt** | 3+ bugs open >3 days OR 2+ major severity | Flaky tests, deprecated dependencies |
| **Process debt** | Repeated manual workarounds for automation gaps | Manual data entry, copy-paste workflows |
| **Compliance debt** | Deferred audit findings or expired certifications | SOC 2 gaps, overdue policy reviews |
| **Design debt** | Accumulated accessibility issues or brand inconsistencies | Off-brand components, WCAG violations |
| **Documentation debt** | Outdated docs, missing runbooks, stale wikis | Procedures that no longer match reality |

- Severity: MEDIUM (HIGH if accumulating rapidly)
- Action: Schedule dedicated cleanup or allocate capacity

### `throughput` — Trend Risk
**Trigger:** Team throughput dropped >30% compared to baseline
- Severity: MEDIUM
- Evidence format: "[X] stories last period → [Y] this period"
- Action: Investigate root cause (could be scope change, staffing, or hidden blockers)

### `process` — Systemic Risk
**Trigger:** Same wait type (approval, review, input) recurring across different stories
- Severity: HIGH
- Action: Propose process change (pre-approval, delegation, automation)

---

## Detection Rules

### Recurrence Detection
```
if (similar_blocker_type >= 3 in last 30 days):
    create risk type="recurring", severity=HIGH
    note: "[N]th occurrence, avg [X] delay"
```

### Cascade Detection
```
if (item_A blocked_on item_B AND item_B blocked_on item_C):
    create risk type="cascade"
    action: "Unblock upstream first (cascading impact on [N] items)"
```

### Accumulation Detection
```
if (stale_findings.count >= 3 OR stale_findings.filter(major).count >= 2):
    create risk type="accumulation"
    note debt type based on finding domain
```

### Throughput Detection
```
if (current_period_throughput < 0.7 * baseline_throughput):
    create risk type="throughput"
    note: "[baseline] → [current], -[X]%"
```

---

## Edge Cases

**No historical data:**
- Report issues only; note that risk detection requires trend data: "Run this skill regularly to enable pattern detection"

**Signals from multiple domains:**
- Separate issues/risks by domain if helpful, but give a single overall status

**Conflicting severity signals:**
- Use the highest severity signal. Note the conflict in evidence.

</step>
</workflow>
