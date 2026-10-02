---
lab:
  title: 'Lab 4 - Control sign-in and protect communications'
  description: 'Configure scoped authentication and recovery controls, evaluate Conditional Access decisions, set Defender for Office 365 protection scope, and interpret sample investigation evidence before creating a draft training campaign.'
  duration: 100
  level: 300
  islab: true
  primarytopics:
    - Authentication methods
    - Self-service password reset
    - Conditional Access
    - Defender for Office 365
    - Attack simulation training
---

# Lab 4: Control sign-in and protect communications

Identity and communication controls work best when you scope them narrowly, read the resulting evidence, and change only the setting that caused the observed result. In this lab, you configure sign-in recovery for nonadmin test users, evaluate a Conditional Access policy safely in report-only mode, set Microsoft Defender for Office 365 protection scope for Finance, and interpret sample investigation evidence before you create a draft follow-up training campaign.

You continue in the same Microsoft 365 E7 tenant from Lab 3. **Lynne Robbins** and **Alex Wilber** act as nonadmin engineering testers, **Patti Fernandez** acts as the Finance sign-in tester, and the seeded **Finance Users** group anchors the Conditional Access work. You create a mail-enabled **Finance Mail** group for the Defender for Office 365 work.

This lab contains four exercises:

- **Exercise 1: Configure method and recovery policy**
- **Exercise 2: Resolve an access decision**
- **Exercise 3: Set effective protection scope**
- **Exercise 4: Investigate and plan training**

This lab takes approximately **100 minutes** to complete.

## Learning objectives

By the end of this lab, you'll be able to:

- Scope a passkey authentication method and self-service password reset (SSPR) to a test group, then compare enabled, registered, and capable states.
- Configure tenant password protection settings and complete one supported SSPR test without using a personal phone.
- Create a human-user Conditional Access policy in report-only mode, then interpret sign-in log and What If results.
- Scope Defender for Office 365 anti-spam and anti-phishing policies to a mail-enabled Finance group, and confirm Safe Attachments and Safe Links coverage.
- Interpret sample threat and risk evidence without treating it as a live tenant event, then save a training campaign as a draft.

> [!IMPORTANT]
> Keep your administrator account and any emergency-access account out of Conditional Access policy assignments. Do not enforce the Conditional Access policy in this lab.

## Before you start

You complete this lab signed in as a tenant administrator. The following conditions are provided by the lab environment, so you don't set them up:

| Provided by the environment | Detail |
| --- | --- |
| Tenant | The Lab 3 Microsoft 365 **E7** tenant with an assigned `*.onmicrosoft.com` domain. |
| People | Nine ready-to-use licensed people. You use Lynne Robbins, Alex Wilber, Megan Bowen, Patti Fernandez, and Nestor Wilke in this lab. |
| Seeded groups | **Finance Users** is a security group without email that contains Patti Fernandez and Nestor Wilke. Lynne Robbins and Alex Wilber hold no administrator roles. |
| Test client | **SEA-DEV2**, where you sign in as sample people to test SSPR and Conditional Access evidence. |
| Evidence files | Sample evidence for sign-in risk and threat investigation is in the `assets` folder. The evidence is not from your tenant. |
| Known absent records | No risk detections, Explorer messages, alerts, incidents, or automated investigation and response recommendations are seeded. |
| Your access | An administrator account with the roles noted at the start of each exercise. |

You keep the work you create in this lab. Later exercises and later labs build on it, so there's no end-of-lab cleanup.

---

## Exercise 1: Configure method and recovery policy

**Estimated time:** 25 minutes.

### Scenario

The security team wants passkeys and self-service password reset available only to a small engineering test group. You create the group, scope passkeys and SSPR to it, register Lynne Robbins for SSPR, compare Lynne with Alex Wilber in the registration report, and run a real reset as Lynne.

**Roles used:** **Authentication Policy Administrator** and **Privileged Authentication Administrator** or equivalent administrator access to manage user authentication methods.

