---
lab:
  title: 'Lab 1 - Operate the Microsoft 365 tenant'
  description: 'Set the organization baseline, assign and troubleshoot service entitlements with group-based licensing, and investigate tenant health, connectivity, and recovery readiness up to the Microsoft 365 Backup billing boundary.'
  duration: 40
  level: 300
  islab: true
  primarytopics:
    - Microsoft 365 admin center
    - Group-based licensing
    - Service health and connectivity
    - Microsoft 365 Backup
---

# Lab 1: Operate the Microsoft 365 tenant

As a Microsoft 365 administrator, your first responsibility is to keep the tenant configured, licensed, and healthy so that the people who depend on it can work. That means setting a correct organization baseline, making sure the right users hold the entitlements they need, and knowing how to triage a service report without guessing.

You work in a ready-made Microsoft 365 E7 tenant that's already set up for you. It includes nine E7-licensed people: **Megan Bowen**, **Nestor Wilke**, **Alex Wilber**, **Isaiah Langer**, **Lynne Robbins**, **Joni Sherman**, **Allan Deyoung**, **Diego Siciliani**, and **Patti Fernandez**. Microsoft 365 Copilot, Agent 365, and a 25,000-Copilot-Credit tenant quota are also provided. You can focus on operating the tenant rather than building it.

This lab contains three exercises:

- **Exercise 1: Establish the organization baseline**
- **Exercise 2: Assign and troubleshoot service entitlements**
- **Exercise 3: Investigate health, connectivity, and recovery readiness**

This lab takes approximately **40 minutes** to complete.

## Learning objectives

By the end of this lab, you'll be able to:

- Configure an organization profile, company theme, and a scoped privacy setting, and confirm each change by reading it back.
- Interpret a DNS record set to tell domain-verification records apart from service records and spot a broken SPF record.
- Create unlicensed users and assign Microsoft 365 E7 to them through group-based licensing.
- Show that nested-group members don't inherit a group license, correct the assignment, and verify the Copilot and Agent 365 service plans.
- Configure a Service Health email notification, compare network connectivity test results from two clients with service state, and identify where Microsoft 365 Backup setup stops at the Azure billing prerequisite.

> [!IMPORTANT]
> Some steps use a **practice case**: supplied training content you reason about rather than a live event in your tenant. The settings, licenses, and notifications you configure are real and live.

## Before you start

You complete this lab signed in as a tenant administrator. The following conditions are provided by the lab environment, so you don't set them up:

| Provided by the environment | Detail |
| --- | --- |
| Tenant | A ready-made Microsoft 365 **E7** tenant with an assigned `*.onmicrosoft.com` domain. |
| People | Nine ready-to-use sample people, already licensed for Microsoft 365 E7. Leave their existing licenses in place. You create two more, unlicensed users in Exercise 2. |
| AI services | Microsoft 365 Copilot and Microsoft Agent 365 enabled, with a 25,000-Copilot-Credit tenant quota. |
| Your access | An administrator account with the roles noted at the start of each exercise. |

You keep the work you create in this lab. Later exercises and later labs build on it, so there's no end-of-lab cleanup.

---

## Exercise 1: Establish the organization baseline

**Estimated time:** 10 minutes.

### Scenario

Before your organization rolls out Microsoft 365 more widely, you set the tenant baseline. You update the organization profile and sold-to address, apply the company theme, and confirm that usage reports conceal user names. You then review a DNS record set for a planned custom domain and find the record that would break outbound mail.

**Roles used:** **Global Administrator**.

### Task 1: Set the organization profile

1. In the **Microsoft 365 admin center** at `https://admin.cloud.microsoft`, select **Show all** > **Settings** > **Org settings**.

1. Select the **Organization profile** tab, then select **Organization information**.

1. In the **Organization information** pane, set **Technical contact** to your administrator account (for example, `admin@<yourtenant>.onmicrosoft.com`), then select **Save**.

1. In the same pane, select **Edit address**. The **Billing accounts** page opens.

1. Select **Contoso**. Under **Sold to address**, select **Edit**.

1. In the **Edit sold-to address** pane, enter the following:
   - **First name:** `Allan`
   - **Last name:** `Deyoung`
   - **Address line 1:** `One Microsoft Way`
   - **City:** `Redmond`
   - **State:** **Washington**
   - **Zip:** `98052`
   - **Email:** Your administrator account

