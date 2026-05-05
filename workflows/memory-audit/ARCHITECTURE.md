---
name: memory-audit
description: "Three-tier audit system (daily/weekly/monthly) with feedback loop."
version: 1.1.0
updated: 2026-05-05
---

<p align="right">
<a href="ARCHITECTURE.md">English</a> |
<a href="ARCHITECTURE.zh-CN.md">简体中文</a> |
<a href="ARCHITECTURE.zh-TW.md">繁體中文</a> |
<a href="ARCHITECTURE.ja.md">日本語</a>
</p>

# Memory Audit Architecture

> An architecture design for persistent memory management in AI Agents.

---

## 1. Problem

AI Agent memory degrades over time.

Each session produces new information — user preferences, environment facts, operational experience. This information is written to persistent storage, retained across sessions. But there is no mechanism to clean it up.

The results are predictable:

- **Redundancy**: The same information exists in multiple layers in different forms
- **Staleness**: Old entries become inaccurate after environmental changes
- **Bloat**: Memory capacity is gradually exhausted, compressing space for new information
- **Confusion**: Entries are miscategorized, with preferences mixed into processes

This is not a storage space problem — it is an information quality problem. A memory system full of stale, redundant entries is more dangerous than no memory at all — the Agent will make decisions based on wrong premises.

Memory Audit is a proactive management mechanism: maintaining memory accuracy and usability through periodic review, tiered response, and feedback tracking.

---

## 2. Core Principles

### 2.1 Proactive Over Reactive

> Don't wait until memory overflows to clean up.

Periodic audits prevent problems from accumulating, rather than responding to crises. It's like regular health checkups being more effective than emergency room visits — problems found early are easier to fix and less costly.

**Practice**: Three-tier audit cycle (daily/weekly/monthly), ranging from lightweight scans to deep reviews.

### 2.2 Report as History

> Audit reports themselves are decision records.

No separate decision storage is needed. Each report contains the review results for that period, discovered issues, actions taken, and user decisions. History is written right in the reports.

**Practice**: Feedback status files only track active items (pending/decided/deferred); completed decisions are archived in reports and then deleted.

### 2.3 Graduated Response

> Not every issue requires human intervention.

Severity determines the response path. Format issues are auto-fixed; categorization errors require human judgment. This way users only focus on truly important decisions, undisturbed by trivial matters.

**Practice**: Five severity levels (trivial → critical), from auto-fix to immediate alert.

### 2.4 Minimize User Effort

> Handle all pending items in a single reply.

Users should not have to approve items one by one. Batch operations are provided: "Approve All", "Ignore All", "Defer", or a single custom instruction to handle multiple entries.

**Practice**: The interaction protocol supports batch operations; no reply means items stay in pending status, with no push notifications.

---

## 3. Conceptual Model

### 3.1 Memory Model

The audit system needs to understand what is being audited. The persistent memory of an AI Agent is typically layered by stability and purpose:

```
┌─────────────────────────────────────────────────────┐
│  Layer 1: Config                                     │
│  Platform credentials, service configuration, feature flags │
│  Change frequency: Rare                              │
├─────────────────────────────────────────────────────┤
│  Layer 2: Profile                                    │
│  Interaction preferences, communication style, user habits │
│  Change frequency: Occasional                        │
├─────────────────────────────────────────────────────┤
│  Layer 3: Memory                                     │
│  One-line index pointing to detailed content in Skills │
│  Change frequency: Moderate                          │
├─────────────────────────────────────────────────────┤
│  Layer 4: Skills                                     │
│  Complete workflows, detailed manuals, deep knowledge │
│  Change frequency: Moderate                          │
├─────────────────────────────────────────────────────┤
│  Layer 5: Session                                    │
│  Current conversation context, temporary state       │
│  Change frequency: Very high (session-level)         │
└─────────────────────────────────────────────────────┘
```

**Design Intent**:

- **Progressive Disclosure**: Memory only stores a one-line index, Skills store the full content. This keeps the Agent's active context lean, loading details only when needed.
- **Separation of Concerns**: Configuration, preferences, knowledge, and context each have their own place, without cross-contamination.

**Storage Decision**:

```
Information X needs to be persisted
    │
    ├─ Platform config/credentials?        → Config (Layer 1)
    ├─ Preferences affecting interaction?  → Profile (Layer 2)
    ├─ Short facts / behavioral constraints? → Memory (Layer 3)
    ├─ Reusable workflows / deep knowledge? → Skills (Layer 4)
    └─ Only relevant to current session?   → Session (Layer 5), not persisted
```

> The five layers are a reference model, not a hard requirement. A simple Agent may only need three layers (Config / Memory / Session). The audit system should adapt to your actual layer structure.

### 3.2 Audit Cycle

Three-tier audits cover different depths:

