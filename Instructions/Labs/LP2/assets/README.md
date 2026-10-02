# LP2 lab assets — practice case files

These files are the **practice case fixtures** referenced by the LP2 lab guides in this folder's parent directory (`../03`, `../04`, `../05`). The guides link to them with `./assets/...`.

## What these are

- **Every value is sample data** for interpretation practice — names, object IDs, UPNs, timestamps, verdicts, and assessment results. Each file keeps a short practice-case header.
- **Interpretation only.** A practice case is for when you can't run the live task; it isn't a live result from your tenant. Where a guide offers one, note which path you used (live or practice case).
- **Placeholders stay placeholders.** UPNs use `<your-verified-domain>` — substitute your assigned `*.onmicrosoft.com` domain when you interpret them. No secrets or live tenant data are present.

## Files and where they're used

| Asset | Lab · Exercise | Purpose |
| --- | --- | --- |
| `03-new-hires.csv` | Lab 3 · Ex 1 | Two-column (`DisplayName`, `MailNickname`) new-hire seed file for the Microsoft Graph PowerShell bulk-create task. No passwords, no domain. Reference copy; the Lab 3 guide creates the same file inline on SEA-DEV1. |
| `03-expected-results.md` | Lab 3 · Ex 1 | Create, verify, membership-delta, and negative-test output for the practice case. Not linked from the Lab 3 guide, which runs the task live. |
| `03-pim-evidence.md` | Lab 3 · Ex 2 | PIM eligibility, activation, approval, scheduled end time, and audit correlation for the practice case. Not linked from the Lab 3 guide, which runs PIM live. Its names don't match the guide. |
| `04-sspr-registration.csv` | Lab 4 · Ex 1 | Registration-report export with enabled-not-registered and enabled-registered-capable cases for the readiness diagnosis. |
| `04-signin-evidence.md` | Lab 4 · Ex 2 | Report-only sign-in results, a risky sign-in, a What If result, and the emergency-access safeguard. |
| `04-threat-evidence.md` | Lab 4 · Ex 4 | Simulation report, email entity timeline, alert-to-incident correlation, and a pending AIR recommendation. |
| `05-label-visibility.md` | Lab 5 · Ex 1 | Sensitive-information-type test results and label priority/visibility resolution across two policies. |
| `05-dlp-design-worksheet.md` | Lab 5 · Ex 2 | Fill-in worksheet for the cross-workload DLP rule logic and verification plan. |
| `05-dlp-dspm-evidence.md` | Lab 5 · Ex 2 | DLP alert inside a Defender XDR incident and a DSPM oversharing assessment case. |