1. Select **Save**.

**You have successfully set the organization profile and sold-to address.**

### Task 2: Apply the company theme and conceal report names

1. Go back to **Settings** > **Org settings** > **Organization profile**, then select **Custom themes**.

1. Select **Default theme**, then select the **Colors** tab.

1. In **Navigation bar color**, enter `#0F6CBD`. Confirm that the contrast message doesn't report a contrast problem.

1. Select **Save**, then close the pane.

1. Select the **Services** tab, then select **Reports**.

1. Confirm that **Conceal user, group, and site names in all reports** is selected. If it isn't, select it, then select **Save**.

1. Close the **Reports** pane.

**You have successfully applied the company theme and concealed user names in reports.**

> [!NOTE]
> The theme changes the signed-in Microsoft 365 experience. Sign-in page branding is configured separately in the Microsoft Entra admin center under **Company branding**.

### Task 3: Diagnose a DNS record set (practice case)

You review the following DNS records for the sample domain `contoso.com`. You don't add or verify a domain in your tenant.

| Host | Type | Value | Priority |
| --- | --- | --- | --- |
| @ | TXT | `MS=ms12345678` | — |
| @ | MX | `contoso-com.mail.protection.outlook.com` | 0 |
| @ | TXT | `v=spf1 -all` | — |
| autodiscover | CNAME | `autodiscover.outlook.com` | — |
| sip | CNAME | `sipdir.online.lync.com` | — |
| lyncdiscover | CNAME | `webdir.online.lync.com` | — |
| enterpriseregistration | CNAME | `enterpriseregistration.windows.net` | — |

1. Identify the **verification** record: the `TXT` record `MS=ms12345678`. Microsoft 365 reads it to confirm domain ownership.

1. Identify the **service** records:
   - **MX:** Routes inbound mail to Exchange Online.
   - **autodiscover:** Lets Outlook find mailbox settings.
   - **sip** and **lyncdiscover:** Support Teams sign-in and discovery.
   - **enterpriseregistration:** Supports device registration.

1. Identify the fault: the SPF record `v=spf1 -all` authorizes no senders, so mail sent from Exchange Online fails SPF checks.

1. State the correction: `v=spf1 include:spf.protection.outlook.com -all`. Don't apply it to your tenant.

**You have successfully identified the verification record, the service records, and the SPF fault.**

---

## Exercise 2: Assign and troubleshoot service entitlements

**Estimated time:** 15 minutes.

### Scenario

Your organization wants to license new hires through group membership instead of one user at a time. You create two unlicensed users, place one in a licensing group directly and the other in a nested group, and then assign Microsoft 365 E7 to the parent group. Group-based licensing applies only to direct members, so you troubleshoot the nested user and fix the assignment path.

**Roles used:** **License Administrator** or **User Administrator** to create users and assign licenses; **Groups Administrator** to manage group membership.

### Task 1: Create two unlicensed users

1. In the **Microsoft 365 admin center** at `https://admin.cloud.microsoft`, select **Users** > **Active users**.

1. Select **Add a user**.

1. On the **Set up the basics** page, enter the following:
   - **First name:** `Adele`
   - **Last name:** `Vance`
   - **Display name:** `Adele Vance`
   - **Username:** `AdeleV`
   - **Domains:** Select your `*.onmicrosoft.com` domain
   - **Password settings:** Leave **Automatically create a password** selected

1. Select **Next**.

1. On the **Assign product licenses** page, select **Create user without product license**, then select **Next**.

1. On the **Optional settings** page, select **Next**.

1. On the **Review and finish** page, select **Finish adding**, then select **Close**.

1. Repeat steps 2–7 to create a second user:
   - **First name:** `Grady`
   - **Last name:** `Archie`
   - **Display name:** `Grady Archie`
   - **Username:** `GradyA`
   - **License:** **Create user without product license**

1. On the **Active users** page, confirm that the **Licenses** column shows **Unlicensed** for both users.

**You have successfully created two unlicensed users.**

### Task 2: Create a direct and a nested licensing group

1. In the **Microsoft Entra admin center** at `https://entra.microsoft.com`, select **Groups** > **All groups**.

1. Select **New group** and enter the following:
   - **Group type:** **Security**
   - **Group name:** `LAB1-Licensing-Direct`
   - **Membership type:** **Assigned**

