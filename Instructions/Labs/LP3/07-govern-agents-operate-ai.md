---
lab:
  title: 'Lab 7 - Govern agents and operate AI services'
  description: 'Establish accountable agent identity and access, curate the Agent Registry and tools, monitor the agent estate, govern sharing and availability, and operate Microsoft 365 AI services.'
  duration: 85
  level: 300
  islab: true
  primarytopics:
    - Microsoft Entra Agent ID
    - Microsoft Agent 365 registry
    - Agent governance
    - Microsoft 365 Copilot reports
---

# Lab 7: Govern agents and operate AI services

AI services need the same discipline as any other enterprise workload: accountable identities, scoped availability, monitored activity, and operational reporting. In this final lab, you use the tenant's seeded Agent ID and Agent 365 state to make and verify governance decisions without inventing activity.

You continue in the same Microsoft 365 E7 tenant from Lab 6. **Nestor Wilke** handles AI administration, **Joni Sherman** handles security and compliance review, **Patti Fernandez** represents Finance users, and **Diego Siciliani** is the maker for staged Agent 365 requests.

This lab contains five exercises:

- **Exercise 1: Establish agent identity and access**
- **Exercise 2: Curate the Agent Registry and tools**
- **Exercise 3: Monitor the agent estate**
- **Exercise 4: Govern agent sharing and availability**
- **Exercise 5: Operate Microsoft 365 AI services**

This lab takes approximately **85 minutes** to complete.

## Learning objectives

By the end of this lab, you'll be able to:

- Locate a Copilot Studio agent's Microsoft Entra Agent ID and record its identity, blueprint, sponsor, owner, and manager relationships.
- Assign agent access through entitlement management and evaluate agent Conditional Access in report-only mode.
- Review Pending Agent 365 requests, publish one request to a limited audience, and keep Pending, Registry approval, Agent ID, and tool approval distinct.
- Build and share an agent as a maker, and find it in the Agent Registry.
- Monitor agent inventory and activity, and assign an owner to an agent that has none.
- Verify agent availability, installation, and sharing scope.
- Read live Copilot usage, agent usage, and Service Health signals, and route deeper analytics questions.

> [!IMPORTANT]
> Keep all Conditional Access changes in **Report-only** mode in this lab. Do not enforce a new agent-scoped policy, and do not create or connect Azure subscriptions, resource groups, billing accounts, or Azure resources.

## Before you start

You complete this lab signed in as a tenant administrator unless a task tells you to use a named sample person. The following conditions are provided by the lab environment, so you don't set them up:

| Provided by the environment | Detail |
| --- | --- |
| Tenant | The Lab 6 Microsoft 365 **E7** tenant with Microsoft 365 Copilot, Microsoft Agent 365, Microsoft Entra ID P2, Microsoft Purview, and Defender capabilities. |
| Agent identity | The Copilot Studio agent **AB650 Agent Governance Guide** and its Microsoft Entra agent identity, **AB650 Agent Governance Guide (Microsoft Copilot Studio)**. The agent has no owner in Agent 365. |
| Agent groups | **Agent Administrators** with Nestor Wilke, **Agent Security Admins** with Joni Sherman and Nestor Wilke, and business groups including **Finance Users**, **HR Users**, **IT Users**, and **Sales Users**. |
| Agent 365 requests | Six native agents staged by Diego Siciliani as **Pending** requests, including finance, HR, IT, sales, compliance, and legacy examples. |
| Governance seeds | The **Agent 365 Governance** access-package catalog with packages such as **Agent 365 - Finance Data Access**, and the report-only Conditional Access policies **CA: Block Unapproved Agent Identities** and **CA: Block High-Risk Agent Identities**. |
| Workstations | **SEA-DEV1** for administrator work and **SEA-DEV2** for sample-person sign-ins. |

You keep the work you create in this lab. This is the final lab, so there's no next lab dependency or end-of-lab cleanup.

---

## Exercise 1: Establish agent identity and access

**Estimated time:** 30 minutes.

### Scenario

