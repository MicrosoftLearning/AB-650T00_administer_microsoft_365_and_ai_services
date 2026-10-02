# Lab 5, Exercise 2 — DLP and DSPM evidence (practice case)

> **Practice case**: All alerts, users, sites, and assessment results are sample data for interpretation practice, not a live result from your tenant. Use this file only when no live alert or completed assessment is available, and note which path you used. No real personal or financial data is represented.

## 1. DLP alert inside a Defender XDR incident

| Field | Value |
|---|---|
| Incident ID | INC-2026-0921 |
| Detection source | Microsoft Data Loss Prevention |
| Matched policy | Finance - external sharing protection |
| Matched rule | Block external share of transaction references |
| Affected user | Megan Bowen (meganb@<your-verified-domain>) — example row, not a real alert |
| Affected content | FinanceForecast-Q3.xlsx (SharePoint) |
| Workload | SharePoint Online |
| Sensitive info type | Woodgrove transaction reference (sample SIT) |
| Severity | Medium |

**What the alert supports:** you can name the policy that fired, who triggered it, the content, the workload, the SIT, and the severity.

## 2. Proportionate remediation options (choose the smallest that addresses the finding)

| Option | Impact | Proportionate here? |
|---|---|---|
| Remove the external share | Affects only this share | Yes — addresses the finding directly |
| Apply a sensitivity label | Adds protection, low blast radius | Possibly, in addition |
| Delete the file | Affects all users of the file | No — over-reach for one external share |
| Disable the user | Affects all the user's access | No — not justified by this evidence |

Case-management updates (comment, classification, status **In progress** → **Resolved**) document the response; they don't remediate the content themselves. Use **Go hunt** if you need to know whether the same user touched other sensitive content.

**Evidence needed before a high-impact action:** proof that deleting content or disabling the user actually addresses the finding (for example, repeated exfiltration across many files), not a single external share.

## 3. DSPM — data risk assessment

| Field | Value |
|---|---|
| Assessment | Default Microsoft 365 (weekly, top 100 SharePoint sites by usage) |
| Status | Completed |
| Last run | 2026-09-20 |

## 4. Oversharing finding (clustered exposure evidence)

| Site | Intended audience | Actual access | Gap |
|---|---|---|---|
| Finance Transaction Archive | 12 finance analysts | Organization-wide sharing links to transaction documents | Far more access than the content warrants |
| Finance Team Announcements | All employees (by design) | All employees | None — access matches purpose |

**Owner:** Finance Transaction Archive site owner (confirmed outsiders don't need the documents).

**Smallest supported remediation:** tighten sharing on the Finance Transaction Archive (remove organization-wide links) and/or apply a sensitivity label to the content, then escalate to the site owner for confirmation. Leave "Finance Team Announcements" unchanged — its access matches its purpose.
