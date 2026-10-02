# LP3 lab practice datasets

These files are **original fictional practice data** for the AB-650 LP3 lab guides in this folder's parent (`06-roll-out-copilot-cowork.md` and `07-govern-agents-operate-ai.md`).

> **Practice records only — not tenant events.** Every case and figure below is a fictional practice case, not a tenant event. No file represents a real alert, audit event, billing record, consumption, credit, or charge. A familiar agent, user, or document name in a row does **not** make the fictional event a tenant observation. Never merge a practice figure with a live tenant read-back. Each row is marked in its `DataLabel` or `Provenance` column as **Practice data** (the consumption and cost files add **(not paid usage)**).

| File | Used by | Purpose |
| --- | --- | --- |
| `17-cowork-consumption-practice.csv` | **Lab 6, Exercise 3 (M02 revisit)** | Fictional Cowork Credits by group and user. `IntendedFunded` = intended to be in scope of a Cowork-selecting spending policy (Yes = Finance, No = Legal). One Legal user with consumption illustrates a possible historical policy overlap (default **All users** or auto-apply) to investigate — not a presumed violation. Practice consumption case only, not paid usage. |
| `21-agent-governance-case-practice.csv` | **ILT discussion for Lab 7, Exercise 4 (M16)** | Three fictional audit, DLP, and analyst-note rows for correlation. They are **not** in Microsoft Purview or Agent 365; live agent status is checked separately. Do not search Purview for these rows. |
| `22-copilot-usage-practice.csv` | **ILT discussion for Lab 7, Exercise 5 (M17)** | Fictional enabled vs active users and active-users rate by workload, with a report window, for adoption interpretation. |
| `23-cost-reconciliation-practice.csv` | **ILT discussion for Lab 7, Exercise 5 (M17)** | Fictional Microsoft 365 and Azure cost rows with one shared tagged charge appearing in both views (to practice booking it once). Reading these rows performs **no Azure operation**; the Azure column is practice reconciliation data only. |

The `DataLabel` and `Provenance` cells read **Practice data** — the consumption and cost files add **(not paid usage)**. If a real financial, usage, or security record becomes available and is verified, inspect it **separately** from these practice files. Record a missed hands-on practice only for a live task that was unavailable; working through a practice case does not erase a live configuration the learner completed.