A finance-facing agent must have a governed identity before you assign access or evaluate access-time controls. You locate the Copilot Studio agent, match it to its Microsoft Entra Agent ID, assign accountable relationships, run a sponsorship transfer, assign an access package, and scope a report-only Conditional Access policy.

**Roles used:** **AI Administrator** or **Agent Administrator** for discovery, **Identity Governance Administrator** for access packages, **Privileged Role Administrator** or **Conditional Access Administrator** for Conditional Access, and **Security Administrator** for review.

### Task 1: Locate the Agent ID and establish relationships

Microsoft Entra Agent ID separates owners, who manage the identity, from sponsors, who are accountable for it. A sponsor's manager is the fallback when sponsorship must move. For the model, see [Administrative relationships in Microsoft Entra Agent ID](https://learn.microsoft.com/entra/agent-id/identity-platform/agent-owners-sponsors-managers).

1. On **SEA-DEV1**, open the Microsoft Entra admin center at `https://entra.microsoft.com`.

1. Select **Entra ID** > **Agents** > **Agent identities**, and open **AB650 Agent Governance Guide (Microsoft Copilot Studio)**.

1. On the **Overview** page, note the **Object ID** and the **Agent blueprint**, **Microsoft Copilot Studio agent identity blueprint**. This identity is the agent's Microsoft Entra Agent ID, not an Agent 365 request.

1. Select **Owners and sponsors**. Select **Add** > **Add sponsor**, search for and select **Patti Fernandez**, and then choose **Select**.

1. Select **Add** > **Add owner**, search for and select **Nestor Wilke**, and then choose **Select**.

1. Confirm that the list shows **Patti Fernandez** as **Sponsor** and **Nestor Wilke** as **Full owner**.

1. Select **Entra ID** > **Users**, search for and open **Patti Fernandez**, and then select **Properties**.

1. Select the edit icon next to **Job Information**. Under **Manager**, select **Add manager**, select **Joni Sherman**, choose **Select**, and then select **Save**.

1. Confirm that Patti's **Manager** is **Joni Sherman** and her **Department** is **Executive Management**. You use both values in the next task.

**You have successfully located the agent identity and established its accountability relationships.**

### Task 2: Transfer sponsorship without orphaning the agent

