---
lab:
  title: 'Lab 5 - Protect and govern information'
  description: 'Create and test Microsoft Purview classification, sensitivity-label, DLP, retention, and DSPM controls with sample finance content.'
  duration: 55
  level: 300
  islab: true
  primarytopics:
    - Microsoft Purview information protection
    - Sensitive information types
    - Sensitivity labels
    - Data loss prevention
    - Retention and DSPM
---

# Lab 5: Protect and govern information

Information protection starts with identifying sensitive content, then applying the right handling and governance controls. In this lab, you create a custom sensitive information type (SIT), publish an encrypted sensitivity label, create a DLP policy in simulation mode, publish a retention label for separate content, and inspect Data Security Posture Management (DSPM).

You continue in the same Microsoft 365 E7 tenant from Lab 4. **Patti Fernandez** acts as a finance user who should see and use the label, and **Alex Wilber** acts as a user outside the label's encryption permissions.

This lab contains two exercises:

- **Exercise 1: Classify and publish**
- **Exercise 2: Prevent loss and govern lifecycle**

This lab takes approximately **55 minutes** to complete.

## Learning objectives

By the end of this lab, you'll be able to:

- Create a custom sensitive information type with a regular expression, then test matching and near-miss samples.
- Create an item-scoped sensitivity label that encrypts files and grants access only to a test group.
- Publish a sensitivity label and verify label availability, priority, and access behavior in Office on the web.
- Create a DLP policy in simulation mode across Microsoft 365 workloads and review simulation evidence.
- Publish a retention label for a separate item, then inspect DSPM objectives and oversharing assessment evidence.

> [!IMPORTANT]
> Use only the sample data in this lab. Keep DLP policies in simulation mode, use a copy of any finance file, and don't paste real customer, payment, or financial records into a tester, document, prompt, or message.

## Before you start

You complete this lab signed in as a tenant administrator. The following conditions are provided by the lab environment, so you don't set them up:

| Provided by the environment | Detail |
| --- | --- |
| Tenant | The Lab 4 Microsoft 365 **E7** tenant with Microsoft Purview capabilities and an assigned `*.onmicrosoft.com` domain. |
| People | Nine ready-to-use sample people, already licensed for Microsoft 365 E7. You use Patti Fernandez, Nestor Wilke, and Alex Wilber in this lab. |
| Groups | Seeded groups include **Finance Users** with Patti Fernandez and Nestor Wilke, but you create a separate Lab 5 test group for label publishing and encryption. |
| Finance file | A copyable finance file from earlier labs. Use a copy only, such as the copy in the Lab 2 `LAB2-Finance` site, and leave the original Agent 365 knowledge source unchanged. |
| Lab assets | `05-dlp-design-worksheet.md` for recording DLP design choices and `05-dlp-dspm-evidence.md` as a clearly labeled sample case. |
| Test client | **SEA-DEV2**, where you sign in as sample people to test label availability and protected-content access. |
| Your access | An administrator account with the roles noted at the start of each exercise. |

You keep the work you create in this lab. Later exercises and later labs build on it, so there's no end-of-lab cleanup.

---

## Exercise 1: Classify and publish

**Estimated time:** 20 minutes.

### Scenario

Finance transaction references need consistent classification before the organization can protect them. You create a custom SIT for a fictional transaction-reference format, test it against matching and near-miss samples, create an encrypted sensitivity label for item content, and publish the label to a test group.

**Roles used:** **Compliance Administrator** or **Information Protection Administrator**, and **Groups Administrator**.

### Task 1: Create a test group for label publishing

A separate test group gives you a safe target for the label policy and the label's encryption permissions. Label policies and encryption permissions need a group with an email address, so you create a mail-enabled security group. Patti Fernandez represents a finance user who should receive the label.

1. On **SEA-DEV1**, open the **Microsoft 365 admin center** at `https://admin.microsoft.com`.

1. Select **Teams & groups** > **Active teams & groups**, then select the **Security groups** tab.

1. Select **Add a mail-enabled security group**.

1. Enter the following, then select **Next**:
   - **Name:** `LAB5-Information-Protection-Test`
   - **Description:** `Lab 5 test group for Purview label publishing and encryption`