1. Under **Members**, select **No members selected**, search for and select **Adele Vance**, and then select **Select**.

1. Select **Create**.

1. Repeat steps 2–4 to create a second group:
   - **Group name:** `LAB1-Licensing-Nested`
   - **Member:** **Grady Archie**

1. In **All groups**, open **LAB1-Licensing-Direct** and select **Members**.

1. Select **Add members**, search for `LAB1-Licensing-Nested`, select the group, and then select **Select**.

1. Select **Refresh**. Confirm that the members list shows **Adele Vance** and **LAB1-Licensing-Nested**.

**You have successfully created a licensing group with one direct member and one nested group.**

### Task 3: Assign E7 to the direct group

1. In the **Microsoft 365 admin center**, select **Billing** > **Licenses**.

1. Select **Microsoft 365 E7 (No Teams)**. Note the number of assigned licenses.

1. On the **Licenses** tab, select **Assign licenses**.

1. Search for and select `LAB1-Licensing-Direct`, and then select **Assign licenses**.

1. Close the confirmation pane. Confirm that **LAB1-Licensing-Direct** appears in the list with an assignment type of **Group**.

**You have successfully assigned Microsoft 365 E7 to a group.**

### Task 4: Troubleshoot the nested member

1. Note the assigned license count. It increased by **1**, not 2.

1. Select **Users** > **Active users**, and refresh the page.

1. Review the **Licenses** column:
   - **Adele Vance** shows **Microsoft 365 E7 (No Teams)**.
   - **Grady Archie** still shows **Unlicensed**.

Grady is a member of **LAB1-Licensing-Nested**, which is itself a member of **LAB1-Licensing-Direct**. Group-based licensing doesn't flow through nested groups, so only direct members of the licensed group receive the license. See [Add a group to another group](https://learn.microsoft.com/entra/fundamentals/how-to-manage-groups#add-a-group-to-another-group).

**You have successfully identified why a nested group member didn't receive a license.**

### Task 5: Fix the assignment and verify the AI entitlement

1. Select **Billing** > **Licenses** > **Microsoft 365 E7 (No Teams)**.

1. Select **Assign licenses**, search for and select `LAB1-Licensing-Nested`, and then select **Assign licenses**.

1. Close the confirmation pane. Confirm that both **LAB1-Licensing-Direct** and **LAB1-Licensing-Nested** are listed, and that the assigned count increased by **1**.

1. Select **Users** > **Active users**, refresh the page, and confirm that **Grady Archie** now shows **Microsoft 365 E7 (No Teams)**.

1. Select **Grady Archie**, and then select the **Licenses and apps** tab.

1. Confirm that **Microsoft 365 E7 (No Teams)** is selected.

1. Expand **Apps**, and confirm that services such as **Agent 365** and **Copilot Studio in Copilot for M365** are listed under **Microsoft 365 E7 (No Teams)**.

1. Close the pane without saving changes.

**You have successfully fixed a nested-group licensing gap and verified the user's AI services.**

> [!NOTE]
> Licenses and pay-as-you-go are separate entitlement paths. Pay-as-you-go agent usage requires a billing policy linked to an Azure subscription, which is a spending decision you don't make in this lab. See [Microsoft Copilot pay-as-you-go service overview](https://learn.microsoft.com/microsoft-365/copilot/pay-as-you-go/overview).

---

## Exercise 3: Investigate health, connectivity, and recovery readiness

**Estimated time:** 15 minutes.

### Scenario

Users report that Copilot and Teams "are down." Before you escalate, you determine whether the cause is a Microsoft service incident or the users' network path. You also check how ready the tenant is to recover overwritten content.

**Roles used:** **Global Administrator**. Microsoft 365 Backup setup also requires the **Owner** or **Contributor** role on an Azure subscription, which you don't have in this lab.

### Task 1: Review service health and configure an email notification

1. On **SEA-DEV1**, in the Microsoft 365 admin center, select **Health** > **Service health**.

1. On the **Overview** tab, review the service list and any active issues.

1. Select the **Issue history** tab, and record the ID of one recent incident or advisory.

1. Select **Customize**, and then select the **Email** tab.

1. Select **Send me email notifications about service health**.

1. Leave **Primary email address** selected, and leave **Incidents**, **Advisories**, and **Issues in your environment that require action** selected.

1. Leave all services selected, and then select **Save**.

