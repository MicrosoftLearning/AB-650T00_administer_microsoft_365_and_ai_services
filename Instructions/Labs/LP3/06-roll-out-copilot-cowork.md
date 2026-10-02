---
lab:
  title: 'Lab 6 - Roll out and administer Copilot and Cowork'
  description: 'Gate a Microsoft 365 Copilot rollout, route Copilot settings to the right admin controls, and scope Microsoft 365 Copilot Cowork with the tenant credit method.'
  duration: 60
  level: 300
  islab: true
  primarytopics:
    - Microsoft 365 Copilot readiness
    - Copilot settings
    - Copilot connectors
    - Microsoft 365 Copilot Cowork
    - Copilot Credits
---

# Lab 6: Roll out and administer Copilot and Cowork

Microsoft 365 Copilot rollout starts with evidence: the intended users have the right license, the content they can reach is governed, and the first responses cite the expected work data. In this lab, you use the readiness information in the Microsoft 365 admin center, inspect tenant Copilot settings, verify a seeded connector boundary, and then scope Microsoft 365 Copilot Cowork to one group with a capped spending policy.

You continue in the same Microsoft 365 E7 tenant from Lab 5. **Megan Bowen** is the first Microsoft 365 Copilot and Cowork test user. **Patti Fernandez** is the intended finance reviewer, and **Diego Siciliani** is the denied user for the finance review content and the connector search test.

This lab contains three exercises:

- **Exercise 1: Gate a Copilot rollout**
- **Exercise 2: Match Copilot controls to experiences**
- **Exercise 3: Grant, use, and observe Cowork**

This lab takes approximately **60 minutes** to complete.

## Learning objectives

By the end of this lab, you'll be able to:

- Use Microsoft 365 Copilot readiness and license information to decide whether a small rollout wave can start.
- Gate Copilot access with a security group and verify one grounded response against a seeded work file.
- Route Copilot web search, connectors, and AI provider requirements to their owning settings without changing the wrong control.
- Inspect a connector as a discovery control and verify the intended and denied user outcomes.
- Create Cowork access groups, scope a spending policy to the funded group, run Cowork tasks as Megan and Patti, check their cost with `/cost`, and interpret usage or a pending usage result.

> [!IMPORTANT]
> This lab spans two teaching days. Complete Exercise 1 at the end of Day 2, then stop at the overnight pause. Resume with Exercise 2 and Exercise 3 on Day 3. Don't reset the tenant or delete groups, policies, content, or evidence between exercises.

## Before you start

You complete the administrator work on **SEA-DEV1** and the persona tests on **SEA-DEV2**. The following conditions are provided by your lab hoster, so you don't set them up:

| Provided by the environment | Detail |
| --- | --- |
| Tenant | The same Microsoft 365 **E7** tenant used in earlier labs, with Microsoft 365 Copilot, Microsoft 365 Copilot Cowork, and Copilot Credits enabled. |
| People | Nine E7-licensed sample people. You use Megan Bowen, Patti Fernandez, Diego Siciliani, and Nestor Wilke in this lab. |
| Finance review site | The `AB650-Finance-Review` site from Lab 2, where Patti Fernandez should retain access and Diego Siciliani should be denied. |
| Beacon work content | Megan Bowen has seeded Beacon mail, the **Beacon War Room** Teams chat, and files, including `Beacon Rollout Plan.docx` and `Beacon Pilot Metrics.xlsx`. `Beacon GA Decision Options.docx` is in Isaiah Langer's OneDrive and isn't shared with Megan. |
| Connector | The external connection `AB650-Policy-Connector` with connection ID `ab650policy` and one item titled **Finance review policy**. Patti is granted access and Diego is denied. |
| Cowork funding | The tenant has Copilot Credit capacity packs, and Cost management hasn't been activated. You don't create or connect Azure billing. |
| Test client | **SEA-DEV2**, where you sign in as sample people with the user passwords provided by your lab hoster. |
| Your access | An administrator account with the roles noted at the start of each exercise. |

You keep the work you create in this lab. Later exercises and later labs build on it, so there's no end-of-lab cleanup.

