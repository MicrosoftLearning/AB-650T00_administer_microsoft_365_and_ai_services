---
lab:
  title: 'Lab 2 - Enable collaboration and govern its content'
  description: 'Deliver a working collaboration space with a delegated shared mailbox, a team with a private channel, and a meeting Copilot policy, then create finance sites, remove an excessive access grant, and separate permission enforcement from Copilot discovery.'
  duration: 70
  level: 300
  islab: true
  primarytopics:
    - Exchange Online mailbox delegation
    - Microsoft Teams private channels
    - Teams meeting Copilot policy
    - SharePoint permissions and discovery
---

# Lab 2: Enable collaboration and govern its content

Collaboration and governance are the same job seen from two sides. When you stand up a shared mailbox, a team, or a site, you also decide who is included, who is excluded, and how content stays protected once people use it, including with Microsoft 365 Copilot. In this lab, you turn collaboration requests into precise access boundaries, then prove those boundaries hold.

You continue in the same Microsoft 365 E7 tenant from Lab 1, with the same sample people as your collaboration cast, including **Megan Bowen**, **Alex Wilber**, **Isaiah Langer**, **Lynne Robbins**, **Joni Sherman**, **Diego Siciliani**, **Nestor Wilke**, and **Patti Fernandez**.

This lab contains two exercises:

- **Exercise 1: Deliver a working collaboration space**
- **Exercise 2: Correct exposure and verify discovery**

This lab takes approximately **70 minutes** to complete.

## Learning objectives

By the end of this lab, you'll be able to:

- Create a shared mailbox, grant Full Access and Send As as separate permissions, and verify each one while the mailbox's sign-in stays blocked.
- Build a Microsoft Teams team with two business owners and a private channel, and show that an excluded team member can't see the channel.
- Configure a Teams meeting Copilot policy, predict the organizer's experience, and name what would disprove your prediction.
- Create SharePoint finance sites, introduce an excessive group grant, and remove it while preserving intended access.
- Verify allowed and denied access as test users, and apply Restricted Content Discovery to separate permission enforcement from Copilot discovery.

> [!IMPORTANT]
> Test access by signing in as the sample people on **SEA-DEV2**, not from your administrator session, which can mask a permission boundary. Work only in your assigned tenant, and don't send external invitations.

## Before you start

You complete this lab signed in as a tenant administrator. The following conditions are provided by the lab environment, so you don't set them up:

| Provided by the environment | Detail |
| --- | --- |
| Tenant | The Lab 1 Microsoft 365 **E7** tenant with an assigned `*.onmicrosoft.com` domain. |
| People | Nine ready-to-use sample people, already licensed for Microsoft 365 E7. You use them as owners, members, delegates, and test users. |
| Sample finance file | `Contoso_Finance_Policy.docx` on the tenant's root SharePoint site, in **Documents** > **A365Lab** > **documents** > **finance**. You upload copies of it and leave the original unchanged. |
| Test client | **SEA-DEV2**, where you sign in as sample people to test access. |
| Your access | An administrator account with the roles noted at the start of each exercise. |

You keep the work you create in this lab. Later exercises and later labs build on it, so there's no end-of-lab cleanup.

---

## Exercise 1: Deliver a working collaboration space

**Estimated time:** 35 minutes.

### Scenario

A support team needs a shared mailbox they can work from, a team with a restricted workstream for a subset of members, and a meeting policy that makes Copilot behavior predictable. You build all three and verify the participant's experience, not just the configuration.

**Roles used:** **Exchange Administrator**, **User Administrator**, **Teams Administrator**, and **SharePoint Administrator**.

### Task 1: Create a shared mailbox and delegate Full Access and Send As