1. Select **Assign owners**, search for `MOD` and press **Enter**, select **MOD Administrator**, select **Add**, and then select **Next**.

1. Select **Add members**. Search for and select **Patti Fernandez**, then search for and select **Nestor Wilke**. Select **Add (2)**, then select **Next**.

1. In **Group email address**, enter `lab5iptest`. Leave the option to allow external senders cleared, then select **Next**.

1. Confirm that **Group type** is **Mail-enabled security**, select **Create group**, and then select **Close**.

**You have successfully created the Lab 5 test group for label publishing.**

### Task 2: Create and test a custom sensitive information type

The sample pattern is a `WG-` prefix, eight digits, a hyphen, and a check letter, such as `WG-48213077-K`. For more information, see [Create custom sensitive information types](https://learn.microsoft.com/purview/create-a-custom-sensitive-information-type).

1. On **SEA-DEV1**, open **Notepad**, enter the following matching sample text, and save it to **Documents** as `Lab5-SIT-Match.txt`:

   ```text
   WG-48213077-K
   WG-12345678-A
   wg-48213077-k
   ```

1. In Notepad, create a new file with the following near-miss sample text, save it to **Documents** as `Lab5-SIT-NearMiss.txt`, and then close Notepad:

   ```text
   WG-48213077
   WG-4821307-K
   WG-482130777-K
   WG-48213077-K9
   ```

1. In Microsoft Edge, open the **Microsoft Purview portal** at `https://purview.microsoft.com`.

1. Select **Solutions** > **Information Protection**. If a welcome dialog appears, select **Get started**, and close any tour that follows.

1. Under **Classifiers**, select **Sensitive info types**, then select **Create sensitive info type**.

1. On the **Name** page, enter the following, then select **Next**:
   - **Name:** `Woodgrove transaction reference`
   - **Description:** `Matches fictional Woodgrove transaction references in the WG-########-L format.`

1. On **Define patterns for this sensitive info type**, select **Create pattern**.

1. In **New pattern**, leave **Confidence level** set to **High confidence**, then select **Add primary element** > **Regular expression**.

1. In **Add a regular expression**, enter the following, leave **String match** selected, and then select **Done**:
   - **ID:** `WoodgroveTransactionReference`
   - **Regular expression:** `\b([Ww][Gg]-[0-9]{8}-[A-Za-z])\b`

1. In **New pattern**, select **Create**, then select **Next**.

1. On **Choose the recommended confidence level to show in compliance policies**, leave **High confidence level** selected, then select **Next**.

1. On **Review settings and finish**, select **Create**. When **Your sensitive info type is created** appears, select **Done**.

1. In **Sensitive info types**, search for `Woodgrove`, select **Woodgrove transaction reference**, and then select **Test** in the details pane.

1. Select **Upload file**, select **Documents** > `Lab5-SIT-Match.txt`, and then select **Test**.

1. On **Match results**, confirm that **Woodgrove transaction reference** reports **3 unique matches**, then select **Finish**.

   The results list the same three matches at **Low**, **Medium**, and **High** confidence because a match at the pattern's confidence level also satisfies the lower levels.

1. Select **Test** again, upload `Lab5-SIT-NearMiss.txt`, and then select **Test**.

1. Confirm that **Match results** reports that no sensitive information was detected, then select **Finish**.

**You have successfully created and tested a custom sensitive information type.**

### Task 3: Create an item-scoped sensitivity label with encryption

A sensitivity label can protect the file itself, not only the site or folder where the file is stored. In this task, the label grants access to the Lab 5 test group and adds a visible footer.

1. In the **Microsoft Purview portal**, select **Solutions** > **Information Protection** > **Sensitivity labels**.

1. Select **Create** > **Label**.

1. On **Provide basic details for this label**, enter the following, leave **Label priority** set to **Highest**, and then select **Next**:
   - **Name:** `Woodgrove Confidential - Transaction Records`
   - **Display name:** `Woodgrove Confidential - Transaction Records`
   - **Description for users:** `Use for sample transaction-reference files that require finance-only access.`
   - **Description for admins:** `Lab 5 item-scoped label with encryption for the LAB5-Information-Protection-Test group.`

1. On **Define the scope for this label**, close the **Extend labels beyond Microsoft 365** callout if it appears. Keep **Files & other data assets** and **Emails** selected, clear **Meetings**, and then select **Next**.

1. On **Choose protection settings for the types of items you selected**, select **Control access** and **Apply content marking**, then select **Next**.

1. On **Access control**, keep **Configure access control settings** and **Assign permissions now** selected. Keep **User access to content expires** set to **Never** and **Allow offline access** set to **Always**.

1. Select **Assign permissions**, then select **Add users or groups**.

1. Search for `LAB5`, select **LAB5-Information-Protection-Test**, and then select **Add**.

1. Keep the **Editor** permissions, then select **Save**.

1. Confirm that the group's email address appears with **Co-Author** permissions, then select **Next**.

1. On **Content marking**, turn on **Content marking**. Confirm that **Add a footer** is selected, then select **Customize text**.

1. In **Footer text**, enter `Confidential - Woodgrove transaction data`, select **Save**, and then select **Next**.

1. On **Auto-labeling for files and emails**, leave the setting off and select **Next**.

1. On **Define protection settings for groups and sites**, leave all settings cleared and select **Next**.

1. On **Review your settings and finish**, select **Create label**.

1. On **Your sensitivity label was created**, select **Don't create a policy yet**, then select **Done**.

1. Confirm that **Woodgrove Confidential - Transaction Records** appears in **Sensitivity labels** with the highest priority number in the list.

**You have successfully created an encrypted item-scoped sensitivity label.**

### Task 4: Publish the label and verify availability in Office on the web

Sensitivity labels aren't available to users until you publish them in a label policy. Label and label-policy changes can take up to 24 hours to propagate through services, and group membership changes can take 24-48 hours.

1. In the **Microsoft Purview portal**, select **Solutions** > **Information Protection** > **Policies** > **Label publishing policies**.

1. Select **Publish label**.

1. On **Choose sensitivity labels to publish**, select **Choose sensitivity labels to publish**, select **Woodgrove Confidential - Transaction Records**, select **Add**, and then select **Next**.

1. On **Assign admin units**, keep **Full directory**, then select **Next**.

1. On **Publish to users and groups**, select **Edit** for **Exchange email**.

1. Select **Include only specific**, then select **Add**.

1. Search for `LAB5`, press **Enter**, select **LAB5-Information-Protection-Test**, and then select **Add (1)**.

1. Select **Save and close**. Confirm that the scope shows **1 group**, then select **Next**.

1. On **Policy settings**, select **Users must provide a justification to remove a label or lower its classification**, then select **Next**.

1. On **Default settings for documents**, keep **Default label** set to **None**, then select **Next**.

   An encrypted default label would encrypt every new or unlabeled document that the test group opens, including files used in later labs.

1. On **Default settings for emails**, keep **Default label** set to **None**, then select **Next**.

1. On **Default settings for meetings and calendar events** and **Default settings for Fabric and Power BI content**, keep the defaults and select **Next**.

1. In **Name**, enter `Finance - Woodgrove transaction labeling`, then select **Next**.

1. On **Review and finish**, confirm the label, the **Exchange email - 1 account** scope, and the justification setting, then select **Submit**.

1. On **New policy created**, select **Done**.

1. In **Label policies**, confirm that **Finance - Woodgrove transaction labeling** appears with the highest **Priority** number in the list and a **Policy sync status** of **Sync in progress** or **Sync completed**.

   When more than one label policy applies to a user, the policy with the highest priority number provides that user's default label and policy settings.

1. On **SEA-DEV2**, open an InPrivate window, go to `https://<yourtenant>.sharepoint.com/sites/LAB2-Finance/Shared%20Documents`, and sign in as `PattiF@<yourtenant>.onmicrosoft.com` with the user password provided by your lab hoster.

1. Select **Contoso_Finance_Policy.docx** to open it in Word for the web.

1. Select **File** > **Create a Copy** > **Create a copy online**. In **File Name**, enter `Lab5-Woodgrove-Transaction-Records`, keep the current location, and then select **Create a Copy**. The copy opens in a new tab.

   You label the copy only. The original stays unlabeled.

1. In the copy, select **Sensitivity** > **Woodgrove Confidential - Transaction Records**. Confirm that a lock icon appears next to the document name and that the footer **Confidential - Woodgrove transaction data** appears at the bottom of the page.

   If the label isn't listed yet, record **Label availability pending** with the current date and time, close the InPrivate window, and continue with Exercise 2.

1. Select **Copilot** at the lower right of the document. In **Message Copilot**, enter `Summarize this document in three bullets.`, and press **Enter**. Confirm that Copilot summarizes the document, because Patti is in the label's permissions group.

1. Select **Share** > **Share**. Enter `Alex Wilber`, select Alex, and then select **Send**. Confirm that **You've invited Alex Wilber to edit** appears, then close the InPrivate window.

1. Open a new InPrivate window, go to `https://<yourtenant>.sharepoint.com/sites/LAB2-Finance/Shared%20Documents`, and sign in as `AlexW@<yourtenant>.onmicrosoft.com` with the user password provided by your lab hoster. Alex sees only the shared copy.

1. Select **Lab5-Woodgrove-Transaction-Records.docx**. Confirm that the new tab shows **Sorry, you don't have permission to open this document** because the document is protected by a rights management service. The sharing grant doesn't override the label's encryption permissions.

1. Go to `https://m365.cloud.microsoft/chat`. Enter `Summarize Lab5-Woodgrove-Transaction-Records from the LAB2-Finance site.`, and press **Enter**. Confirm that Copilot can't find or summarize the document for Alex.

1. Close the InPrivate window.

**You have successfully published the sensitivity label and verified protected access for Patti and Alex.**

---

## Exercise 2: Prevent loss and govern lifecycle

**Estimated time:** 35 minutes.

### Scenario

A classification result becomes useful when other governance controls respond to it. You create a DLP policy in simulation mode for Microsoft 365 workloads, test it with a harmless file, publish a retention label for a separate item, inspect DSPM, and interpret a sample case that doesn't represent a live tenant incident.

**Roles used:** **Compliance Administrator** or **Data Loss Prevention Administrator**.

### Task 1: Create a DLP policy in simulation mode

Simulation mode records matches without enforcing policy actions. For more information, see [Create and deploy data loss prevention policies](https://learn.microsoft.com/purview/dlp-create-deploy-policy).

1. On **SEA-DEV1**, open the **Microsoft Purview portal** at `https://purview.microsoft.com`.

1. Select **Solutions** > **Data Loss Prevention** > **Policies**, then select **Create policy**.

1. On **What info do you want to protect?**, select **Enterprise applications & devices**.

1. Under **Categories**, select **Custom**. Under **Regulations**, select **Custom policy**, then select **Next**.

1. Replace the default name with `Finance - transaction protection (simulation)`, replace the default description with `Lab 5 simulation policy for fictional transaction references`, and then select **Next**.

1. On **Assign admin units**, keep **Full directory**, then select **Next**.

1. On **Choose where to apply the policy**, keep **Exchange email**, **SharePoint sites**, **OneDrive accounts**, and **Teams chat and channel messages** selected with their default scopes.

1. Clear **Devices**, **Instances**, and **On-premises repositories**, and leave every other location cleared. Select **Next**.

1. On **Define policy settings**, keep **Create or customize advanced DLP rules** selected, then select **Next**.

1. Select **Create rule**. If a **Create compound conditions** tip appears, close it.

1. In **Name**, enter `Detect Woodgrove transaction references`.

1. Under **Conditions**, select **Add condition** > **Content is shared from Microsoft 365**, then change the dropdown to **with people outside my organization**.

1. Select **Add condition** > **Content contains**. Keep the operator between the two conditions set to **AND**.

1. In the **Content contains** group, select **Add** > **Sensitive info types**. Search for `Woodgrove`, press **Enter**, select **Woodgrove transaction reference**, and then select **Add**. Keep **High confidence** and an instance count of **1** to **Any**.

1. Under **Actions**, select **Add an action** > **Restrict access or encrypt the content in Microsoft 365 locations**. Keep **Block users from receiving email, or accessing shared SharePoint, OneDrive, Teams files...** and select **Block only people outside your organization**.

1. Under **User notifications**, turn on notifications, then select **Notify users in Office 365 service with a policy tip or email notifications**. If a policy tips message appears, select **Got it**.

1. Under **Policy tips**, select **Customize the policy tip text**, then enter `This item contains a sample transaction reference. Sharing outside the organization is restricted by policy.`

1. Under **Incident reports**, set the severity level to **Medium**, keep the admin alert on, and then select **Save**.

1. On **Customize advanced DLP rules**, confirm that the rule is listed, then select **Next**.

1. On **Policy mode**, keep **Run the policy in simulation mode** and **Show policy tips while in simulation mode** selected. Leave **Turn the policy on if it's not edited within fifteen days of simulation** cleared, then select **Next**.

1. Review the policy, select **Submit**, and then select **Done**.

1. In **Policies**, confirm that `Finance - transaction protection (simulation)` shows the mode **In simulation with notifications**.

1. Open `05-dlp-design-worksheet.md` and record the policy name, locations, conditions, action, and simulation mode. Record **Device DLP not tested** for the Devices location.

**You have successfully created a DLP policy in simulation mode.**

### Task 2: Test with a harmless file and review simulation evidence

A simulation result can take time to appear. You record the live evidence if it appears during the lab, or record the result as pending if it hasn't arrived yet.

1. On **SEA-DEV2**, open an InPrivate window, go to `https://<yourtenant>.sharepoint.com/sites/LAB2-Finance/Shared%20Documents`, and sign in as `PattiF@<yourtenant>.onmicrosoft.com` with the user password provided by your lab hoster.

1. Select **Create or upload** > **Word document**. The new document opens in a new tab.

1. In the document body, enter the following text:

   ```text
   This is a fictional training file.
   Sample transaction reference: WG-48213077-K.
   ```

1. Select the document name at the top of the window, enter `Lab5-DLP-Simulation-Test`, and press **Enter**.

1. Select **Share** > **Share**, enter `external.recipient@fabrikam.com`, and press **Enter**. On **Share outside of your organization?**, select **Continue**.

   > **Note:** Fabrikam is a fictitious sample domain. Don't use a real customer's address.

1. Select **Send**, and confirm that the invitation is sent. The policy runs in simulation mode, so the share isn't blocked.

1. Close the confirmation, then select **Share** > **Share** again. Share the file with **Nestor Wilke**, then select **Send**.

1. Close the InPrivate window.

1. On **SEA-DEV1**, in the **Microsoft Purview portal**, select **Solutions** > **Data Loss Prevention** > **Policies**.

1. Select `Finance - transaction protection (simulation)`. In the details pane, review **Simulation progress**, then select **View simulation**.

1. If **Welcome to simulation** appears, close it. Select **Items for review**, and confirm that `Lab5-DLP-Simulation-Test.docx` is listed with the rule **Detect Woodgrove transaction references** and the location **SharePoint**. Other sample files that contain a transaction reference might also be listed.

1. In the **Data Loss Prevention** navigation, select **Explorers** > **Activity explorer**. Select **Activity: Any**, select **DLP rule matched**, and then select **Apply**.

1. Confirm that a **DLP rule matched** row lists `Lab5-DLP-Simulation-Test.docx` for Patti with the policy `Finance - transaction protection (simulation)`. The internal share with Nestor doesn't match, because the rule covers only content shared outside the organization.

1. In `05-dlp-design-worksheet.md`, record the match. If the file doesn't appear yet, record **DLP simulation results pending** with the current date and time.

**You have successfully tested the DLP policy and recorded the simulation evidence or pending state.**

### Task 3: Publish a retention label for separate content

Retention governs how long content is kept or when it's removed. Use a separate item for retention so the DLP and sensitivity-label tests stay distinct.

1. On **SEA-DEV1**, in the **Microsoft Purview portal**, select **Solutions** > **Data Lifecycle Management** > **Retention labels**.

1. Select **Create a label**.

1. In **Name**, enter `Finance transaction retention - test`. In **Description for users**, enter `Lab 5 retention label for a separate sample item`, and then select **Next**.

1. On **Define label settings**, keep **Retain items forever or for a specific period**, then select **Next**.

1. On **Define the retention period**, set **Retain items for** to **Custom**, enter **0** years, **1** month, and **0** days, keep **When items were created**, and then select **Next**.

1. On **Choose what happens after the retention period**, select **Deactivate retention settings**, then select **Next**.

   > **Note:** The default, **Delete items automatically**, would remove test content when the period ends. Don't keep it for this lab.

1. Review the label, then select **Create label**.

1. On **Your retention label is created**, keep **Publish this label to Microsoft 365 locations**, then select **Done**.

1. On **Choose labels to publish**, confirm that `Finance transaction retention - test` is listed, then select **Next**.

1. On **Policy Scope**, keep **Full directory**, then select **Next**.

1. On **Choose the type of retention policy to create**, select **Static**, then select **Next**.

1. Select **Let me choose specific locations**. Turn off **Exchange mailboxes**, **SharePoint classic and communication sites**, and **OneDrive accounts**.

1. For **Microsoft 365 Group mailboxes & sites**, select **Edit** under **Included**, select **LAB2-Finance**, and then select **Done**. Select **Next**.

1. In **Name**, enter `Finance transaction retention - test policy`, then select **Next**.

1. Confirm that the policy applies to **Microsoft 365 Group mailboxes & sites (1 Group)**, select **Submit**, and then select **Done**.

1. On **SEA-DEV2**, open an InPrivate window, go to `https://<yourtenant>.sharepoint.com/sites/LAB2-Finance/Shared%20Documents`, and sign in as `PattiF@<yourtenant>.onmicrosoft.com` with the user password provided by your lab hoster.

1. Select **Create or upload** > **Word document**. The new document opens in a new tab.

1. In **Describe what you'd like to draft with Copilot**, enter the following prompt, then select the send arrow:

   ```text
   Draft a short, fictional note to the finance team about an upcoming quarterly records review. Don't include account numbers, transaction references, or personal data.
   ```

1. Review the draft in the document, then select **Done**.

1. Select the document name at the top of the window, enter `Lab5-Retention-Test`, press **Enter**, and then close the tab.

1. In the **Documents** library, select `Lab5-Retention-Test.docx`, then select **Details**. Under **Apply label**, select **Choose a label**. If **Finance transaction retention - test** is listed, select it and confirm that the field shows the label.

1. If the label isn't listed yet, record **Retention label availability pending** with the current date and time in `05-dlp-design-worksheet.md`. Published retention labels can take up to seven days to appear. Close the InPrivate window.

**You have successfully published a retention label for separate content or recorded propagation as pending.**

### Task 4: Inspect DSPM and interpret the sample case

Data Security Posture Management (DSPM) combines data security posture and AI activity in one solution. It replaces DSPM for AI (classic). The supplied DLP and DSPM case is sample evidence only, so don't search a portal for its fictional incident, users, sites, or files.

1. In the **Microsoft Purview portal**, select **Solutions** > **DSPM**. Don't select **DSPM for AI (classic)**. It retires on November 30, 2026.

1. If **DSPM and DSPM for AI unified in one solution** appears, select **Get started**.

1. On **Complete setup to unlock the unified DSPM experience**, confirm that **Auditing and analytics** shows **Audit is on**. Leave **Collection policies for AI** unchanged, select **Start setup**, and then select **Close**.

   > **Note:** The collection policies use Purview pay-as-you-go billing, which requires an Azure subscription linked to Purview. You don't need them in this lab. Interactions from Microsoft 365 Copilot, agents, and Copilot Studio are captured without these policies.

1. Select **Objectives**. In **Prevent data exposure in Microsoft 365 Copilot and Microsoft Copilot interactions**, record the number of interactions, user prompts, and Copilot responses with sensitive data.

1. In **Prevent oversharing of sensitive data**, select **View remediation plan**. Record the actions it lists, and mark the ones you already completed in this lab, such as the custom DLP policy. Select **Cancel**.

1. Select **Discover** > **Data risk assessments**, then keep the **Microsoft 365** tab selected.

1. If default assessment results are available, record one overshared site or item, its access pattern, and the smallest reasonable remediation. Otherwise, record **DSPM assessment pending** with the current date and time. The default assessment runs weekly, so a new tenant might not have results yet.

1. Open `05-dlp-dspm-evidence.md`. Treat it as **sample case evidence, not from this tenant**.

1. In the sample DLP case, identify the matched policy, matched rule, affected user, affected content, workload, SIT, and severity.

1. In the sample DSPM case, compare **Finance Transaction Archive** with **Finance Team Announcements**. Confirm that only **Finance Transaction Archive** needs remediation because its access is broader than its purpose.

1. Choose the smallest supported remediation for the sample case: remove organization-wide links or tighten sharing on **Finance Transaction Archive**, then escalate to the site owner for confirmation. Don't delete content or disable a user based on the sample case.

**You have successfully inspected DSPM and interpreted the sample DLP and DSPM case without treating it as a live incident.**

### If something doesn't work

| Symptom | Likely cause | Recovery |
| --- | --- | --- |
| The SIT test pane has no text box | The SIT **Test** pane accepts only an uploaded file. | Upload `Lab5-SIT-Match.txt` or `Lab5-SIT-NearMiss.txt` from **Documents**, one file at a time. |
| Woodgrove transaction reference doesn't appear in **Sensitive info types** | The list shows Microsoft-provided types first and isn't filtered. | Search for `Woodgrove`, or select **Refresh** and search again. |
| The near-miss sample matches the SIT | The regular expression was entered with a missing boundary, digit count, hyphen, or letter class. | Compare the expression with `\b([Ww][Gg]-[0-9]{8}-[A-Za-z])\b`, correct it, and rerun both tests. |
| The sensitivity label doesn't appear in Word for the web | Label policy or group membership propagation hasn't completed. | Wait up to 24 hours before troubleshooting label policy changes. For new group or membership changes, allow 24-48 hours and record the verification as pending. |
| Alex can open the protected copy | The label wasn't applied, the wrong file was shared, or Alex received encryption permissions through another group. | Confirm that the copied file shows the label, confirm that the label grants access only to the Lab 5 test group, and retest with a fresh InPrivate session. |
| The DLP policy blocks an action | The policy was turned on instead of left in simulation mode. | Edit the policy and set the mode back to **Run the policy in simulation mode**. Don't leave an enforced block in this lab. |
| No DLP simulation event appears | Policy evaluation or activity reporting hasn't caught up, or the test activity didn't use content that matches the SIT. | Confirm the file contains `WG-48213077-K`, then record **DLP simulation results pending** and revisit the DLP simulation overview or **Activity explorer** later. |
| The retention label doesn't appear on the item | Retention label publication is still distributing. | For SharePoint or OneDrive, wait at least one day and allow up to seven days before troubleshooting policy status. |
| DSPM has no completed assessment or objective data | The default assessment runs weekly, and objective metrics need activity first. | Record **DSPM assessment pending**, then complete the sample-case interpretation from `05-dlp-dspm-evidence.md`. |
| The collection policy switches in DSPM setup can't be turned on | The collection policies use Purview pay-as-you-go billing, and no Azure subscription is linked to Purview. | Leave them off and select **Start setup**. Microsoft 365 Copilot interactions are captured without them. |
| Submitting the DLP policy fails with an error | **Restrict access or encrypt the content in Microsoft 365 locations** with **Only people outside your organization** requires the external-sharing condition first. | Edit the rule so that **Content is shared from Microsoft 365** > **with people outside my organization** is the first condition, joined to **Content contains** with **AND**. |
| A 30-day retention period can't be entered | The **Custom** retention period's days box accepts a maximum of 29. | Enter **0** years, **1** month, and **0** days. |

---

## Summary

In this lab, you created a custom sensitive information type for fictional transaction references, tested matching and near-miss samples, created an encrypted item-scoped sensitivity label, and published it through a label policy. You verified label availability and protected-file access when propagation allowed, or recorded the result as pending. You then created a DLP policy in simulation mode, tested it with a harmless file, published a retention label for separate content, inspected DSPM objectives and data risk assessments, and interpreted a clearly labeled sample DLP and DSPM case without treating it as a live tenant incident.

You keep everything you created here. Lab 6 builds on this tenant to roll out Copilot and administer Cowork.