### Task 1: Create the Engineering group and scope passkeys

Each authentication method has its own targeting. Scoping passkeys to Engineering doesn't retarget other methods. For current passkey targeting guidance, see [How to enable passkeys (FIDO2) in Microsoft Entra ID](https://learn.microsoft.com/entra/identity/authentication/how-to-authentication-passkeys-fido2).

1. On **SEA-DEV1**, in Microsoft Edge, go to the Microsoft Entra admin center at `https://entra.microsoft.com` and sign in with your administrator account.

1. Select **Entra ID** > **Groups** > **All groups**, then select **New group**.

1. Enter the following values:
   - **Group type:** **Security**
   - **Group name:** `Engineering`
   - **Membership type:** **Assigned**

1. Under **Members**, select **No members selected**. Search for and select **Lynne Robbins** and **Alex Wilber**, then select **Select**.

1. Select **Create**.

1. Select **Entra ID** > **Authentication methods** > **Policies**.

1. Select **Passkey (FIDO2)**. On the **Enable and target** tab, confirm that **Enable** is **On**.

1. On the **Include** tab, select **Add target** > **Select targets**. Select **Engineering**, and then select **Select**.

1. In the **Engineering** row, open **Passkey profiles** and select **Default passkey profile**.

1. In the **All users** row, select the **Delete** icon. Only **Engineering** remains on the **Include** tab.

1. Select **Save**.

1. Confirm that the **Passkey (FIDO2)** row shows **1 group** as the target and **Yes** under **Enabled**.

**You have successfully created the Engineering group and scoped passkeys to it.**

### Task 2: Configure password protection and SSPR