---

## Exercise 1: Gate a Copilot rollout

**Estimated time:** 10 minutes.

### Scenario

A small rollout wave is ready only when the license, readiness, and data-access evidence all line up. You review Copilot readiness, make the rollout group match your decision, verify one grounded response as Megan, and record an honest pending result if indexing isn't ready.

**Roles used:** **AI Administrator** or **License Administrator** for readiness and license review, **Groups Administrator** for group membership, and **SharePoint Administrator** to read site permissions.

### Task 1: Review readiness and finance-site access

Use readiness and access evidence together. A license doesn't prove that the user's content boundary is safe.

1. On **SEA-DEV1**, in Microsoft Edge, go to the **Microsoft 365 admin center** at `https://admin.microsoft.com` and sign in with your administrator account.

1. Select **Reports** > **Usage**. If **Reports** doesn't appear, select **Show all** first.

1. Expand **Microsoft 365 Copilot**, select **Copilot**, and then select the **Readiness** tab.

1. Record the license summary and the number of rows where **Has Copilot license assigned** is **Yes** in **Copilot readiness details**.

   > [!NOTE]
   > If the user names in the table are concealed, the tenant hides user details in reports. Don't change that setting. Confirm Megan's license in the next step instead.

1. Select **Users** > **Active users** > **Megan Bowen** > **Licenses and apps**. Confirm that **Microsoft 365 E7** is selected, then close the pane without saving changes.

1. Select **Copilot** > **Overview** and review **Top actions**. Record any action that affects Megan or the first rollout wave, such as the Cowork billing action.

1. Go to `https://<yourtenant>.sharepoint.com/sites/AB650-Finance-Review`. Select **Settings** > **Site permissions** > **Advanced permissions settings**.

1. On the ribbon, select **Check Permissions**. Enter **Patti Fernandez**, select **Check Now**, and confirm **Read** through `AB650-Review-Finance`.

1. Repeat the check for **Diego Siciliani** and confirm **None**. If either result doesn't match, record it as a readiness blocker. Don't change the site in this lab.

**You have successfully reviewed Copilot readiness and the finance-site access boundary.**

### Task 2: Gate the rollout group

The rollout group is the visible gate for the first wave. Membership must match the readiness decision you made in Task 1.

1. On **SEA-DEV1**, in the Microsoft 365 admin center, select **Teams & groups** > **Active teams & groups**.

1. Select the **Security groups** tab, then select **Add a security group**.

1. In **Name**, enter `Copilot-Rollout-Wave1`, then select **Next**. Leave role assignment cleared, select **Next**, and then select **Create group**. Select **Close**. The group can take up to 5 minutes to appear in the list.

1. Select `Copilot-Rollout-Wave1`, select the **Members** tab, and then select **View all and manage members**.

1. Select **Add members**, search for and select **Megan Bowen**, and then select **Add**. Confirm that **Saved** appears.

1. Close the pane. Confirm that the **Members** tab lists Megan Bowen as the only member.

**You have successfully gated the rollout group to match the readiness decision.**

### Task 3: Verify one grounded response and pause overnight

A grounded answer with a citation is the proof you need. A missing citation is a readiness signal to record as pending, not a substitute result.

1. On **SEA-DEV2**, open an InPrivate window, go to `https://m365.cloud.microsoft/chat`, and sign in as `MeganB@<yourtenant>.onmicrosoft.com` with the user password provided by your lab hoster.

1. Confirm that the account shows **M365 Copilot (Premium)** in the lower-left corner and that **Work IQ** is on at the top of the chat.

1. Enter this prompt:

   ```text
   What is the Beacon migration risk described in my Beacon Rollout Plan.docx? Cite the source.
   ```

1. Review the answer. Confirm that it cites **Beacon Rollout Plan** and identifies the failing data-migration dry run on record mapping, owned by Isaiah Langer.

1. If Copilot doesn't cite the file or says it can't find the file, record the result as **pending indexing or access validation** with the date and time. Don't use a screenshot, sample answer, or practice data as a replacement.

1. Close the InPrivate window.