**You have successfully reviewed service health and configured a service health email notification.**

### Task 2: Run the network connectivity test from two clients

1. On **SEA-DEV1**, in the Microsoft 365 admin center, select **Health** > **Network connectivity**.

   > [!NOTE]
   > An empty **Overview** means the tenant doesn't have enough location telemetry yet. It doesn't mean the network is healthy.

1. Select **Network connectivity test**. The Microsoft 365 network connectivity test opens in a new tab.

1. Select **Sign in** and sign in with your administrator account so that the results are saved.

1. Select **Run test**. When Microsoft Edge asks to use your location, select **Allow**.

1. When the report opens and `Connectivity.<id>.exe` finishes downloading, press **Ctrl+J**, and then select **Open file** under the downloaded file.

1. Wait for the tool to finish its tests (1–3 minutes). When **Testing is complete** appears, select **Close**.

1. On the report page, select the **Details** tab, and record the following values:
   - **Location:** Your network egress location.
   - **Exchange:** The service front door location.
   - **Copilot:** The HTTPS and WebSocket (WSS) connectivity results.
   - **Teams:** The UDP packet loss percentage.
   - **Connectivity:** Any URLs the test asks you to unblock.

1. Switch to **SEA-DEV2**, and repeat steps 1–7. If Windows asks whether to allow apps to use your location, select **No**.

1. Compare the two results. When both clients report similar Teams packet loss and Service Health shows no matching incident, the cause is the network path from the lab environment, not a Microsoft service.

**You have successfully compared network connectivity test results from two clients with service health.**

### Task 3: Inspect the Microsoft 365 Backup setup prerequisites

1. On **SEA-DEV1**, in the Microsoft 365 admin center, select **Show all** > **Settings** > **Microsoft 365 Backup**.

1. Review the three setup steps: **Things to know**, **Connect a billing policy**, and **Start backing up**.

1. Under step 2, select **Go to Pay-as-you-go**.

1. On the **Pay-as-you-go** page, confirm that **Add a billing policy** is unavailable and that the page states that you need the **Owner** or **Contributor** role for an Azure subscription.

1. Select the **Services** tab. Confirm that **Microsoft 365 Backup** is listed with **Connect a policy**. Don't select it.

**You have successfully identified the Azure billing prerequisite that blocks Microsoft 365 Backup setup.**

### Task 4: Plan a SharePoint restore (practice case)

A user reports that a finance workbook in a SharePoint site was overwritten with bad data two days ago. Assume Microsoft 365 Backup protects the site and has a restore point from before the overwrite. Other team members have edited other files in the site since then. You don't perform the restore.

1. State the acceptance criteria: the workbook is restored to its state before the overwrite, and the site owner confirms that its content is correct.

1. Choose the restore scope: **Selected content only**. A full site restore to the same URL would roll back the other team members' edits.

1. Choose the destination: **Create a new folder and restore to it**. The site owner can compare the restored workbook with the current one before replacing it.

1. Identify who approves the restore point: the site owner and the business owner of the finance data.

1. State whether file version history could fix this first. If the workbook still has a good earlier version, a version history restore is faster, but it isn't a Microsoft 365 Backup restore.

**You have successfully planned a scoped SharePoint restore.**

> [!NOTE]
> A **Selected content only** restore requires the **SharePoint Backup Admin** role. For more information, see [Microsoft 365 Backup overview](https://learn.microsoft.com/microsoft-365/backup/backup-overview), [Set up Microsoft 365 Backup](https://learn.microsoft.com/microsoft-365/backup/backup-setup), and [Restore data in Microsoft 365 Backup](https://learn.microsoft.com/microsoft-365/backup/backup-restore-data).

---

## Summary

In this lab, you established a tenant baseline by setting the organization profile, applying a company theme and a scoped privacy setting, and separating domain verification from service records in a DNS practice case. You created two unlicensed users, licensed them with Microsoft 365 E7 through group-based licensing, confirmed that a nested group doesn't inherit the parent group's license, corrected the assignment, and verified the Microsoft 365 Copilot and Agent 365 service plans. You configured a Service Health email notification, compared network connectivity test results from two clients with service health, identified the Azure billing prerequisite that blocks Microsoft 365 Backup setup, and planned a scoped SharePoint restore.

You keep everything you created here. Lab 2 builds on this tenant to enable collaboration and govern its content.