```
┌─────────────────────────────────────────────────────┐
│  Daily Audit                                         │
│  Lightweight scan: capacity check, duplicate detection, format issues │
│  Auto-handle trivial/minor, log moderate/major       │
├─────────────────────────────────────────────────────┤
│  Weekly Audit                                        │
│  Medium review: summarize 7 days of findings, independent deep checks │
│  Process feedback, execute cleanup, generate weekly report │
├─────────────────────────────────────────────────────┤
│  Monthly Audit                                       │
│  Deep review: cross-week trends, health scores, long-term analysis │
│  Aggregate monthly data, track legacy issues         │
└─────────────────────────────────────────────────────┘
```

**Why three tiers?**

- A single tier is either too frequent (deep audits daily are too heavy) or too sparse (lightweight audits monthly are too slow)
- Three tiers complement each other: daily catches obvious issues, weekly handles those requiring judgment, monthly looks at trends
- Each tier is independently optional — you can do only daily (lightweight) or only monthly (deep)

**Design Intent**:

- Daily audits are fast, automatic, with no user interaction
- Weekly audits process user feedback and serve as the primary interaction point
- Monthly audits provide a global perspective without executing cleanup

---

## 4. Key Mechanisms

### 4.1 Severity Classification

Issues are classified into five levels by impact:

| Level | Definition | Response |
|-------|-----------|----------|
| **trivial** | Readability only (formatting, punctuation) | Auto-fix, not logged |
| **minor** | Improvement that doesn't change meaning | Auto-fix, logged in report |
| **moderate** | Requires moving or rewriting an entry | Reported, awaiting user approval |
| **major** | Requires deletion or significant rewrite | Reported, awaiting user approval |
| **critical** | Involves security or data integrity | Immediate alert + auto-fix |

**Design Intent**:

- trivial/minor are the majority, auto-handling saves time
- moderate/major require judgment, delegated to the user
- critical is rare but urgent, handled immediately

### 4.2 Feedback Lifecycle

Issues discovered by audits enter the feedback tracking system and go through a complete lifecycle:

```
    ┌──────────┐
    │  Audit   │
    │ Discovery│
    └────┬─────┘
         │
         ▼
    ┌──────────┐
    │  pending │ ← New issue enters
    └────┬─────┘
         │
    ┌────┴────┐
    ▼         ▼
┌────────┐ ┌──────────┐
│decided │ │ deferred │
└────┬───┘ └────┬─────┘
     │          │
     ▼          ▼
┌──────────┐ ┌──────────┐
│ Archived │ │ Held until│
│ in next  │ │ decision/ │
│ report   │ │ timeout   │
└──────────┘ └──────────┘
```

**Status Description**:

- **pending**: Newly discovered issue, awaiting user action
- **decided**: User has made a decision (approved/ignored)
- **deferred**: User deferred, to be handled later

**Cleanup Rules**:

- decided items are deleted after being shown in the next report
- Any item is auto-deleted after 60 days (safety net mechanism)
- deferred items are held until user decision or 60 days

**Why files instead of a database?**

- Human-readable: open feedback.json directly to check status
- Version-controllable: git diff tracks changes
- No infrastructure dependency: no need to run a database service
- Simple and sufficient: feedback entries typically range from single digits to low double digits

**Design Intent**:

- Reports themselves are history, no extra storage needed
- 60-day timeout prevents zombie entries from accumulating
- Items stay pending when users don't reply, with no push notifications

### 4.3 Smart Cleanup

File cleanup depends on pending status:

```
No pending items and no moderate/major issues
    → Auto-delete expired files

Only trivial/minor issues
    → Auto-delete

Has moderate/major pending
    → Wait for user confirmation before deleting
```

**Design Intent**:

- When there are no pending items, cleanup is safe and runs automatically
- When there are pending items, files are preserved for user reference
- Avoids deleting evidence while the user is still deliberating

### 4.4 Configuration Structure

The audit system is controlled through a configuration file. A JSON format (`config.json`) is recommended, containing the following sections:

| Field | Description | Example |
|-------|------------|---------|
| `data_dir` | Data storage root directory | `$DATA_DIR/memory-audit` |
| `retention.daily_reports` | Days to keep daily reports | `14` |
| `retention.daily_backups` | Days to keep daily backups | `14` |
| `retention.weekly_reports` | Days to keep weekly reports (-1 = permanent) | `-1` |
| `retention.monthly_reports` | Days to keep monthly reports (-1 = permanent) | `-1` |
| `retention.feedback_timeout_days` | Feedback entry timeout in days | `60` |
| `audit_schedule.daily` | Daily audit cron expression | `0 9 * * *` |
| `audit_schedule.weekly` | Weekly audit cron expression | `0 10 * * 1` |
| `audit_schedule.monthly` | Monthly audit cron expression | `0 10 1 * *` |
| `severity.auto_fix` | Severity levels for auto-fix | `["trivial", "minor"]` |
| `severity.require_approval` | Severity levels requiring user approval | `["moderate", "major"]` |
| `severity.immediate_alert` | Severity levels for immediate alert | `["critical"]` |
| `notification.daily` | Daily audit notification target | `"<your-notification-target>"` |
| `notification.weekly` | Weekly audit notification target | `"<your-notification-target>"` |
| `notification.monthly` | Monthly audit notification target | `"<your-notification-target>"` |