1. Record one unresolved risk before you widen the rollout, such as a pending citation, a permissions mismatch, or a user who isn't in the rollout group.

> [!IMPORTANT]
> **Overnight pause at the end of Day 2.** Stop here. Leave the rollout group, the finance review site, and every tenant object in place. Resume on Day 3 at Exercise 2 with the evidence you recorded here.

**You have successfully verified one grounded Copilot response or recorded the pending readiness result.**

---

## Exercise 2: Match Copilot controls to experiences

**Estimated time:** 20 minutes.

### Scenario

Copilot settings live in several admin surfaces. You route each requirement to the owning setting, inspect the web search policy state, review the seeded connector, make a no-change provider decision, and prove that connector discovery doesn't grant access to a denied user.

**Roles used:** **AI Administrator**, **Global Reader**, **Search Administrator** for search features, and **Security Administrator** or **Office Apps Administrator** for the web search policy.

### Task 1: Route requirements to owning settings

Decide which setting owns each requirement before you change anything.

1. On **SEA-DEV1**, in the Microsoft 365 admin center, select **Copilot** > **Settings**.

1. Select **View all** to display the available Copilot settings.

1. Record the owning setting, admin surface, and role for each requirement:

   | Requirement | Owning setting | Admin surface | Role |
   | --- | --- | --- | --- |
   | Let Copilot use public web grounding | **Web search for Microsoft Copilot** (cloud policy **Allow web search in Copilot**) | Microsoft 365 admin center shortcut to Microsoft 365 Apps admin center cloud policy | **Office Apps Administrator**, **Security Administrator**, or **Global Administrator** |
   | Improve internal search answers and acronyms | **Microsoft Copilot Search** and search answers | Microsoft 365 admin center and Microsoft Search settings | **Search Administrator** |
   | Make external content discoverable to Copilot | **Copilot connectors** | Microsoft 365 admin center > **Copilot** > **Connectors** | **AI Administrator** |
   | Allow another AI provider for eligible experiences | **AI providers operating as Microsoft subprocessors** or **AI providers operating as independent processors** | Microsoft 365 admin center > **Copilot** > **Settings** > **View all** | **AI Administrator** or **Global Administrator** |

1. For each row, write one sentence that states what the setting doesn't do. For example, a connector changes discovery, not the source authorization model.

1. Confirm that you have a four-row routing table and a boundary statement for each row.

**You have successfully routed each Copilot requirement to its owning setting.**

### Task 2: Inspect the web search state

The admin policy sets the ceiling. A user can turn web content off when it's allowed, but can't turn it on when the admin policy disables it.

1. On **SEA-DEV1**, in the Microsoft 365 admin center, select **Copilot** > **Settings** > **View all**.

1. Select **Web search for Microsoft Copilot**, then select the link to the Microsoft 365 Apps admin center and review the **Policy Management** page.

1. Review whether any policy configuration sets **Allow web search in Copilot**. If the page shows **You haven't created any policy configurations**, the policy isn't configured and web search is enabled by default.

1. Record which of these states applies to `Copilot-Rollout-Wave1` or to Megan Bowen:
   - **Enabled in Microsoft Copilot and Microsoft Copilot Chat**
   - **Disabled in Microsoft Copilot and Microsoft Copilot Chat**
   - **Disabled in Microsoft Copilot Work mode; Enabled in Microsoft Copilot Web mode and Microsoft Copilot Chat**

1. Record whether the current state also affects **Researcher** and **Cowork**. The most restrictive Work-mode state disables web search in those experiences.

1. On **SEA-DEV2**, open an InPrivate window, go to `https://m365.cloud.microsoft`, and sign in as `MeganB@<yourtenant>.onmicrosoft.com` with the user password provided by your lab hoster.

1. Select **Settings and more** (**...**) at the top right, select **Chat settings** > **Personalization**, and then expand **Advanced**.

1. Record the state of the **Web search** toggle. If the admin policy allows web search, the toggle is on and Megan can turn it off. If the policy disables web search, the toggle isn't available.

    > **Note:** Don't change the toggle. The user preference persists across sessions and would affect later tasks.

