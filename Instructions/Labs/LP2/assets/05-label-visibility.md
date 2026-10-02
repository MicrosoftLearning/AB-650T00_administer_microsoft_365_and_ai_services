# Lab 5, Exercise 1 — label visibility and priority evidence (practice case)

> **Practice case**: All labels, policies, and menu states are sample data for interpretation practice, not a live result from your tenant. Use this file only when client propagation (24–48 hours with new groups) hasn't completed, and note which path you used. No real sensitive data is represented.

## Custom sensitive information type test results

Pattern: `\b([Ww][Gg]-[0-9]{8}-[A-Za-z])\b` at **Low confidence**.

| Test file | Sample strings | Result |
|---|---|---|
| Valid references | `WG-48213077-K`, `WG-12345678-A`, `wg-48213077-k` | 3 matches (incl. lowercase variant) |
| Near-misses | `WG-48213077`, `WG-4821307-K`, `WG-482130777-K`, `WG-48213077-K9` | 0 matches |

Low confidence is the configured level, not a failed test.

## Label policies

| Policy | Order | Audience | Mandatory labeling | Default label |
|---|---|---|---|---|
| Finance - Woodgrove transaction labeling | 2 (higher) | Finance group | On | Woodgrove Confidential - Transaction Records |
| Pilot - broad labeling | 0 (lower) | Broad group | Off | None |

## Effective visibility for a dual-group user (Word, Files scope)

| Observation | Result |
|---|---|
| Woodgrove label in Sensitivity menu | Yes |
| Pilot label in Sensitivity menu | Yes (both remain on the menu) |
| Default label applied | Woodgrove Confidential - Transaction Records |
| Footer present | "Confidential - Woodgrove transaction data" |
| Document can remain unlabeled | No (default label satisfies the requirement) |

**Priority resolution:** the higher-order (order 2) policy's mandatory-labeling and default settings win outright — settings from different policies do not merge — but both labels stay on the menu for anyone the pilot policy also reaches. Priority resolves by **order number**, not by which policy looks more restrictive.

## Access vs. visibility

| User | Label visible in own menu? | Can open the protected document? |
|---|---|---|
| Finance-group member | Yes | Yes |
| Out-of-group user (no decryption rights) | Possibly (via pilot policy) | **No** |

Encryption restricts access to the file itself. Seeing a label in your own menu is separate from having permission to open protected content.
