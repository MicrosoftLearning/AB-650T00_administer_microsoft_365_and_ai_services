# Lab 3, Exercise 1 — expected results (practice case)

> **Practice case**: These object IDs, UPNs, and attributes are sample data for interpretation practice, not a live result from your tenant. Use this file only if you can't run the live Microsoft Graph PowerShell task, and note which path you used.

The verified training domain in this sample is shown as `<your-verified-domain>` — the same placeholder you set in `$tenantDomain`. In a real run, each UPN uses your own verified domain.

## Create result (`$results | Format-Table`)

| RequestedUpn | ReturnedId | Status | Error |
|---|---|---|---|
| priyas@<your-verified-domain> | 6f1c2a90-1111-4a2b-9c01-aaaa00000001 | Created | |
| jamiec@<your-verified-domain> | 6f1c2a90-2222-4a2b-9c01-aaaa00000002 | Created | |
| alexm@<your-verified-domain>  | 6f1c2a90-3333-4a2b-9c01-aaaa00000003 | Created | |
| danar@<your-verified-domain>  | 6f1c2a90-4444-4a2b-9c01-aaaa00000004 | Created | |
| samo@<your-verified-domain>   | 6f1c2a90-5555-4a2b-9c01-aaaa00000005 | Created | |

Every row reports `Created` with a returned object ID. A `Created` status means Graph accepted the create request for that row; it does not yet confirm you can read the account back.

## Verify result (`$verified | Format-Table`)

| RequestedUpn | IdMatches | UpnMatches |
|---|---|---|
| priyas@<your-verified-domain> | True | True |
| jamiec@<your-verified-domain> | True | True |
| alexm@<your-verified-domain>  | True | True |
| danar@<your-verified-domain>  | True | True |
| samo@<your-verified-domain>   | True | True |

Matching IDs and UPNs confirm Graph can read back the identities you created. They do not confirm every property you submitted.

## Group membership delta

- Members before add: `10`
- Accounts added: `5`
- Members after add: `15`
- Delta (after − before): `5` ✔ equals accounts added

The delta — not the absolute member count — is what proves the five new accounts joined the group. The group may already contain unrelated seeded members.

## Negative-test result (`$negativeResults | Format-Table`)

Run against a **brand-new** valid row plus a **conflicting** row (a UPN that already exists), tracked in an isolated variable so it never overwrites `$results`:

| RequestedUpn | ReturnedId | Status | Error |
|---|---|---|---|
| newhire-neg@<your-verified-domain> | 6f1c2a90-6666-4a2b-9c01-aaaa00000006 | Created | |
| priyas@<your-verified-domain> | | Failed | Another object with the same value for property userPrincipalName already exists. |

The brand-new row succeeds; the conflicting row fails on its own with a naming conflict. The batch continues past the failure — that is the point of a row-linked result.