1. Close **Chat settings**, and then close the InPrivate window.

**You have successfully inspected the web search state and verified the user ceiling.**

### Task 3: Inspect the connector and verify discovery

The connector is already created and indexed. You confirm its state, prove the item permissions in the admin center, make the connection visible to Copilot, and then test discovery as Patti and Diego.

1. On **SEA-DEV1**, in the Microsoft 365 admin center, select **Copilot** > **Connectors** > **Your Connections**.

1. Select `AB650-Policy-Connector`. Confirm that the subtitle shows `ab650policy` and that **Items indexed** on the **Detail** tab shows **1**.

1. Select **Index browser**. In **Item Identifier**, enter `policy1`, then select **Submit**. Confirm that the item shows **Indexed**.

1. Select **Permissions**. Confirm that Patti Fernandez shows **Allowed** and Diego Siciliani shows **Denied**.

1. Select **Check user access**, search for `Diego`, then select **Diego Siciliani**. Confirm that the result is **Denied access**. Repeat for Patti Fernandez and confirm **Allowed access**.

1. Select **Detail**. If the pane shows **Data from this connection will not appear in Copilot Chat or Search Results**, scroll to **Copilot Visibility**, expand it, and turn the toggle **On**. Confirm that the warning no longer appears.

1. On **SEA-DEV2**, open an InPrivate window, go to `https://m365.cloud.microsoft`, and sign in as `PattiF@<yourtenant>.onmicrosoft.com` with the user password provided by your lab hoster.

1. Select **Search**, enter `Finance review policy`, and press **Enter**. Confirm that **Finance review policy** appears in the results and that **Sources** lists **AB650-Policy-Connector**.

    > **Note:** The **Contoso_Finance_Policy** results are SharePoint documents, not connector items.

1. Select **New chat**, enter `Find the Finance review policy.`, and press **Enter**. Confirm that Copilot quotes **Finance review access is limited to the assigned reviewer** and cites the **AB** connector source.

1. Close the InPrivate window. Open a new InPrivate window, go to `https://m365.cloud.microsoft`, and sign in as `DiegoS@<yourtenant>.onmicrosoft.com` with the user password provided by your lab hoster.

1. Repeat the search. Confirm that **Finance review policy** doesn't appear and that **Sources** doesn't list **AB650-Policy-Connector**.

1. Repeat the Copilot prompt. Confirm that Copilot reports it couldn't find a matching policy and doesn't cite the connector.

    > **Note:** Diego still sees **AB650-Policy-Connector** in **Chat settings** > **Sources**. The connection is visible, but item permissions trim what he can retrieve.

1. Close the InPrivate window.

**You have successfully inspected the connector and verified discovery for Patti and Diego.**

### Task 4: Review the AI provider decision without enabling a provider

A provider decision is a data-handling decision. Subprocessors run under Microsoft's terms. Independent processors run under the provider's own terms. You review both settings and record the decision, but you don't enable a provider.

1. On **SEA-DEV1**, in the Microsoft 365 admin center, select **Copilot** > **Settings** > **View all**, then search for `AI providers`.

1. Select **AI providers operating as Microsoft subprocessors**. Record the status of each listed provider, such as **Anthropic** and **OpenAI**, then close the pane.

1. Select **AI providers operating as independent processors**, then expand **Mistral AI**.

1. Review the legal terms and the access options **All users**, **No users**, and **Specific users and groups**. Record the selected option.

1. Don't select the terms check box, don't change the access option, and don't select **Save**. Close the pane.

1. Record the decision `Do not enable an independent processor in this lab`, then add the reason: the provider's terms, not your Microsoft customer agreements, apply to data the provider processes.

**You have successfully reviewed the AI provider decision without enabling a provider.**

### Task 5: Record the intended and denied outcomes

Finish the exercise by separating discovery, authorization, and web grounding in one short evidence record.

1. On **SEA-DEV1**, create a three-row evidence note with these rows: **Web search**, **Connector discovery**, and **Provider decision**.

1. For **Web search**, record the admin policy state and the user control result from Task 2.