Password protection improves password quality and slows guessing attacks. SSPR controls whether users can recover their own account. For password protection settings, see [Configure custom banned passwords for Microsoft Entra password protection](https://learn.microsoft.com/entra/identity/authentication/tutorial-configure-custom-password-protection).

1. In the Microsoft Entra admin center, select **Entra ID** > **Authentication methods** > **Password protection**.

1. Set **Enforce custom list** to **Yes**.

1. In **Custom banned password list**, add the following lab-only banned terms on separate lines:
   - `contoso`
   - `engineering`
   - `password`
   - `welcome`

1. Review the **Lockout threshold** and **Lockout duration in seconds** values, and leave them at the tenant defaults.

1. Select **Save** on the toolbar. Confirm that **Save** is unavailable after the change is saved.

1. Select **Entra ID** > **Password reset** > **Properties**.

1. Set **Self service password reset enabled** to **Selected**.

1. Under **Select group**, select the group link, select **Engineering**, and then choose **Select**.

1. Select **Save**. Confirm the **Password reset policy saved** notification.

1. Under **Manage**, select **Authentication methods**.

1. Set **Number of methods required to reset** to **1**.

1. Under **Methods available to users**, make sure **Security questions** is cleared, and then select **Save**.

   > [!NOTE]
   > Security questions are retiring for SSPR in March 2027, so this lab doesn't use them. Email, Microsoft Authenticator, and other methods are controlled by the authentication methods policy. In this tenant, **Email OTP** is already enabled for all users. Administrator accounts always use a stricter two-method reset policy that you can't change.

**You have successfully configured password protection and SSPR for the Engineering group.**

### Task 3: Register Lynne Robbins for SSPR

Lynne needs an SSPR-eligible method before the report can show her as capable. You add a lab-only alternate email that points to Megan's mailbox, so no personal phone is required. For SSPR registration behavior, see [Self-service password reset frequently asked questions](https://learn.microsoft.com/entra/identity/authentication/passwords-faq#password-reset-registration).

1. On **SEA-DEV1**, in the Microsoft Entra admin center, select **Entra ID** > **Users**, and then select **Lynne Robbins**.

1. Under **Manage**, select **Authentication methods**. Note that Lynne already has **Microsoft Authenticator** registered.

1. Select **Add authentication method** on the toolbar. In **Choose method**, select **Email**.

1. In **Email address**, enter `MeganB@<yourtenant>.onmicrosoft.com`, and then select **Add**. You read the verification code in Megan's mailbox in Task 4.

1. Confirm that **Email** appears under **Usable authentication methods** with Megan's address.

**You have successfully registered Lynne Robbins for SSPR.**

### Task 4: Compare registration states and run SSPR

The registration report can lag after a method or policy change. Use the live report, wait and refresh when needed, and don't substitute a CSV or sample fallback. For report fields, see [Authentication Methods Activity](https://learn.microsoft.com/entra/identity/authentication/howto-authentication-methods-activity#registration-details).

1. On **SEA-DEV1**, in the Microsoft Entra admin center, select **Entra ID** > **Authentication methods** > **User registration details**.

1. Find the **Lynne Robbins** row. Confirm that **SSPR Registered** shows **Registered**, **SSPR Enabled** shows **Enabled**, **SSPR Capable** shows **Capable**, and **Methods Registered** lists **Email** and **Microsoft Authenticator**.

1. Find the **Alex Wilber** row. Confirm that **SSPR Enabled** shows **Enabled** while **SSPR Registered** shows **Not Registered** and **SSPR Capable** shows **Not Capable**. Alex is in scope but has no methods.

1. If the report doesn't yet show Lynne's new state, check **Last Updated Time**, select **Refresh**, and wait a few minutes before checking again. Record the comparison only after the live report shows both rows.

1. Switch to **SEA-DEV2**. Open an InPrivate window, go to `https://aka.ms/sspr`, and enter `LynneR@<yourtenant>.onmicrosoft.com`.

1. If a character verification appears, complete it. Select **Next**.

1. Select **Email my alternate email**, and then select **Email**.

1. On **SEA-DEV1**, open an InPrivate window, go to `https://outlook.office.com`, and sign in as `MeganB@<yourtenant>.onmicrosoft.com`. If **Your privacy matters** appears, select **Continue**. Open the **Contoso account email verification code** message, copy the code, and close the InPrivate window.

1. Switch to **SEA-DEV2**, enter the code, and select **Next**.

1. On **Create a new password**, enter and confirm a new password for Lynne Robbins, and then select **Finish**. Confirm that **Your password has been reset** appears.

1. Go to `https://outlook.office.com`, and sign in as Lynne Robbins with the new password to confirm the reset worked.

1. Close the InPrivate window.

**You have successfully compared SSPR registration states and completed a real SSPR reset for Lynne Robbins.**

---

## Exercise 2: Resolve an access decision

**Estimated time:** 30 minutes.

### Scenario

The Finance team needs a Conditional Access policy that requires multifactor authentication and a compliant device for SharePoint. You create the policy in report-only mode, read how it would apply to Patti Fernandez, use What If for a legacy-client input, and interpret a sample risk case without generating live risk detections.

**Roles used:** **Conditional Access Administrator**, **Security Reader**, and **Reports Reader** or equivalent access to sign-in logs.

### Task 1: Create the Finance report-only policy

Report-only mode lets you evaluate a Conditional Access policy without enforcing the result. For safe evaluation, see [Analyze Conditional Access Policy Impact](https://learn.microsoft.com/entra/identity/conditional-access/concept-conditional-access-report-only).

1. On **SEA-DEV1**, in the Microsoft Entra admin center, select **Entra ID** > **Conditional Access** > **Policies**.

1. On the toolbar, select **New policy**.

1. In **Name**, enter `Finance - Require MFA and compliant device`.

1. Under **Users or agents**, select **0 users or agents selected**. On the **Include** tab, select **Select users and groups**, then select the **Users and groups** checkbox.

1. In **Select users and groups**, search for and select **Finance Users**, then select **Select**.

1. Select the **Exclude** tab, then select the **Users and groups** checkbox. Search for and select **MOD Administrator**, then select **Select**.

1. Under **Target resources**, select **No target resources selected**. On the **Include** tab, select **Select resources**.

1. Under **Select specific resources**, select **None**. Search for `Office 365`, select **Office 365 SharePoint Online**, then select **Select**.

    > **Note:** The wizard recommends targeting the **Office 365** app instead and warns that SharePoint Online also affects apps such as Microsoft Teams. Keep **Office 365 SharePoint Online** for this lab.

1. Under **Conditions**, select **0 conditions selected**, then select **Client apps** > **Not configured**.

1. Set **Configure** to **Yes**. Clear **Exchange ActiveSync clients** and **Other clients** so that only **Browser** and **Mobile apps and desktop clients** remain selected, then select **Done**.

1. Under **Grant**, select **0 controls selected**. Confirm that **Grant access** is selected, then select **Require multifactor authentication** and **Require device to be marked as compliant**.

1. Under **For multiple controls**, confirm that **Require all the selected controls** is selected, then select **Select**.

1. Confirm that **Enable policy** is set to **Report-only**.

1. Under the device-certificate warning, keep **Exclude device platforms macOS, iOS, Android, and Linux from this policy** selected, then select **Create**.

1. On the toolbar, select **Refresh**. Confirm that `Finance - Require MFA and compliant device` appears with **State** set to **Report-only**.

**You have successfully created the Finance Conditional Access policy in report-only mode.**

### Task 2: Read the report-only result for Patti

A sign-in with no visible prompt can still satisfy MFA through an existing claim. Read the sign-in record instead of judging only by the prompt.

1. Switch to **SEA-DEV2**. Open an InPrivate window, go to `https://<yourtenant>.sharepoint.com`, and sign in as `PattiF@<yourtenant>.onmicrosoft.com` with the user password provided by your lab hoster.

1. Confirm that SharePoint opens or shows the expected access result for Patti.

1. Close the InPrivate window.

1. Switch to **SEA-DEV1**. In the Microsoft Entra admin center, select **Entra ID** > **Monitoring & health** > **Sign-in logs**.

1. Filter the logs for **Patti Fernandez** and the application **Office 365 SharePoint Online**.

1. Open the matching sign-in event.

1. Select the **Conditional Access** or **Report-only** tab.

1. Locate `Finance - Require MFA and compliant device`, then record the report-only result, such as **Report-only: Success**, **Report-only: Failure**, **Report-only: User action required**, or **Not applied**.

1. Select **Authentication Details** and **Device Info**. Record whether MFA was already satisfied and whether the device was reported as compliant.

1. Confirm that the policy stayed in **Report-only** and did not block Patti's sign-in.

**You have successfully interpreted Patti's report-only Conditional Access result.**

### Task 3: Evaluate a legacy-client input with What If

What If estimates whether policies apply to the inputs you select. It doesn't run an authentication attempt.

1. On **SEA-DEV1**, in the Microsoft Entra admin center, select **Entra ID** > **Conditional Access** > **Policies**, then select **What if** on the toolbar.

1. Under **Identity**, confirm that **Users** is selected, then select **Edit user**. Search for and select **Patti Fernandez**, then select **Select**.

1. Under **Target resource**, confirm that **Cloud apps** is selected, then select **Select cloud app**. Search for `Office 365`, select **Office 365 SharePoint Online**, then select **Select**.

1. Under **Sign-in conditions**, set **Device platform** to **Windows**.

1. Set **Client app** to **Mobile apps and desktop clients - Other clients**. This option represents legacy authentication clients.

1. At the bottom of the form, select **What if**.

1. Under **Evaluation result**, select **Policies that will not apply**. Confirm that `Finance - Require MFA and compliant device` is listed with **Client app** as the reason.

1. Change **Client app** to **Browser**, then select **What if** again.

1. Select **Policies that will apply**. Confirm that `Finance - Require MFA and compliant device` is listed with **Require multifactor authentication AND Require compliant device** and **State** set to **Report-only**.

**You have successfully used What If to compare a legacy-client input with a browser input.**

### Task 4: Interpret the sample risk case

No risk detections are seeded in this tenant. Use the sample case in [`04-signin-evidence.md`](./assets/04-signin-evidence.md) to reason about policy interactions without searching for live risk events.

1. Open [`04-signin-evidence.md`](./assets/04-signin-evidence.md).

1. Read **Report-only policies in scope** and **Sign-in event B - compliant device, high sign-in risk**.

1. Write the applicable policy list for a Finance user signing in to SharePoint from a compliant device during a high-risk sign-in.

1. Write the predicted combined result.

1. Write the smallest correction needed to produce the intended result. Use the sample evidence to decide whether the correction is a user registration fix, a device compliance fix, or a policy-scope fix.

1. Write the rollback and safeguard: return the policy to report-only or its prior state, and confirm that emergency-access accounts stay excluded.

1. Confirm that your answer clearly labels the case as sample evidence, not a live risk detection.

**You have successfully interpreted the sample risk case and selected the smallest correction.**

---

## Exercise 3: Set effective protection scope

**Estimated time:** 25 minutes.

### Scenario

Finance receives messages from a supplier domain and needs the right inbound protection. You confirm which protection applies today, create a mail-enabled Finance group that Defender policies can target, create a Finance-scoped anti-spam policy, add impersonation protection for `woodgrovebank.com`, and confirm collaboration protection for shared files and Teams links.

**Roles used:** **Exchange Administrator** to create the mail-enabled group, and **Security Administrator** for Microsoft Defender for Office 365 policy settings.

### Task 1: Inspect existing protection for Finance

Preset security policies take precedence over custom policies for the same feature. For precedence details, see [Preset security policies in cloud organizations](https://learn.microsoft.com/defender-office-365/preset-security-policies).

1. On **SEA-DEV1**, in Microsoft Edge, go to the Microsoft Defender portal at `https://security.microsoft.com` and sign in with your administrator account.

1. Select **Email & collaboration** > **Policies & rules** > **Threat policies**.

1. Under **Templated policies**, select **Preset Security Policies**.

1. Confirm that **Standard protection is off** and **Strict protection is off**. **Built-in protection** applies to all users.

1. Select **Threat policies** in the breadcrumb, then under **Policies**, select **Anti-spam**.

1. Confirm that only the **(Default)** policies are listed, so no custom anti-spam policy targets Finance yet.

**You have successfully inspected the current protection scope for Finance.**

### Task 2: Create a mail-enabled Finance group

Defender for Office 365 policies can target only mail-enabled security groups or Microsoft 365 groups. The seeded **Finance Users** group is a security group without email, so you create **Finance Mail** with the same members.

1. In a new tab, go to the Microsoft 365 admin center at `https://admin.microsoft.com`.

1. Select **Teams & groups** > **Active teams & groups**, then select the **Security groups** tab.

1. Select **Add a mail-enabled security group**.

1. In **Name**, enter `Finance Mail`, then select **Next**.

1. Search for and select your administrator account as the owner, then select **Next**.

1. Add **Patti Fernandez** and **Nestor Wilke** as members, then select **Next**.

1. In the group email address, enter `financemail`. Leave the option to allow external senders cleared, then select **Next**.

1. Select **Create group**, then select **Close**.

**You have successfully created the mail-enabled Finance group.**

### Task 3: Create the Finance inbound anti-spam policy

The default anti-spam policy moves spam and high confidence spam to Junk Email and quarantines phishing and high confidence phishing. For Finance, you quarantine high confidence spam too. For policy steps, see [Configure anti-spam policies in Microsoft Defender for Office 365](https://learn.microsoft.com/defender-office-365/anti-spam-policies-configure).

1. In the Microsoft Defender portal, on **Anti-spam policies**, select **Create policy** > **Inbound**.

1. In **Name**, enter `Finance inbound anti-spam`. In **Description**, enter `Finance inbound spam policy for AB-650 Lab 4`, then select **Next**.

1. On **Users, groups, and domains**, in **Groups**, enter `Finance Mail`, select it from the suggestions, then select **Next**.

1. On **Bulk email threshold & spam properties**, keep the defaults, then select **Next**.

1. On **Actions**, set **High confidence spam** to **Quarantine message**. In **Select quarantine policy**, select **DefaultFullAccessPolicy**.

1. Keep the other actions at their defaults, then select **Next** until you reach **Review**.

1. Select **Create**, then select **Done**.

1. Confirm that `Finance inbound anti-spam` shows **Status** **On** and **Priority** **0**. A lower number has higher priority.

**You have successfully created the Finance inbound anti-spam policy.**

### Task 4: Add supplier impersonation protection

The harmless supplier-verification mail in the tenant is context only. It isn't a detection, and you don't search for it as a Defender event.

1. Select **Threat policies** in the breadcrumb, then select **Anti-phishing**.

1. Select **Create**.

1. In **Name**, enter `Finance supplier impersonation`, then select **Next**.

1. On **Users, groups, and domains**, in **Groups**, enter `Finance Mail`, select it from the suggestions, then select **Next**.

1. On **Phishing threshold & protection**, select **Enable users to protect**, then select **Manage 0 sender(s)**.

1. Select **Add user**. In **Email**, enter `ap-desk@woodgrovebank.com`, then select the suggestion below the box.

1. In **Name**, enter `Woodgrove Bank AP Desk`, then select **Add**.

1. Select **Add**, then select **Done**.

1. Select **Enable domains to protect**, then select **Include custom domains**.

1. Select **Manage 0 custom domain(s)** > **Add domains**. In **Domain**, enter `woodgrovebank.com`, select the suggestion, then select **Add domains** > **Done**.

1. Confirm that **Enable mailbox intelligence (Recommended)** is selected, then select **Enable Intelligence for impersonation protection (Recommended)**. Select **Next**.

1. On **Actions**, set **If a message is detected as user impersonation** to **Quarantine the message**, and in **Apply quarantine policy**, select **DefaultFullAccessPolicy**.

1. Set **If a message is detected as domain impersonation** to **Quarantine the message** with **DefaultFullAccessPolicy**, then select **Next**.

1. On **Review**, confirm that user impersonation is **On for 1 user(s)** and domain impersonation includes **1 custom domain(s)**.

1. Select **Submit**, then select **Done**.

1. Confirm that `Finance supplier impersonation` shows **Status** **On**.

**You have successfully added supplier impersonation protection for Finance.**

### Task 5: Confirm collaboration protection and run a negative check

Safe Attachments for SharePoint, OneDrive, and Teams is a global setting that doesn't depend on Safe Attachments policies. Safe Links protection for Teams is on in the **Built-in protection** preset. For details, see [Quickly configure Microsoft Teams protection](https://learn.microsoft.com/defender-office-365/mdo-support-teams-quick-configure).

1. Select **Threat policies** in the breadcrumb, then select **Safe Attachments**.

1. Select **Global settings**.

1. Confirm that **Turn on Defender for Office 365 for SharePoint, OneDrive, and Microsoft Teams** is **On**, then select **Cancel**.

1. Select **Threat policies** in the breadcrumb, then select **Safe Links**.

1. Confirm that **Built-in protection (Microsoft)** is the only policy, with **Status** **On**. Because no custom Safe Links policy takes precedence, Finance gets Teams link protection from Built-in protection.

1. Select **Threat policies** in the breadcrumb, select **Anti-spam**, then select `Finance inbound anti-spam`.

1. In the details pane, select **Turn off**, then select **Turn off** to confirm.

1. Confirm that the pane shows **Policy off | Priority 0**. A policy that's off doesn't apply to Finance, even at the highest priority.

1. Select **Turn on**, then select **Turn on** to confirm.

1. Confirm that the pane shows **Policy on | Priority 0**, then select **Close**.

**You have successfully confirmed collaboration protection and proved that an off policy doesn't apply.**

---

## Exercise 4: Investigate and plan training

**Estimated time:** 20 minutes.

### Scenario

The message, alert, incident, and automated investigation records in this exercise are sample evidence, not tenant records. You use the sample threat evidence to practice correlation and decision-making, then create a draft training campaign for lab-only recipients.

**Roles used:** **Security Reader** to inspect Defender areas and **Attack Simulation Administrator** or **Security Administrator** to create a draft training campaign.

### Task 1: Interpret the sample message and incident evidence

The fields in the sample case match the types of fields you would review in Defender. Do not search the portal for the fictional subject, Network Message ID, alert, or incident.

1. Open [`04-threat-evidence.md`](./assets/04-threat-evidence.md).

1. Read **Simulation training report - "Finance reporting practice"**. Record that the simulation report has no Network Message ID, alert, or incident.

1. Read **Real message - email entity timeline**. Record the recipient, subject, sender, Threat Explorer verdict, Network Message ID, and timeline sequence.

1. Read **Alert correlated to incident**. Confirm that the alert's Network Message ID matches the message and that the sample incident is a single-alert incident.

1. Write the defensible classification: **True positive - Phishing**.

1. Optional: In the Microsoft Defender portal, select **Email & collaboration** > **Explorer**. Open the **Sender address** filter list to see the message fields you can filter on. Don't search for the sample subject or IDs.

1. Optional: Select **Investigation & response** > **Incidents & alerts** > **Incidents** to see where incident fields live. Don't search for `INC-2026-0918`.

1. Confirm that your notes label the message, alert, and incident as sample evidence, not tenant evidence.

**You have successfully interpreted the sample message and incident evidence.**

### Task 2: Decide what the pending recommendation needs

Automated investigation and response (AIR) recommendations can group more than one action. The sample case asks you to decide whether the evidence supports approving the grouped action.

1. In [`04-threat-evidence.md`](./assets/04-threat-evidence.md), read **Pending AIR recommendation**.

1. Identify **Message A** and **Message B**.

1. Record that **Message A** has a confirmed phish verdict and matching timeline.

1. Record that **Message B** has no verdict or timeline of its own in these records.

1. Write the decision: don't approve the two-message soft-delete as one action until Message B has its own supporting evidence.

1. Write the evidence that would unblock the action: Message B's Network Message ID, verdict, timeline, and relationship to the incident.

1. Confirm that your answer separates the completed ZAP remediation for Message A from the pending decision about the grouped AIR recommendation.

**You have successfully decided what evidence the pending recommendation still needs.**

### Task 3: Create a draft follow-up training campaign

A training campaign assigns learning. It doesn't require a phishing simulation and doesn't create a Defender alert. For the campaign workflow, see [Training campaigns in Attack simulation training](https://learn.microsoft.com/defender-office-365/attack-simulation-training-training-campaigns#create-training-campaigns).

> [!IMPORTANT]
> Finish this task with **Save and close**. If you select **Submit**, the campaign launches and assigns training to Finance.

1. In the Microsoft Defender portal, select **Email & collaboration** > **Attack simulation training**. If a welcome dialog appears, select **Close**.

1. Select the **Settings** tab and note the **Training threshold** value. Leave it unchanged.

1. Select the **Training** tab, then select **Create new**.

1. In **Training name**, enter `Finance - phishing awareness follow-up`, then select **Next**.

1. On **Target users**, leave **Include only specific users and groups** selected, then select **Add users**.

1. In **Search for Users or Groups**, enter `Finance` and press **Enter**. Select **Finance Mail**, then select **Add 1 User(s)**.

1. Select **Next**. On the exclusion page, leave the exclusion check box cleared and select **Next**.

1. On **Select training modules**, leave **Training catalog** selected and select **Add trainings**.

1. Search for `phish`, select **Phishing — Six clues that should raise your suspicions**, select **Add**, then select **Next**.

1. On **Select end user notification**, leave **Microsoft default notification (recommended)** selected.

1. In the reminder row, set **Delivery preferences** to **Weekly**.

1. Optional: Select the preview (eye) icon for a notification to review the message, then select **Close**. Select **Next**.

1. On **Schedule**, leave **Launch this training campaign as soon as I'm done** selected and clear **Send training with an end date**.

1. Select **Save and close**.

1. On **Training Campaigns**, confirm that `Finance - phishing awareness follow-up` is listed and the **Draft** count is **1**.

**You have successfully created a draft follow-up training campaign.**

### If something doesn't work

| Symptom | Likely cause | Recovery |
| --- | --- | --- |
| Lynne doesn't appear as SSPR capable after registration | The registration report hasn't refreshed yet. | Select **Refresh**, wait a few minutes, and refresh again. Don't use a CSV substitute. |
| Lynne can't complete SSPR | The email method isn't registered, or Lynne isn't in the **Engineering** group. | On **SEA-DEV1**, verify Lynne's **Authentication methods** page and the **Engineering** members, then retry `https://aka.ms/sspr`. |
| Patti's SharePoint sign-in isn't in the sign-in logs yet | Sign-in log processing hasn't completed. | Wait a few minutes, then filter by **Patti Fernandez** and **Office 365 SharePoint Online** again. |
| The Conditional Access result is **Not applied** | The selected user, application, or client app doesn't match the policy scope. | Compare the sign-in event with the policy assignments and run **What If** with the same inputs. |
| **Finance Users** doesn't appear in a Defender policy or training campaign | Defender policies and training campaigns accept only mail-enabled security groups or Microsoft 365 groups. | Select **Finance Mail**. If it doesn't appear, complete Exercise 3, Task 2. |
| The Finance anti-spam policy seems ignored | A preset policy, higher-priority custom policy, or disabled state is controlling the result. | Inspect **Preset security policies**, custom policy priority, and the policy **Status**. |
| The sample message can't be found in Defender | The message is sample evidence, not a tenant event. | Use [`04-threat-evidence.md`](./assets/04-threat-evidence.md) and don't search the portal for the sample IDs. |
| The training wizard shows **Please select default language and delivery preference** | The reminder row's **Delivery preferences** is empty. | Set **Delivery preferences** to **Weekly**. |
| **Save and close** shows **Please select a valid launch datetime** | A schedule-later option or an end date is selected without dates. | Select **Launch this training campaign as soon as I'm done** and clear **Send training with an end date**. |
| The training campaign doesn't show **Draft** | The final wizard action was **Submit**, not **Save and close**. | On the **Training** tab, check the campaign status. If it's **In progress**, tell your instructor; don't create a second campaign. |

---

## Summary

In this lab, you created an Engineering group for nonadmin testers, scoped passkeys and SSPR to it, configured password protection, registered Lynne Robbins for SSPR, compared Lynne and Alex in the registration report, and completed a real SSPR reset. You created the Finance Conditional Access policy in report-only mode, read Patti's sign-in evidence, used What If with a legacy-client input, and interpreted a sample risk case without enforcing the policy. You inspected Defender for Office 365 preset scope, created a mail-enabled Finance group, created Finance anti-spam and anti-phishing policies, confirmed Safe Attachments and Safe Links collaboration protection, and proved that an off policy doesn't apply. Finally, you interpreted sample threat evidence and saved a Finance training campaign as a draft.

You keep everything you created here. Lab 5 builds on this tenant to protect and govern information.