`retention` corresponds to the cleanup strategy in 4.3, `severity` corresponds to the severity classification in 4.1, and `audit_schedule` corresponds to the audit cycle in 3.2. `notification` configures notification channels for each audit tier (email, IM, webhook, etc.); the format depends on your notification system.

---

## 5. Practical Guide

### 5.1 How to Apply

**Step 1: Define Your Memory Layers**

Five layers are not required. Key principles:

- Each layer has a clear responsibility boundary (which information belongs to which layer)
- Each layer has capacity limits (to prevent unlimited growth)
- There are clear migration rules between layers (when to promote from Memory to Skills)

**Step 2: Establish Audit Mechanisms**

Choose frequency based on your scheduling capabilities:

| Frequency | Suitable Scenarios |
|-----------|-------------------|
| Daily | Agents with frequently updated memory |
| Weekly | Moderate usage frequency |
| Monthly | Low frequency or supplementary audit |

**Step 3: Implement the Feedback Loop**

A feedback status file (JSON recommended) tracks active items. Reports carry history; the status file only stores the present.

Envelope structure:
- `version`: Schema version (integer, e.g. `1`)
- `updated`: Last modification time (ISO-8601)
- `items`: Array of feedback entries

Core fields per item:
- `id`: Unique identifier
- `issue`: Issue description
- `severity`: Severity level (trivial / minor / moderate / major / critical)
- `action`: Suggested action
- `status`: Current status (pending / decided / deferred)
- `created`: Discovery time (ISO-8601)
- `decision`: User decision text (filled when decided/deferred)
- `decided`: Decision time (filled when decided)

**Step 4: Configure Cleanup Strategy**

Set retention periods:
- Daily reports: 7-30 days
- Weekly/monthly reports: Permanent
- Feedback timeout: 30-90 days

### 5.2 Adaptation Suggestions

| Your Scenario | Suggestion |
|---------------|-----------|
| Single Agent, file storage | Adopt this architecture directly |
| Multiple Agents, shared memory | Add shared knowledge layer, merge views during audit |
| Database storage | Use database tables for feedback status, structured records for reports |
| No scheduled triggering | Trigger audits manually, or check at session start |
| No notification channel | Write audit results to logs, display during sessions |

### 5.3 Common Pitfalls

**Unclear Audit Judgment Criteria**

The most common mistake is placing entries in the wrong layer. Establish clear classification rules:

- Profile only contains interaction preferences (communication style, language, approval mode)
- Memory only contains short facts and behavioral constraints (one-line indexes)
- Skills contain complete workflows and deep knowledge

If information contains both preferences and processes, split it into two parts and store separately.

**Context Limitations of the Audit Executor**

If the audit is executed by the AI Agent itself (rather than an independent script), note a fundamental contradiction: the auditor cannot simultaneously have a complete view of the audited memory and independent judgment.

- The Agent's tools may not be able to read all content of its own memory
- The Agent's judgment may be influenced by biases in its own memory
- Solution: Use a pre-execution script to inject the current state into the audit context, or periodically cross-validate with an independent script

> Using Hermes Agent as an example: the cron audit task runs in `skip_memory` mode, unable to directly call memory tools. A pre-exec script serializes the current memory state and injects it into the context, bypassing this limitation.

**Feedback Timeout Set Too Short**

60 days is the recommended value. If your users are not frequently online, consider extending to 90 days or longer. Timeout deletion is a safety net mechanism and should not trigger frequently.

**Timing of Backup and Cleanup**

Always back up before cleanup. Recommended workflow:
1. Create a backup before the audit
2. Execute the audit, generate the report
3. Auto-handle trivial/minor
4. Report moderate/major to the user
5. Delete the backup only after user confirmation

### 5.4 Frequently Asked Questions

**Q: Will audits be slow?**

A: Daily audits only check obvious issues (capacity, duplicates), typically completing in seconds. Deep reviews are left to weekly/monthly.

**Q: What if the user doesn't reply?**

A: Pending items remain, with no push notifications. They are auto-cleaned after 60 days. Users can always proactively view pending items.

**Q: How to handle accidental deletions?**

A: Back up before each audit. Weekly/monthly reports are retained permanently and can be used to recover decision records.

**Q: Can I run partial audits?**

A: Yes. Three tiers are recommended; you can do only daily (lightweight) or only monthly (deep).

---

## Appendix: Design Decision Records

| Decision | Alternatives Considered | Reason for Choice |
|----------|------------------------|-------------------|
| Report as history | Separate decision database | Simpler, reports already contain full context |
| File system storage | Database/vector storage | Human-readable, version-controllable, no infrastructure dependency |
| Manual classification | Automatic embedding routing | Deterministic, explainable, no model dependency |
| Fixed retention period | Dynamic adjustment | Simple and predictable, no added complexity |
| 60-day timeout | No timeout | Prevents zombie entries, forces periodic decisions |
| Three-tier audit | Single or multi-tier | Three tiers complement each other, each independently optional |
| Feedback tracking on audit side | Feedback tracking on memory side | Audit system owns quality, memory system owns storage |

---

*Memory Audit Architecture 1.1.0*