1. For **Connector discovery**, record Patti's result and Diego's denial from Task 3.

1. For **Provider decision**, record **No change** and the reason from Task 4.

1. Confirm that your evidence note doesn't claim that a discovery setting changed source authorization or that a provider was enabled.

**You have successfully recorded the intended and denied Copilot control outcomes.**

---

## Exercise 3: Grant, use, and observe Cowork

**Estimated time:** 30 minutes.

### Scenario

Cowork access depends on spending-policy scope. You create two security groups, activate a capped spending policy scoped only to the finance group with alerts to Patti, verify that the excluded group isn't covered, run a decision-brief task as Megan and an exec-review readout as Patti, check each cost with `/cost`, and keep live usage separate from a sample historical-cost case.

**Roles used:** **Global Administrator** or **Billing Administrator** for Cost management, **Groups Administrator** for group creation, and **AI Administrator** for Copilot and Cowork settings.

### Task 1: Create the Cowork access groups

The two groups make the access boundary explicit. Megan and Patti are training test members of the funded group.

1. On **SEA-DEV1**, in the Microsoft 365 admin center, select **Teams & groups** > **Active teams & groups**.

1. Select the **Security groups** tab, then select **Add a security group**.

1. In **Name**, enter `cowork-pilot-finance`, then select **Next**, **Next**, and **Create group**.

1. Select **Add another Security group** and repeat the steps to create `cowork-pilot-legal`. Select **Close**.

1. Select `cowork-pilot-finance`, then select **Members** > **View all and manage members** > **Add members**. Select **Megan Bowen** and **Patti Fernandez**, then select **Add**.

1. Confirm that `cowork-pilot-finance` lists Megan and Patti and that `cowork-pilot-legal` has no members.

**You have successfully created the Cowork access groups.**

### Task 2: Configure funded Cowork access

A spending policy that selects Cowork grants access to everyone in its scope. The default policy covers all users, so you scope it to the finance group before you activate it.

1. On **SEA-DEV1**, in the Microsoft 365 admin center, select **Copilot** > **Cost management**, then select **Get started**.

1. In **Activate the default spending policy**, keep **Use Capacity Packs** selected and don't select pay-as-you-go billing. Select **Customize setup configuration**.

1. Select **Policy name**, enter `Cowork Finance Access`, then select **Save changes**.

1. Select **Budget scope**, select **Specific groups**, search for and select `cowork-pilot-finance`, then select **Save changes**.

1. Select **Agents and services**. Confirm that **Copilot Cowork** and **Work IQ API** are selected, turn off **Allow new Agents and services as they become available**, then select **Save changes**.

1. Select **Spending limit** and configure these settings:

   | Setting | Value |
   | --- | --- |
   | **Set the monthly spending limit for this policy** | **Limit monthly spending**: `15000` |
   | **Select monthly budget limits for users** | **Limit monthly spending per user**: `3000` |
   | **Define alerts** | On |
   | **Send email to the following users** | **Patti Fernandez** |
   | **Alert when monthly spending reaches** | `90` **% of limit** |
   | **Alert when a user's monthly spending reaches** | `2700` **Credits** |

1. Select **Save changes**, then select **Activate**.

1. Select **Manage configuration**. On the **Configuration** tab, under **Spending policies**, confirm that **Cowork Finance Access** is **Enabled** for `cowork-pilot-finance` and is the only policy. `cowork-pilot-legal` isn't covered.

   > **Note:** You can add more policies for other groups or all users. If a user is in more than one policy, the policy with the highest spending limit applies.

**You have successfully configured funded Cowork access for the finance group.**

### Task 3: Run a Cowork task as Megan and check its cost

Megan has a Beacon GA decision meeting. You ask Cowork to build her decision brief from her mail, Teams chat, and files, then use `/cost` to see what the task consumed against her 3,000-credit monthly limit. Run one task only.

1. On **SEA-DEV2**, open an InPrivate window, go to `https://m365.cloud.microsoft`, and sign in as `MeganB@<yourtenant>.onmicrosoft.com` with the user password provided by your lab hoster.

