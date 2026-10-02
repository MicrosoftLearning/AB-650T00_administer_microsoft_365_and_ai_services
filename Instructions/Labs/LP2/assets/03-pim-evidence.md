# Lab 3, Exercise 2 — PIM activation and audit evidence (practice case)

> **Practice case**: All names, IDs, and timestamps are sample data for interpretation practice, not a live result from your tenant. Use this file only when PIM licensing or authorized approvers are unavailable, and note which path you used.

## Role settings in effect (Message Center Reader)

| Setting | Value |
|---|---|
| Assignment type for requester | Eligible (permanent eligible) |
| Activation maximum duration | 4 hours |
| Require MFA on activation | Yes |
| Require justification | Yes |
| Require approval | Yes |
| Named approvers | Allan Deyoung, Joni Sherman |

Role settings apply to **every** assignment for this role, not only the requester below.

## Activation request

| Field | Value |
|---|---|
| Requester | Dana Reyes (danar@<your-verified-domain>) |
| Role | Message Center Reader |
| Requested duration | 2 hours |
| Justification | "Reviewing service advisories for the finance workload." |
| Activation start (UTC) | 2026-09-24T21:05:00Z |
| Scheduled end (UTC) | 2026-09-24T23:05:00Z |
| MFA satisfied | Yes (recent strong auth) |
| Approver decision | Approved by Joni Sherman at 2026-09-24T21:06:12Z |

The **scheduled end time** is the key evidence: the activation is temporary and expires automatically at 23:05 UTC unless deactivated sooner.

## Audit events (Microsoft Entra roles → My audit)

| Event | roleAssignmentRequestId | CorrelationId |
|---|---|---|
| Add member to role (activation requested) | req-4f8a-0001 | corr-a1 |
| Approve activation | req-4f8a-0001 | corr-a2 |
| Add member to role completed (active) | req-4f8a-0001 | corr-a3 |
| Remove member from role (deactivation) | req-4f8a-0001 | corr-b1 |

Correlate the full cycle by **`roleAssignmentRequestId`** (`req-4f8a-0001`), even though the `CorrelationId` values differ across events. After deactivation the requester is still **eligible** but holds no active permissions.

## Scope evidence (administrative unit)

| Action | Target user | In `Relecloud Regional HR` AU? | Result |
|---|---|---|---|
| Password reset (allowed) | HR coordinator | Yes | Success |
| Password reset (denied) | Out-of-AU user | No | Access denied |

The out-of-AU user is still visible in directory search — an administrative unit scopes management permissions, not read visibility.