When a sponsor changes jobs, a lifecycle workflow can transfer their agent sponsorships to their manager so the agent is never left without an accountable person. For the task details, see [Agent sponsor tasks in Lifecycle Workflows](https://learn.microsoft.com/entra/id-governance/agent-sponsor-tasks).

1. On **SEA-DEV1**, in the Microsoft Entra admin center, select **ID Governance** > **Lifecycle workflows** > **Create workflow**.

1. Select the **Agents** tab, and then select **Agent sponsor job profile change**.

1. On **Basics**, enter the name `AB650 sponsor transfer`. Leave **Trigger type** as **Attribute changes** and **Trigger attribute** as **department**, and then select **Next: Configure scope**.

1. In the rule, change the value from **Marketing** to `Executive Management`, and then select **Next: Review tasks**.

1. Select **Remove all access package assignments for user**, and then select **Disable**. Leave **Transfer agent sponsorships to manager** enabled.

1. Select **Next: Review + create**, leave **Enable schedule** cleared, and then select **Create**.

1. In the workflow list, select **AB650 sponsor transfer**, and then select **Run on demand**.

1. Select **Select users**, select **Patti Fernandez**, choose **Select**, and then select **Run workflow**.

1. Select **Entra ID** > **Agents** > **Agent identities** > **AB650 Agent Governance Guide (Microsoft Copilot Studio)** > **Owners and sponsors**. Select **Refresh** until **Joni Sherman** replaces **Patti Fernandez** as **Sponsor**.

    > [!NOTE]
    > The workflow can take a few minutes to run. You can continue with the next task and check back.

1. Confirm that the agent still has a sponsor and that **Nestor Wilke** is still **Full owner**.

**You have successfully transferred sponsorship without orphaning the agent.**

### Task 3: Assign the finance access package to the agent identity

Access packages give agent identities time-bound, auditable access. For the supported pattern, see [Access packages for agent identities](https://learn.microsoft.com/entra/agent-id/agent-access-packages).

1. On **SEA-DEV1**, in the Microsoft Entra admin center, select **ID Governance** > **Entitlement management** > **Access packages**.

1. Open **Agent 365 - Finance Data Access**. Note that its catalog is **Agent 365 Governance**.

1. Select **Assignments** > **New assignment**.

1. In **Select policy**, select **Agent 365 - Finance Data Access - Auto Policy**.

1. Select **Add identities**. Select **AB650 Agent Governance Guide (Microsoft Copilot Studio)**, which has the type **Agent identity**, and then choose **Select**.

1. In **Business justification**, enter `Lab 7 finance agent access`, and then select **Add**.

1. Select **Refresh** until the agent identity appears with the status **Delivered** and an end date.

**You have successfully assigned the finance access package to the agent identity.**

### Task 4: Scope a report-only Conditional Access policy to the agent

Conditional Access evaluates access-time conditions for agents separately from the entitlement you assigned in Task 3. For the policy model, see [Conditional Access for agents](https://learn.microsoft.com/entra/identity/conditional-access/agent-id).

1. On **SEA-DEV1**, in the Microsoft Entra admin center, select **Entra ID** > **Conditional Access** > **Policies**.

1. Open **CA: Block High-Risk Agent Identities**, and review its **State** and **Requirements for access**. Close the pane without changing the policy.

1. Select **New policy**, and then enter the name `AB650 report-only agent identity review`.

1. Under **Users or agents**, change **What does this policy apply to?** to **Agents**. Select **Select agent identities**, select **Select individual agents**, select **AB650 Agent Governance Guide**, and then choose **Select**.

1. Under **Target resources**, select **All resources (formerly 'All cloud apps')**.

1. Under **Conditions**, select **Agent risk (Preview)**, set **Configure** to **Yes**, select **High**, and then select **Done**.

1. Under **Grant**, select **Block access**, and then choose **Select**.

1. Leave **Enable policy** set to **Report-only**, and then select **Create**.

1. Confirm that **AB650 report-only agent identity review** appears in the list with the state **Report-only**.

**You have successfully scoped a report-only Conditional Access policy to the agent identity.**

---

## Exercise 2: Curate the Agent Registry and tools

**Estimated time:** 20 minutes.

### Scenario

Agent 365 curation controls which agents and tools become available in Microsoft 365. You publish one of Diego's Pending requests to a limited audience, leave another Pending for comparison, review the tool registry, and have Patti build and share her own agent.

**Roles used:** **AI Administrator** for Agent 365 review and Registry actions, and **Patti Fernandez** as an agent maker.

> [!NOTE]
> Keep four states separate: **Pending** means a request still needs review, **Registry approval** means an agent is available to a scoped audience, **Agent ID** is the Microsoft Entra identity from Exercise 1, and **tool approval** concerns a tool or action an agent can invoke.

### Task 1: Publish one Pending request and leave another Pending

Agent requests require admin review before they become available. For the documented flow, see [Manage agent requests in Microsoft 365 admin center](https://learn.microsoft.com/microsoft-365/admin/manage/agent-requests).

1. On **SEA-DEV1**, open the Microsoft 365 admin center at `https://admin.microsoft.com`.

1. Select **Agents** > **All agents**, and then select the **Requests** tab.

1. Confirm that the six requests from Diego Siciliani show the state **Pending review** and the request type **Publish**. The requests include **Finance Insights Agent**, **IT Helpdesk Agent**, and **Legacy Ops Agent (Ownerless)**.

1. Select **Finance Insights Agent**. Review the **Details**, **Data & tools**, **Security**, and **Permissions** tabs, and then select **Publish to store**.

1. On **Select users**, under **Select users or groups who can install the agent**, select **Specific users/groups**. Search for and select **Finance Users**.

1. Under **Select users or groups who will have the agent pre-installed (optional)**, select **None**, and then select **Next**.

1. On **Policy template**, keep **Default policy template for agents**, and then select **Next**.

1. On **Accept permissions**, confirm that the wizard shows **No required permissions**, and then select **Next**.

1. On **Review and finish**, confirm that the agent is published to **Finance Users** and deployed to **None**. Select **Publish**, and then select **Done**.

1. Confirm that five requests remain and that **IT Helpdesk Agent** is still **Pending review**. Don't publish or reject it.

1. Select the **Registry** tab, and search for `Finance Insights`. Confirm that two entries appear: the published copy with **Publisher type** set to **Your org**, and Diego's original copy.

1. Open the published copy, select the **Users** tab, and then select **Available to**. Confirm that **Specific users or groups** is set to **Finance Users**.

**You have successfully published one Pending request to a limited audience and kept another Pending.**

### Task 2: Review the tool registry

Tools such as MCP servers are approved separately from agents. A tool request starts only when a developer registers a tool, so viewing the registry doesn't create one. For the review flow, see [Manage plugins, skills, and MCP servers](https://learn.microsoft.com/microsoft-365/admin/manage/manage-plugins-skills-mcp-servers#review-and-approve-mcp-requests).

1. On **SEA-DEV1**, in the Microsoft 365 admin center, select **Agents** > **Tools**. On the **Registry** tab, record the counts for **All tools**, **MCP servers**, and **Plugins**.

1. Set the **Publisher** filter to **Microsoft Corporation**, and then select an MCP server such as **Work IQ MCP**.

1. Review the **Overview**, **Tools**, and **Policies** tabs. Note the **Block** action, but don't select it. Close the pane.

1. Select the **Requests (preview)** tab, and confirm that there are no pending tool requests.

**You have successfully reviewed the tool registry.**

### Task 3: Build and share an agent as a maker

Users can build their own agents in Agent Builder and share them with specific people. These agents appear in the Registry without an admin publishing them. For sharing options, see [Agent Builder in Microsoft 365 Copilot](https://learn.microsoft.com/microsoft-365/copilot/extensibility/agent-builder).

1. On **SEA-DEV2**, open an InPrivate window, go to `https://m365.cloud.microsoft`, and sign in as `PattiF@<yourtenant>.onmicrosoft.com` with the user password provided by your lab hoster.

1. In the left pane, select **Agents**, and then select **New agent**. Select **Skip** to open the **Configure** tab. If **What's new in Agent Builder** appears, select **Get started**.

1. Select the pencil icon next to **New Agent**, and enter the following values:
   - **Name:** `Finance Review Helper`
   - **Describe your agent:** `Answers questions about the finance review process.`
   - **Instructions:** `Answer questions about finance review steps. If you don't know, say so. Don't request or store personal data.`

1. Don't add knowledge sources. Select **Create** (the **+** button). Confirm that **Your agent was created successfully!** appears and that the agent is **private**.

1. Select **Share**. Confirm that **Org-wide sharing for chat access** is off.

1. In **Add a name, group, or email**, search for and select **Nestor Wilke**. Confirm that Nestor is set to **Can chat**, and then select **Add**.

1. Confirm that **Your agent was successfully shared** appears, select **Close**, and then close the InPrivate window.

1. On **SEA-DEV1**, in the Microsoft 365 admin center, select **Agents** > **All agents** > **Registry**, and search for `Finance Review Helper`.

1. Open the agent. On the **Details** tab, confirm that **Created by** shows **Patti Fernandez**.

1. Select the **Users** tab, and then select **Shared with**. Confirm that **Nestor Wilke** is listed under **Users**.

**You have successfully built an agent as a maker and confirmed its limited sharing.**

---

## Exercise 3: Monitor the agent estate

**Estimated time:** 15 minutes.

### Scenario

Inventory helps you find agents that need attention, but it doesn't grant access and activity can lag. You inventory the agent you published, then find and fix an agent that has no owner in Agent 365 instead of trusting a display name.

**Roles used:** **AI Administrator**.

### Task 1: Inventory the Registry entry you published

The Agent Registry provides a centralized view of agents available in the organization. For the Registry view, see [Manage agent registry in Microsoft 365 admin center](https://learn.microsoft.com/microsoft-365/admin/manage/agent-registry).

1. On **SEA-DEV1**, in the Microsoft 365 admin center, select **Agents** > **All agents** > **Registry**.

1. Search for `Finance Insights`, and open the copy whose **Publisher type** is **Your org**.

1. On the **Details** tab, record **Publisher**, **Publisher type**, **Owner**, **Created by**, **Last used**, and **Entra agent ID**.

1. Select the **Activity** tab. Record **Active users**, **Sessions**, **Exceptions**, and **Agent run-time** for the date range shown. If the cards show **No data**, record **Activity pending**. Don't record zero.

**You have successfully inventoried the Registry entry you published.**

### Task 2: Assign an owner to an agent without one

An agent without an owner has no one to maintain, update, or retire it. Agent 365 ownership is separate from the Microsoft Entra Agent ID owner you set in Exercise 1. For ownership actions, see [Governance and lifecycle actions for agents](https://learn.microsoft.com/microsoft-365/admin/manage/agent-actions).

1. On **SEA-DEV1**, in the Microsoft 365 admin center, select **Agents** > **All agents** > **Map**. Record the **Total agents**, **Agents at risk**, and **Agents without owners** cards. If a card shows **Failed to load** or a dash, record it as unavailable rather than zero.

1. Select the **Registry** tab, search for `Legacy Ops`, and open **Legacy Ops Agent (Ownerless)**. Confirm that **Owner** is populated. Despite its name, this agent has an owner. Close the pane.

1. Search for `AB650 Agent Governance`, and open **AB650 Agent Governance Guide**. Confirm that **Owner** and **Created by** show a dash, while **Entra agent ID** is populated.

   > [!NOTE]
   > In Exercise 1, you made Nestor the owner of this agent's Microsoft Entra identity. Agent 365 tracks a separate owner for the agent itself, and that owner is still empty.

1. Select **Assign new owner**. In **Assign Owner**, search for and select **Nestor Wilke**, and then select **Assign**.

1. Confirm that **Owner assigned** appears and that **Owner** and **Created by** now show **Nestor Wilke**.

1. Select **Agents** > **Settings** > **Agent Management Rules**. Review the **Reassign ownerless agents created with Agent Builder to manager** rule and its **Affected agents** count. Don't change the rule.

**You have successfully assigned an owner to an agent without one.**

---

## Exercise 4: Govern agent sharing and availability

**Estimated time:** 10 minutes.

### Scenario

Admin-published agents and user-shared agents reach people in different ways. You verify the published agent's availability and installation scope, compare it with Patti's shared agent, and review the tenant sharing setting.

**Roles used:** **AI Administrator**.

### Task 1: Verify agent availability and sharing scope

Agent availability and installation are separate settings. Review both before you reason about access.

1. On **SEA-DEV1**, in the Microsoft 365 admin center, select **Agents** > **All agents** > **Registry**.

1. Search for `Finance Insights`, and open the copy whose **Publisher type** is **Your org**.

1. Select the **Users** tab, and then select **Installed for**. Confirm that the agent isn't pre-installed for a broader audience.

1. Select **Available to**. If **Specific users or groups** isn't set to **Finance Users**, select it, add **Finance Users**, and then select **Save**. Close the pane.

1. Search for `Finance Review Helper`, open it, and select the **Users** tab. Confirm the message **Shared agents can't be automatically installed** and that only the agent's owner can share it. Close the pane.

1. Select **Agents** > **Settings** > **Sharing**. Record the value of **Choose who can share agents with anyone in the organization**. This setting controls whether makers like Patti can turn on **Org-wide sharing for chat access**. Close the pane without changing it.

1. Confirm that changing Registry availability doesn't change underlying file or application permissions.

**You have successfully verified agent availability and sharing scope.**

---

## Exercise 5: Operate Microsoft 365 AI services

**Estimated time:** 10 minutes.

### Scenario

AI operations combines live usage and health evidence. You read live Copilot and agent usage where reports are populated, record pending where they aren't, route deeper analytics questions, and read Service Health without inventing incidents.

**Roles used:** **Reports Reader** and **Service Support Administrator**, or equivalent administrator access.

### Task 1: Read Copilot and agent usage

Microsoft 365 admin center usage reports show Copilot adoption and agent usage. Reports can take up to 48 hours to populate. For the report path, see [Microsoft 365 Copilot usage report](https://learn.microsoft.com/microsoft-365/admin/activity-reports/microsoft-365-copilot-usage).

1. On **SEA-DEV1**, in the Microsoft 365 admin center, select **Reports** > **Usage**. If **Reports** isn't visible, select **Show all** first.

1. Under **Reports**, expand **Microsoft 365 Copilot**, and then select **Copilot**.

1. On the **Usage** tab, record the **Last updated** date, the period, **Enabled users**, **Active users**, and **Total prompts submitted**. If **Active users** is zero, record **Usage pending** with the last updated date.

1. Under **Microsoft 365 Copilot**, select **Agents**. Record **Total active users** and **Total active agents**. If **Usage details** shows **No data available**, record **Agent usage pending**.

1. Record where you would route deeper adoption or value questions: **Copilot Dashboard** in Viva Insights for organization-level adoption and impact, and these admin center reports for usage read-back.

**You have successfully read Copilot and agent usage or recorded it as pending.**

### Task 2: Read Service Health for AI services

Service Health shows Microsoft service incidents and advisories, not adoption or value.

1. On **SEA-DEV1**, in the Microsoft 365 admin center, select **Health** > **Service health**.

1. On the **Overview** tab, review **Active issues Microsoft is working on**. Record any issue whose **Affected service** is **Microsoft Copilot (Microsoft 365)**, and any advisory about usage report delays.

1. If none are listed, record **No live incident observed**.

**You have successfully read Service Health for AI services.**

### If something doesn't work

| Symptom | Likely cause | Recovery |
| --- | --- | --- |
| **AB650 Agent Governance Guide** isn't listed in **Agent identities** | The Copilot Studio agent's Agent ID hasn't synced or the preview view is unavailable. | Record the blocker, keep the Copilot Studio evidence, and don't substitute a Pending Agent 365 request. |
| The access-package subject picker doesn't show agent identities | The preview assignment path isn't enabled or the package uses unsupported resource roles. | Confirm that the package uses only agent-supported resource roles, and then search for the agent by the Agent ID object ID. |
| **Next** stays unavailable on **Select users** in the publish wizard | The optional pre-install choice isn't set. | Under **Select users or groups who will have the agent pre-installed (optional)**, select **None**. |
| Searching the Registry returns two entries with the same name | Publishing creates an organization copy alongside the maker's copy. | Use the entry whose **Publisher type** is **Your org**. |
| Agent Builder shows **We were unable to create your agent** | The **Instructions** box is empty or the draft didn't save. | Select **Close**, retype the instructions, wait for **Draft auto-saved**, and then select **Create** again. |
| **Finance Review Helper** isn't in the Registry yet | Registry sync can take a few minutes. | Select **Refresh**, clear filters, and search again. |
| **Agents at risk** shows **Failed to load** or a dash | Risk data isn't available for the tenant. | Record the card as unavailable and don't treat it as zero. |
| Activity or usage reports show **No data** | Reports can lag or require interactions before data appears. | Record **pending** with the report period, and check again later in the course. |
| Service Health shows no Copilot incident | There might be no live Microsoft service incident. | Record **No live incident observed**. |

---

## Summary

In this final lab, you located a Copilot Studio agent's Microsoft Entra Agent ID, established sponsor, owner, and manager relationships, assigned an access package to the agent identity, and scoped Conditional Access in report-only mode. You published one Agent 365 request to Finance Users, kept a second request Pending, reviewed the tool registry, and built and shared an agent as a maker. You assigned an owner to an agent that had none in Agent 365, verified agent availability and sharing scope, and operated AI services by reading live usage reports and Service Health.

This is the last lab in the course. Keep your final evidence notes for the course debrief and subject-matter review.