1. Select **Cowork** at the top of the left pane. Confirm that **Great news! You have access to Cowork** appears.

1. In **Start a task**, enter this task, then press **Enter**:

   ```text
   Prepare me for my Beacon GA decision meeting. Use the Beacon GA at risk email thread, Beacon GA Decision Options.docx, the Beacon War Room chat, Beacon Pilot Metrics.xlsx, and the Q3 budget reallocation approval email. Create a one-page Word decision brief that compares holding GA for two weeks with shipping on time, recommends one option, lists the open risks with owners, and ends with a three-sentence draft note to the Northwind Traders sponsor. Cite each source.
   ```

1. In the **Workspace** pane, watch **Steps** and **Tools**. Cowork works across **Outlook**, **Teams**, **SharePoint**, and **Work IQ**. The task takes about 5 minutes.

1. When the response is complete, confirm that Cowork reports it couldn't find **Beacon GA Decision Options.docx**. That file is in Isaiah Langer's OneDrive and isn't shared with Megan, so Cowork can't use it.

1. Under **Output**, select the Word brief and review the recommendation, risks and owners, sponsor note, and source citations. Close the document.

1. In **Message Cowork**, enter `/cost`, select the **/cost** skill, and press **Enter**. Record the credits used for this task, the percentage of the monthly limit remaining, and the credits used this month.

   > **Note:** `/cost` doesn't consume credits. It reports usage only after a task runs, and its totals are estimates, not billing records.

1. Select **View usage**. Review the **Cowork usage limit** bar for **Me** and **Group**, then close **Usage**.

1. Close the InPrivate window.

**You have successfully run a Cowork task as Megan and checked its credit cost.**

### Task 4: Run a Cowork task as Patti and compare usage

Patti presents customer rollout at next week's exec review. You ask Cowork to build her readout from her own calendar, mail, and OneDrive files, then compare her cost with Megan's. Both draw on the same `cowork-pilot-finance` policy.

1. On **SEA-DEV2**, open an InPrivate window, go to `https://m365.cloud.microsoft`, and sign in as `PattiF@<yourtenant>.onmicrosoft.com` with the user password provided by your lab hoster.

1. Select **Cowork** at the top of the left pane. Confirm that **Great news! You have access to Cowork** appears.

1. In **Start a task**, enter this task, then press **Enter**:

   ```text
   Prepare me for the "Exec Review - Portfolio & Customer Rollout" meeting on my calendar. Use my Customer Rollout Narrative and Portfolio Status Summary files and the "Board optics if Beacon GA slips two weeks" and "Exec review - I need the Beacon headline, not a status" email threads. Create a one-page Word readout with a Beacon headline I can stand behind, the status of each launch, the customer impact of a two-week hold, what I need from the exec review, and three questions I should expect. Cite each source.
   ```

1. In the **Workspace** pane, confirm that Cowork uses **Calendar**, **Outlook**, **SharePoint**, and **Work IQ**. The task takes about 3 minutes.

1. Under **Output**, select the Word readout. Confirm that it includes the Beacon headline, a status for each launch, the customer impact of the hold, what Patti needs from the exec review, and three expected questions. Close the document.

1. Select **Sources**. Confirm that Cowork cited both email threads, **Customer Rollout Narrative.docx**, and **Portfolio Status Summary.xlsx**.

1. In **Message Cowork**, enter `/cost`, select the **/cost** skill, and press **Enter**. Record the credits used for this task and compare them with Megan's task in Task 3.

1. Select **View usage**, then expand **Why am I seeing group usage?** Credits are shared with the group defined by the spending policy, so Megan's usage counts toward Patti's **Group** total. Close **Usage**.

1. Close the InPrivate window.

1. On **SEA-DEV1**, return to **Copilot** > **Cost management** > **Consumption**. For **Cowork Finance Access**, review **Active users** and **Credits used**, and compare them with Megan's and Patti's `/cost` results. If recent usage hasn't appeared, record **pending usage reporting** with the date and time.

**You have successfully run a Cowork task as Patti and compared usage across the funded group.**

