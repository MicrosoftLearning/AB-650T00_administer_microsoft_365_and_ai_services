# Lab 4, Exercise 4 — threat evidence pack (practice case)

> **Practice case**: All messages, verdicts, IDs, and metrics are sample data for interpretation practice, not a live result from your tenant. No live simulation is represented here, and any follow-up training campaign you design stays a **Draft**. Note which path you used.

## 1. Simulation training report — "Finance reporting practice"

| Target | Delivered | Clicked | Reported | Training assigned | Training completed |
|---|---|---|---|---|---|
| Megan Bowen | Yes | Yes | No | Yes | No |
| Dana Reyes | Yes | No | Yes | No | — |
| Alex Morgan | Yes | Yes | No | Yes | Yes |

**Key point:** this is benign simulation activity. It has **no Network Message ID, alert, or incident** of its own. A simulation click does not create a real detection, so it cannot be correlated to the real message below.

## 2. Real message — email entity timeline

| Field | Value |
|---|---|
| Recipient | Megan Bowen (meganb@<your-verified-domain>) |
| Subject | Updated wire instructions |
| Sender | accounts@woodgrovebank.com.<lookalike-suffix> |
| Threat Explorer verdict | Phish |
| Network Message ID | 5a2f9c1e-0000-4d3b-9f21-msg000000001 |

**Timeline sequence:** Delivered to Inbox → verdict changed to Phish → **ZAP moved message to Quarantine**. The ZAP remediation is already complete; it needs no decision from you.

## 3. Alert correlated to incident

| Field | Value |
|---|---|
| Alert title | Email messages containing phish URL removed after delivery |
| Alert Network Message ID | 5a2f9c1e-0000-4d3b-9f21-msg000000001 (matches message above) |
| Incident ID | INC-2026-0918 |
| Other correlated alerts | None yet (single-alert incident) |
| Recommended classification | True positive — Phishing |

An incident with a single alert simply means nothing else has been correlated yet.

## 4. Pending AIR recommendation

| Field | Value |
|---|---|
| Recommendation | Soft-delete two related messages (one action) |
| Message A | 5a2f9c1e-0000-4d3b-9f21-msg000000001 — Phish verdict confirmed (investigated) |
| Message B | 5a2f9c1e-0000-4d3b-9f21-msg000000002 — **no verdict or timeline of its own in these records** |

Approving soft-deletes **both** messages as one action, but only Message A has a supporting verdict. Gather the incident ID, alert ID, and per-message evidence before anyone approves. If Message B later shows a clean verdict, the two-message soft-delete should not be approved as a single action.

## 5. Draft follow-up training campaign (design target)

| Field | Value to record |
|---|---|
| Campaign name | Finance - phishing awareness follow-up |
| Audience | Approved test recipients only (include specific users/groups) |
| Training threshold | Default 90 days (inspect, do not change) |
| Modules | Phishing-awareness modules from the training catalog |
| Schedule | Proposed start + end date (completion deadline) |
| Final action | **Save and close** → status **Draft** (never Submit) |
| Success measure | Completion rate by the deadline |
