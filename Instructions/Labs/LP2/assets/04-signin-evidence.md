# Lab 4, Exercise 2 — sign-in and policy evidence (practice case)

> **Practice case**: All users, device IDs, and results are sample data for interpretation practice, not a live result from your tenant. Use this file only when live risk signals or a managed device aren't available, and note which path you used. Every policy below is **report-only**; nothing here represents enforcement.

## Report-only policies in scope

| Policy | Mode | Assignment | Target | Grant control |
|---|---|---|---|---|
| Finance - Require MFA and compliant device | Report-only | Finance Users (emergency-access excluded) | Office 365 SharePoint Online | MFA + compliant device |
| Finance - Remediate risky sign-ins | Report-only | Finance Users (emergency-access excluded) | All resources | MFA strength; sign-in freq every time |
| Finance - Remediate high-risk users | Report-only | Finance Users (emergency-access excluded) | All resources | Require risk remediation |

## Sign-in event A — compliant device, no risk

| Field | Value |
|---|---|
| User | Dana Reyes (danar@<your-verified-domain>) |
| Application | Office 365 SharePoint Online |
| Client app | Browser (modern) |
| Device: managed | Yes |
| Device: compliant | Yes |
| Sign-in risk | None |
| User risk | None |
| Finance - Require MFA and compliant device | **Report-only: Success** |
| Finance - Remediate risky sign-ins | **Report-only: Not applied** |
| Finance - Remediate high-risk users | **Report-only: Not applied** |
| Auth details | MFA satisfied by existing claim (no prompt) |

A no-prompt sign-in is not proof MFA was bypassed — the claim already satisfied it.

## Sign-in event B — compliant device, high sign-in risk

| Field | Value |
|---|---|
| User | Dana Reyes (danar@<your-verified-domain>) |
| Application | Office 365 SharePoint Online |
| Client app | Browser (modern) |
| Device: managed | Yes |
| Device: compliant | Yes |
| Sign-in risk | High |
| User risk | Low |
| Finance - Require MFA and compliant device | **Report-only: Success** |
| Finance - Remediate risky sign-ins | **Report-only: User action required** (MFA strength) |
| Finance - Remediate high-risk users | **Report-only: Not applied** |

Combined projected outcome: the device policy would grant, and the sign-in-risk policy would require MFA strength. The user needs an MFA-capable method registered for risk remediation. Smallest correction: confirm/register that method — not a new blocking policy.

## What If evaluation — legacy client input

| Input | Result |
|---|---|
| User = Dana Reyes; Cloud app = Office 365 SharePoint Online; Device platform = Windows; Client app = Mobile apps and desktop clients - Other clients | Finance - Require MFA and compliant device: **Policies that will not apply**, reason **Client app** |

A policy scoped to modern clients is not also a legacy-authentication block. What If estimates applicability under the inputs you supply; it does not execute authentication.

## Emergency-access safeguard

| Account | In policy scope? |
|---|---|
| break-glass-01@<your-verified-domain> | Excluded from all three policies |

The rollback for any future enforcement is to return the policy to report-only (or its prior state); emergency-access accounts stay excluded so an enforced block never removes the recovery route.