Full Access lets a delegate open and manage a shared mailbox, but not send from it. Send As lets a delegate send as the mailbox, and recipients see only the mailbox in **From**. For more information, see [About shared mailboxes](https://learn.microsoft.com/microsoft-365/admin/email/about-shared-mailboxes).

1. On **SEA-DEV1**, open the **Exchange admin center** at `https://admin.cloud.microsoft/exchange#/`.

1. Select **Recipients** > **Mailboxes**, then select **Add a shared mailbox**.

1. Enter the following, then select **Create**:
   - **Display name:** `Training Support`
   - **Email address:** `training-support`, with your assigned `*.onmicrosoft.com` domain

1. When the mailbox is created, close the pane. In the **Mailboxes** list, select **Training Support**, then select the **Delegation** tab.

1. Next to **Read and manage (Full Access)**, select **Edit**. Select **+ Add members**, select **Diego Siciliani**, select **Save**, then select **Confirm**. Go back to the **Delegation** tab.

1. Next to **Send as**, select **Edit**. Select **+ Add members**, select **Diego Siciliani**, select **Save**, then select **Confirm**. Don't add **Send on behalf**, which shows the delegate's name in **From**. For more information, see [Manage permissions for recipients](https://learn.microsoft.com/exchange/recipients-in-exchange-online/manage-permissions-for-recipients).

1. Close the pane. In the **Microsoft 365 admin center** at `https://admin.cloud.microsoft`, select **Users** > **Active users**, then select **Training Support**. Confirm that the account shows **Sign-in blocked**. Delegates reach the mailbox through their own accounts.

1. Switch to **SEA-DEV2**. In Microsoft Edge, open an InPrivate window (**Ctrl+Shift+N**), go to `https://outlook.office.com`, and sign in as `DiegoS@<yourtenant>.onmicrosoft.com` with the user password provided by your lab hoster.

1. Select **New mail**. If a **Your privacy matters** dialog appears, select **Continue**.

1. Select the message body. Select the **Options** tab, select **Show fields**, then select **Show From**.

1. Select **From**, select **Other email address...**, type `training-support@<yourtenant>.onmicrosoft.com`, then select **Use this address**.

   > [!NOTE]
   > A newly created shared mailbox might not resolve by name yet, so type the full address.

1. In **To**, enter **Joni Sherman**. In **Subject**, enter `Send As test`, then select **Send**.

1. Select your profile picture, then select **Open another mailbox**. Enter `training-support@<yourtenant>.onmicrosoft.com`, then select **Open**. Confirm that the shared mailbox opens in a new tab. This confirms **Full Access**.

   > [!NOTE]
   > Outlook can take time to add a shared mailbox to the folder list automatically. If it doesn't appear there yet, that doesn't mean Full Access failed. Opening it with **Open another mailbox** confirms the permission.

1. Close the InPrivate window. Open a new InPrivate window, go to `https://outlook.office.com`, and sign in as `JoniS@<yourtenant>.onmicrosoft.com` with the user password provided by your lab hoster.

1. Open the **Send As test** message. Confirm that **From** shows **Training Support**, not Diego. This confirms **Send As**.

1. Close the InPrivate window.

**You have successfully created a shared mailbox and verified Full Access and Send As separately.**

### Task 2: Create a team and a private channel for a subset

A private channel gives a subset of a team its own workstream and its own SharePoint site. Team members who aren't added to the channel can't see it. For more information, see [Private channels in Microsoft Teams](https://learn.microsoft.com/microsoftteams/private-channels).

1. Switch to **SEA-DEV1**. Open **Microsoft Teams** at `https://teams.cloud.microsoft`. If a **Get to know Teams** dialog appears, select **Get Started**.

1. Select **Chat**. Next to the new chat icon, select the **New items** dropdown arrow, then select **New team**.

1. Enter the following, then select **Create**:
   - **Team name:** `DealRoom`
   - **Team type:** **Private** (default)
   - **First channel name:** `General`

1. In **Add members to DealRoom**, add **Megan Bowen**, **Alex Wilber**, **Isaiah Langer**, **Lynne Robbins**, and **Joni Sherman**.

1. Next to **Megan Bowen** and **Alex Wilber**, change **Member** to **Owner**, then select **Add**. Two business owners keep the team accountable if one is unavailable.

1. Next to **DealRoom**, select **More options (...)** > **Add channel**.

1. Enter the channel name `Negotiation`. In **Choose a channel type**, select **Private**, then select **Create**.

1. In **Add members to the Negotiation channel**, add **Isaiah Langer** and **Lynne Robbins**, then select **Add**. Don't add Joni Sherman.

1. Next to **Negotiation**, select **More options (...)** > **Manage channel**. On the **Members** tab, expand **Members and guests**. Next to **Isaiah Langer**, change **Channel role** to **Owner**.

1. In the **Microsoft 365 admin center**, select **Show all** > **SharePoint**. In the SharePoint admin center, select **Sites** > **Active sites**.

1. Search for `DealRoom`. In the **Channel sites** column for **DealRoom**, select **1 site**. Record the channel site name, **DealRoom-Negotiation**, and its type, **Private channel**.

   > [!NOTE]
   > A private channel uses a dedicated SharePoint site, separate from the parent team site. Files in the channel are stored there, not in the team site.

1. Switch to **SEA-DEV2**. In Microsoft Edge, open an InPrivate window, go to `https://teams.cloud.microsoft`, and sign in as `IsaiahL@<yourtenant>.onmicrosoft.com` with the user password provided by your lab hoster. If a **Get to know Teams** dialog appears, select **Get Started**.

1. Under **DealRoom**, confirm that the **Negotiation** channel appears with a lock icon. Open it and select the **Shared** tab to confirm that Isaiah can reach its files.

1. Close the InPrivate window, and repeat the previous two steps as `JoniS@<yourtenant>.onmicrosoft.com`. Confirm that Joni sees the **DealRoom** standard channels, but not **Negotiation**.

1. Close the InPrivate window.

**You have successfully created a team with a private channel and verified who can see it.**

### Task 3: Configure a meeting Copilot policy and predict behavior

What Copilot can do in a Teams meeting depends on the meeting policy assigned to the organizer, the Copilot option the organizer selects, and whether a transcript is saved. For more information, see [Manage Copilot and transcription in Teams meetings](https://learn.microsoft.com/microsoftteams/copilot-teams-transcription).

1. Switch to **SEA-DEV1**. Open the **Teams admin center** at `https://admin.teams.microsoft.com`.

1. Select **Meetings** > **Meeting policies**, then select **+ Add**.

1. Name the policy `DealRoom-Copilot`.

1. In the **Copilot and other AI** section, set **Copilot** to **On with saved transcript required**, then select **Save**.

1. In the policy list, select the **DealRoom-Copilot** row. Select **Manage users** > **Assign users**.

1. Search for **Megan Bowen**, select **Add**, select **Apply**, then select **Confirm**.

   > [!NOTE]
   > A policy assignment can take several hours to reach the user.

1. Use the following table to predict Megan's experience when she organizes a meeting:

   | Copilot value | Organizer default | Can the organizer change it? |
   | --- | --- | --- |
   | Off | Off | Yes |
   | On | Available only during the meeting | Yes |
   | On with saved transcript required | During and after the meeting, with a saved transcript | No, enforced |
   | On with transcript saved by default | During and after the meeting | Yes |

1. Record your prediction for Megan: Copilot is available during and after her meetings only with a saved transcript, and she can't change that option. Then record what would disprove it, such as Megan turning Copilot off in her meeting options, or a recap being available after a meeting where no transcript was saved.

**You have successfully configured a meeting Copilot policy and predicted the organizer's behavior.**

---

## Exercise 2: Correct exposure and verify discovery

**Estimated time:** 35 minutes.

### Scenario

Copilot grounds its answers only on content that a user's permissions already allow them to open, so an excessive permission grant is also a Copilot exposure. You create a governed finance site, then create a separate finance review site where an internal group has more access than it should. You prove the exposure, remove only the excessive grant, and choose a discovery control that limits broad discovery without changing who can open the content.

**Roles used:** **SharePoint Administrator**, as the owner of the sites you create.

### Task 1: Create a finance site and add the sample file

1. On **SEA-DEV1**, in Microsoft Edge, open a new tab and go to `https://<yourtenant>.sharepoint.com`.

1. Select **Documents** > **A365Lab** > **documents** > **finance**. Select **Contoso_Finance_Policy.docx**, then select **Download**. The file is saved to your **Downloads** folder.

1. In the SharePoint admin center, select **Sites** > **Active sites**, then select **+ Create**.

1. Select **Team site** > **Standard team** > **Use template**.

1. In **Site name**, enter `LAB2-Finance`. The group email address and site address fill in automatically.

1. In **Group owner**, select your administrator account, then select **Next**.

1. Leave **Privacy settings** set to **Private** and leave the default language, then select **Create site**. You can't change the site language later.

1. In **Add site owners and members**, select **Finish**. If a survey appears, close it.

1. Go to `https://<yourtenant>.sharepoint.com/sites/LAB2-Finance`. Select **+ Create or upload** > **Files upload**, select **Contoso_Finance_Policy.docx** from your **Downloads** folder, then select **Open**.

**You have successfully created a private finance site and added the sample file.**

For more information, see [Manage sites in the SharePoint admin center](https://learn.microsoft.com/sharepoint/manage-sites-in-new-admin-center).

### Task 2: Create a review site, prove the exposure, and correct it

You create a review site with two SharePoint groups. **AB650-Review-Finance** is the intended access for Patti. **AB650-Review-Internal** is an excessive grant that also gives Diego and Nestor access.

1. In the SharePoint admin center, select **Sites** > **Active sites** > **+ Create**.

1. Select **Communication site** > **Standard communication** > **Use template**.

1. In **Site name**, enter `AB650-Finance-Review`. In **Site owner**, select your administrator account, then select **Next**. Select **Create site**. The site appears in **Active sites** after a short delay.

1. Go to `https://<yourtenant>.sharepoint.com/sites/AB650-Finance-Review`. If a dialog appears, select **Maybe later**.

1. Select **+ Create or upload** > **Files upload**, select **Contoso_Finance_Policy.docx** from your **Downloads** folder, then select **Open**.

1. Select **Settings** (gear icon) > **Site permissions** > **Advanced permissions settings**.

1. Select **Create Group**. In **Name**, enter `AB650-Review-Finance`. Under **Give Group Permission to this Site**, select **Read**, then select **Create**.

1. On the group page, select **New**. Enter **Patti Fernandez**, then select **Share**.

1. Go back to **Advanced permissions settings**, and repeat the previous two steps to create `AB650-Review-Internal` with **Read** permission. Add **Diego Siciliani** and **Nestor Wilke**.

1. Switch to **SEA-DEV2**. In Microsoft Edge, open an InPrivate window, go to `https://<yourtenant>.sharepoint.com/sites/AB650-Finance-Review/Shared%20Documents`, and sign in as `PattiF@<yourtenant>.onmicrosoft.com` with the user password provided by your lab hoster. If a **Please sign in** dialog appears, select **Not now**.

1. Open **Contoso_Finance_Policy.docx**, and confirm that it opens.

1. Close the InPrivate window, and repeat the previous two steps as `DiegoS@<yourtenant>.onmicrosoft.com`. Confirm that Diego can also open the document. This is the exposure.

1. Close the InPrivate window. Switch to **SEA-DEV1**. On the **Advanced permissions settings** page, select **Check Permissions**. Enter **Diego Siciliani**, then select **Check Now**. Confirm that Diego has **Read** through the **AB650-Review-Internal** group.

1. Select the checkbox next to **AB650-Review-Internal**, select **Remove User Permissions**, then select **OK**.

1. Select **Check Permissions** again for **Diego Siciliani**. Confirm that Diego now has **None**.

1. Switch to **SEA-DEV2**, and repeat the document test as Patti. Confirm that the document still opens.

1. Repeat the document test as Diego. Confirm that Diego sees **You need access** with a **Request access** option. Don't request access.

1. Close the InPrivate window.

**You have successfully removed an excessive grant while preserving the intended access.**

### Task 3: Restrict content discovery for the review site

Use this requirement to choose a control:

> Authorized finance users must keep direct access, but the site should stop appearing in organization-wide search and broad Copilot discovery during the review.

**Restricted Content Discovery (RCD)** fits this requirement because it limits broad discovery without changing permissions. **Restricted Access Control (RAC)** doesn't fit, because it blocks access for anyone outside a specified group, which nobody asked for. **Restricted SharePoint Search** is a retiring tenant-wide control, so don't choose it for new work. For more information, see [Restricted Content Discovery](https://learn.microsoft.com/sharepoint/restricted-content-discovery) and [Restricted access control](https://learn.microsoft.com/sharepoint/restricted-access-control).

1. Switch to **SEA-DEV1**. In the SharePoint admin center, select **Sites** > **Active sites**, then select **AB650-Finance-Review**.

1. In the details pane, select the **Settings** tab.

1. Under **Restrict content discovery**, select **On**, then select **Save**. Confirm that a **Changes saved** message appears.

   > [!NOTE]
   > RCD requires SharePoint Advanced Management and Microsoft 365 Copilot. It doesn't revoke permissions or remove content from the index, and the change takes time to propagate. To block a group from opening content, correct the permission or use RAC instead.

1. Switch to **SEA-DEV2**. Open an InPrivate window, go to `https://m365.cloud.microsoft`, and sign in as `PattiF@<yourtenant>.onmicrosoft.com` with the user password provided by your lab hoster. If a welcome dialog appears, close it.

1. Select **New chat**, enter `Summarize Contoso_Finance_Policy from the AB650-Finance-Review site, and tell me which site each source is stored on.`, and press **Enter**. Record whether Copilot returns the document from **AB650-Finance-Review**. After RCD propagates, it doesn't. If it still does, record the time, because propagation can take a while on new sites.

1. In the same window, go to `https://<yourtenant>.sharepoint.com/sites/AB650-Finance-Review/Shared%20Documents`. If a **Please sign in** dialog appears, select **Not now**. Select **Contoso_Finance_Policy.docx** to open it in Word for the web.

1. Select **Copilot** at the lower right of the document. In **Message Copilot**, enter `Summarize this document in three bullets.`, and press **Enter**. Confirm that Copilot summarizes the policy and cites **Contoso_Finance_Policy**.

    > **Note:** If the **Summary** card at the top of the document shows an error, ignore it and use the Copilot pane.

1. Close the InPrivate window. Record two separate observations for the review site:
   - **Direct access:** Patti can still open the document and use Copilot on it.
   - **Discovery:** Organization-wide Copilot Chat doesn't surface the review site's copy.

**You have successfully restricted content discovery while preserving direct access.**

---

## Summary

In this lab, you created a shared mailbox, delegated Full Access and Send As as separate permissions, and verified each one while the mailbox's sign-in stayed blocked. You built a team with two business owners and a private channel, found the channel's dedicated SharePoint site, and showed that an excluded team member can't see the channel. You configured a meeting Copilot policy and predicted the organizer's experience. You then created a private finance site, created a review site with an excessive group grant, proved the exposure, removed only the excessive grant, and restricted content discovery while Patti kept direct access and could still use Copilot on the document.

You keep everything you created here. Lab 3 builds on this tenant to administer identities and delegated access.
