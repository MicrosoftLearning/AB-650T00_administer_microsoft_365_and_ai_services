---
title: Get started with the AB-650 labs
---

# Get started with the AB-650 labs

These seven labs give you hands-on practice administering Microsoft 365 and its AI services. Each lab follows a learning path in course AB-650 and builds on the work you did in the labs before it.

## Your lab environment

| Item | Detail |
| --- | --- |
| Tenant | One Microsoft 365 E7 tenant that you keep for all seven labs. It includes nine licensed sample people, seeded groups, mail, documents, and agents. |
| SEA-DEV1 | The administrator workstation. Do most of your configuration work here. |
| SEA-DEV2 | The test workstation. Sign in as sample people here, in Microsoft Edge InPrivate windows, to test what they can and can't do. |
| Lab files | The **AllFiles (F:)** drive on both workstations. |
| Azure | Not required. The labs don't create Azure subscriptions, billing accounts, or resources. |

> [!NOTE]
> Lab steps show your tenant as `<yourtenant>`, for example `PattiF@<yourtenant>.onmicrosoft.com`. Replace `<yourtenant>` with the tenant name provided in your lab environment.

## Lab files

The **AllFiles (F:)** drive holds the files you open or upload during the labs. The drive is read-only. When a lab asks you to edit a file, copy it to **Documents** first.

| Folder | Used in |
| --- | --- |
| **Lab03** | Lab 3: the new-hire file for bulk account creation |
| **Lab05** | Lab 5: sensitive information type sample files and the DLP worksheet |
| **Lab06** | Lab 6: a practice Cowork consumption case |
| **ClassActivities** | Practice files your instructor uses in class discussions |

If you aren't using the hosted lab environment, each lab step that uses a file also links to it so you can download it. All the files are in the [Allfiles](https://github.com/MicrosoftLearning/AB-650T00_administer_microsoft_365_and_ai_services/tree/main/Allfiles) folder of this repo.

## How the labs work

- **Complete the labs in order.** Later labs use groups, sites, policies, and agents that you create in earlier labs.
- **Test as the right person.** Testing a sample person's access from your administrator session can hide a permission boundary. Use an InPrivate window on **SEA-DEV2** for each sample person, and close it before you sign in as someone else.
- **Expect some delays.** Some changes take minutes or hours to appear, such as usage reports, label publishing, and search indexing. When a lab says a result might be pending, record **pending** with the date and time, and continue.
- **Practice cases are labeled.** Some tasks use sample evidence instead of a live event, because a new tenant has no alerts or usage history. These are marked as practice or sample cases. Don't search a portal for their names, IDs, or events.
- **Each exercise ends with a check.** A line that starts with **You have successfully** confirms what you completed.

## The labs

| Learning path | Labs |
| --- | --- |
| 1: Configure and manage Microsoft 365 tenants and workloads | Lab 1: Operate the Microsoft 365 tenant<br>Lab 2: Enable collaboration and govern its content |
| 2: Govern and secure Microsoft 365 tenants and workloads | Lab 3: Administer identities and delegated access<br>Lab 4: Control sign-in and protect communications<br>Lab 5: Protect and govern information |
| 3: Manage and secure Microsoft 365 AI services | Lab 6: Roll out and administer Copilot and Cowork<br>Lab 7: Govern agents and operate AI services |

When you're ready, start with [Lab 1: Operate the Microsoft 365 tenant](LP1/01-operate-microsoft-365-tenant.md).
