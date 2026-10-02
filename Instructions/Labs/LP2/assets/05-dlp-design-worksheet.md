# Lab 5, Exercise 2 — cross-workload DLP design worksheet

Fill in each field for the stated requirement. This worksheet is the **required deliverable** for Lab 5, Exercise 2. Staging a live simulation policy is an optional extension and is **not** equivalent to a tested, enforced policy. Use a **sample** sensitive information type (SIT); never real sensitive data.

**Requirement:** Sensitive transaction references must not be shared externally from Exchange, SharePoint, OneDrive, or Teams; must not be copied to removable media on managed endpoints; and must not be processed in a Microsoft Copilot or Copilot Chat prompt. The finance team may still use the data internally.

## Policy split

| Policy | Locations | Why separate |
|---|---|---|
| Policy 1 — Workloads & endpoints | Exchange, SharePoint, OneDrive, Teams, Devices | ___ |
| Policy 2 — Copilot | Microsoft 365 Copilot and Microsoft Copilot Chat | Selecting the Copilot location disables all other locations, so it needs its own policy |

## Condition (shared)

- Content contains > Sensitive info types > `__________________` (your sample SIT)

## Actions

| Location | Action | Override allowed? |
|---|---|---|
| Exchange / SharePoint / OneDrive / Teams | Block sharing with people outside the organization | ___ |
| Devices (endpoints) | Block copy to removable media | ___ |
| Copilot | Restrict Copilot from processing content → Processing prompts → Block | n/a |

## Exceptions

- Internal recipients: ______________________________
- Approved break-glass exception: ______________________________

## User notifications

- Policy tip text: ______________________________
- Email notification recipients: ______________________________

## Rollout mode

- Start in: **Simulation mode** (records matches without blocking) → review → *(enforcement is out of scope for this lab)*

## Verification plan

| Activity | Expected result | Where you read the evidence |
|---|---|---|
| Permitted: internal share / internal prompt with the SIT | No restrictive action | Activity explorer shows no restrictive action |
| Restricted: external share + Copilot prompt containing the SIT | Match recorded (simulation) | DLP simulation overview / Activity explorer |

## Notes

- Blocking only web searches would still let Copilot use permitted internal sources; **Processing prompts → Block** stops processing the sensitive prompt altogether.
- The Copilot prompt-processing control is a preview capability, and DLP enforcement can take up to four hours to apply.