### Task 5: Interpret the sample 45-credit Cowork consumption case

This task uses a sample case to practice diagnosis. It isn't a substitute for the live usage observations in Tasks 3 and 4.

> [!NOTE]
> **Practice case, not from this tenant:** The CSV describes a designed historical-cost case. Don't search for the sample users or events in a portal, and don't merge the 45-credit value with your live usage record.

1. On **SEA-DEV1**, open File Explorer and go to **AllFiles (F:)** > **Lab06**. Right-click `17-cowork-consumption-practice.csv`, and then select **Open with** > **Notepad**.

   > [!NOTE]
   > If you aren't using the hosted lab environment, download [`17-cowork-consumption-practice.csv`](../../../Allfiles/Lab06/17-cowork-consumption-practice.csv).

1. Find the row where a `cowork-pilot-legal` user consumed **45** credits.

1. Decide whether the row shows a user with no Cowork access or a user who had access through an overlapping Cowork-selecting policy.

1. Justify the diagnosis with the documented rule: access is granted by membership in any spending policy that selects **Cowork**, credit limits are evaluated asynchronously, and overlapping policies use the highest applicable per-user limit.

1. Name the smallest correct action: find the Cowork-selecting policy that included the legal group or all users, identify the policy owner, and remove the unintended overlap.

1. Confirm that your diagnosis stays separate from Megan's and Patti's live Cowork tasks and the usage or pending usage record from Task 4.

**You have successfully interpreted the sample Cowork consumption case.**

### If something doesn't work

| Symptom | Likely cause | Recovery |
| --- | --- | --- |
| The Copilot readiness report doesn't appear | The report can take up to 72 hours to become available, and report data can lag. | Record the readiness report as pending, then use the license page and site permission read-backs for the Day 2 gate. |
| Megan doesn't receive a citation to `Beacon Rollout Plan.docx` | The file might not be indexed for Megan yet, or Megan might not have the expected Copilot entitlement. | Record the result as pending indexing or access validation with the date and time. Don't substitute a sample answer. |
| Patti can't find **Finance review policy** | The connection's **Copilot Visibility** is off, or the change hasn't propagated yet. | Confirm that **Copilot Visibility** is **On** in the connector pane, wait a few minutes, and repeat the search. |
| Diego can find **Finance review policy** | The connector ACL or source item permission doesn't match the intended denied outcome. | Stop the persona test and record the mismatch. Don't claim that connector discovery preserved authorization. |
| Cowork doesn't appear for Megan | The spending policy doesn't select Cowork for `cowork-pilot-finance`, or another required Cowork surface hasn't propagated. | Reopen **Copilot** > **Cost management** > **Configuration**, confirm the policy scope and selected services, then wait for propagation before retrying. |
| A legal group member can open Cowork | Another policy that selects Cowork includes the user, the legal group, or all users. | Review every policy under **Spending policies** and remove the unintended overlap. |
| **Save changes** is unavailable in **Edit limits** | A limit is below the minimum, or **Define alerts** is on without a recipient or threshold. | Use at least `1000` for the policy and `2000` per user, and add a recipient and both thresholds. |
| `/cost` returns no result or isn't listed | `/cost` reports usage for a task that has already run. | Enter `/cost` in **Message Cowork** inside the completed task, not in a new empty task. |
| Cowork usage doesn't appear immediately | The **Consumption** tab refreshes on a delay. | Record the usage result as pending with the date and time. Don't record zero unless the reporting window has passed and the service still reports no usage. |

---

## Summary

In this lab, you gated a Microsoft 365 Copilot rollout with readiness, licensing, and finance-site permission evidence. You matched Copilot requirements to the settings that own them, inspected web search and provider settings without enabling a provider, and proved the connector's item permissions before testing discovery as Patti and Diego. You then created Cowork access groups, activated a capped spending policy scoped to the finance group with alerts to Patti, ran Cowork tasks as Megan and Patti and checked their cost with `/cost`, and kept live usage or pending usage separate from the sample 45-credit case.

You keep everything you created here. Lab 7 builds on this tenant to govern agents and operate AI services.
