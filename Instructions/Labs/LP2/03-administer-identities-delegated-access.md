---
lab:
  title: 'Lab 3 - Administer identities and delegated access'
  description: 'Bulk-create and verify accounts with least-privileged Microsoft Graph PowerShell, compare a mail contact with a guest, scope a helpdesk role with an administrative unit, and run a PIM activation through approval, deactivation, and audit correlation.'
  duration: 40
  level: 300
  islab: true
  primarytopics:
    - Microsoft Graph PowerShell
    - Mail contacts and B2B guests
    - Administrative units
    - Privileged Identity Management
---

# Lab 3: Administer identities and delegated access

Every administrator change to the directory has two parts: making the change, and proving it landed exactly where you intended. In this lab, you onboard a batch of new hires from a file and verify each account, then delegate a bounded administrative task and prove where the delegated administrator can and can't act.

You continue in the same Microsoft 365 E7 tenant from Lab 2. **Allan Deyoung** acts as a delegated helpdesk technician, **Joni Sherman** requests a privileged role, and **Lynne Robbins** and **Patti Fernandez** approve it.

This lab contains two exercises:

- **Exercise 1: Change the directory population**
- **Exercise 2: Delegate an administrative task**

This lab takes approximately **40 minutes** to complete.

## Learning objectives

By the end of this lab, you'll be able to:

- Bulk-create user accounts from a file with least-privileged Microsoft Graph PowerShell, then verify the results by object ID and group membership.
- Create a mail contact and a guest user, and explain when to use each.
- Scope an administrator's reach to a specific set of users with an administrative unit, and verify one allowed and one denied action.
- Run a time-bound Privileged Identity Management (PIM) role activation through eligibility, approval, and deactivation, and correlate its audit trail.

> [!IMPORTANT]
> Use only the sample data in this lab. Don't scope or activate administrative roles against your only administrator account or an emergency-access account.

## Before you start

You complete this lab signed in as a tenant administrator. The following conditions are provided by the lab environment, so you don't set them up:

| Provided by the environment | Detail |
| --- | --- |
| Tenant | The Lab 2 Microsoft 365 **E7** tenant with an assigned `*.onmicrosoft.com` domain. |
| People | Nine ready-to-use sample people. You use Allan Deyoung, Joni Sherman, Lynne Robbins, Patti Fernandez, and Megan Bowen in Exercise 2. |
| Microsoft Graph PowerShell | **Windows PowerShell** with the Microsoft Graph PowerShell SDK installed on **SEA-DEV1**. |
| Test client | **SEA-DEV2**, where you sign in as sample people to test delegated access and PIM. |
| Your access | An administrator account with the roles noted at the start of each exercise. |

You keep the work you create in this lab. Later exercises and later labs build on it, so there's no end-of-lab cleanup.

---

## Exercise 1: Change the directory population

**Estimated time:** 20 minutes.

### Scenario

A partner team hands you a file of five new hires to onboard. You validate the file, bulk-create the accounts with the least-privileged Microsoft Graph scope, add them to a security group, and verify each account by object ID. You then create a mail contact and a guest user so you can explain when to use each.

**Roles used:** **Global Administrator**.

### Task 1: Validate the new-hire file

The file holds two columns only, `DisplayName` and `MailNickname`, with no passwords and no domain. You supply your domain at runtime. Validating the file proves nothing about whether any account exists yet.

1. On **SEA-DEV1**, open **Windows PowerShell** from the taskbar.

1. Create the new-hire file:

   ```powershell
   New-Item -ItemType Directory -Path C:\Labfiles\03 -Force | Out-Null
   @"
   DisplayName,MailNickname
   Priya Shah,priyas
   Jamie Chen,jamiec
   Alex Morgan,alexm
   Dana Reyes,danar
   Sam Okafor,samo
   "@ | Set-Content -Path C:\Labfiles\03\new-hires.csv
   ```

1. Set your assigned tenant domain and import the file:

   ```powershell
   $tenantDomain = "<yourtenant>.onmicrosoft.com"
   if ($tenantDomain -like "*<*>*") { throw "Set `$tenantDomain to your assigned tenant domain first." }
   $requiredColumns = @("DisplayName", "MailNickname")
   $newHires = @(Import-Csv -Path "C:\Labfiles\03\new-hires.csv")
   if ($newHires.Count -eq 0) { throw "The CSV contains no rows." }
   ```

1. Confirm that the required columns are present, no value is empty, and the mail nicknames are unique:

   ```powershell
   $missing = @($requiredColumns | Where-Object { $_ -notin $newHires[0].PSObject.Properties.Name })
   if ($missing.Count -gt 0) { throw "Missing required columns: $($missing -join ', ')" }
   foreach ($row in $newHires) {
       foreach ($col in $requiredColumns) {
           if ([string]::IsNullOrWhiteSpace($row.$col)) { throw "A row has an empty '$col' value." }
       }
   }
   $dupes = @($newHires | Group-Object MailNickname | Where-Object Count -gt 1)
   if ($dupes.Count -gt 0) { throw "Duplicate MailNickname values: $($dupes.Name -join ', ')" }
   "Validation passed for $($newHires.Count) rows against domain $tenantDomain."
   ```

1. Confirm that the output reads **Validation passed for 5 rows** with your domain.

**You have successfully validated the new-hire file.**

### Task 2: Bulk-create the accounts

`User.Create` is the least-privileged delegated Microsoft Graph permission for creating accounts. Each result is linked to its row, so one failed row doesn't cost you the record of the rows that succeeded.

1. Connect with only the creation scope:

   ```powershell
   Connect-MgGraph -Scopes "User.Create"
   ```

1. In the **Pick an account** dialog, select your administrator account. If a **Permissions requested** prompt appears, select **Accept**.

1. Define a helper that generates a strong temporary password. The value is never printed, saved, or logged:

   ```powershell
   $allCreatedIds = [System.Collections.Generic.List[string]]::new()
   function New-TempPassword {
       $bytes = [byte[]]::new(24)
       $rng = [System.Security.Cryptography.RandomNumberGenerator]::Create()
       try { $rng.GetBytes($bytes) } finally { $rng.Dispose() }
       return (([Convert]::ToBase64String($bytes) -replace '[^A-Za-z0-9]', '') + 'Aa1!')
   }
   ```

1. Create the accounts as **disabled** accounts, because the new hires haven't started yet:

   ```powershell
   $results = [System.Collections.Generic.List[object]]::new()
   foreach ($row in $newHires) {
       $upn = "$($row.MailNickname)@$tenantDomain"
       try {
           $tempPassword = New-TempPassword
           $created = New-MgUser -AccountEnabled:$false `
               -DisplayName $row.DisplayName -MailNickname $row.MailNickname -UserPrincipalName $upn `
               -PasswordProfile @{ Password = $tempPassword; ForceChangePasswordNextSignIn = $true } -ErrorAction Stop
           $allCreatedIds.Add($created.Id)
           $results.Add([pscustomobject]@{ RequestedUpn = $upn; ReturnedId = $created.Id; Status = "Created"; Error = $null })
       }
       catch { $results.Add([pscustomobject]@{ RequestedUpn = $upn; ReturnedId = $null; Status = "Failed"; Error = $_.Exception.Message }) }
       finally { if (Get-Variable -Name tempPassword -ErrorAction SilentlyContinue) { Remove-Variable tempPassword } }
   }
   $results | Format-Table
   ```

1. Confirm that all five rows show **Created** with a returned object ID. **Created** means Graph accepted the request; you read each account back in the next task.

**You have successfully bulk-created the accounts.**

### Task 3: Add the accounts to a security group and verify

`User.Create` doesn't grant read or group-management access, so you reconnect with the scopes those operations need. If a lookup fails right after creation, wait a few seconds and retry.

1. Reconnect with the group and read scopes, then create the security group:

   ```powershell
   Disconnect-MgGraph
   Connect-MgGraph -Scopes "Group.ReadWrite.All","User.ReadBasic.All","GroupMember.ReadWrite.All"
   $groups = @(Get-MgGroup -Filter "displayName eq 'Regional Operations - New Hires'")
   if ($groups.Count -eq 0) {
       $group = New-MgGroup -DisplayName "Regional Operations - New Hires" -MailEnabled:$false `
           -MailNickname "regional-ops-new-hires" -SecurityEnabled:$true
   } else { $group = $groups[0] }
   ```

1. In the **Pick an account** dialog, select your administrator account. If a **Permissions requested** prompt appears, select **Accept**.

1. Add each created account, then verify the identities and the membership change:

   ```powershell
   $createdIds = @($results | Where-Object Status -eq "Created" | Select-Object -ExpandProperty ReturnedId)
   $beforeCount = (Get-MgGroupMember -GroupId $group.Id -All).Count
   foreach ($id in $createdIds) { New-MgGroupMember -GroupId $group.Id -DirectoryObjectId $id }

   $verified = foreach ($r in $results | Where-Object Status -eq "Created") {
       $u = Get-MgUser -UserId $r.ReturnedId -Property Id, UserPrincipalName
       [pscustomobject]@{ RequestedUpn = $r.RequestedUpn; IdMatches = ($u.Id -eq $r.ReturnedId); UpnMatches = ($u.UserPrincipalName -eq $r.RequestedUpn) }
   }
   $verified | Format-Table
   $afterCount = (Get-MgGroupMember -GroupId $group.Id -All).Count
   "Members before: $beforeCount  after: $afterCount  delta: $($afterCount - $beforeCount)  expected: $($createdIds.Count)"
   ```

1. Confirm that every row shows **IdMatches** and **UpnMatches** as **True**, and that the delta is **5**.

**You have successfully added the accounts to a security group and verified them.**

### Task 4: Run a safe negative test

You confirm that one bad row fails on its own without discarding the rest of the batch. The test uses separate variables, so your main results stay untouched.

1. Reconnect with the creation scope and run a two-row batch: one new nickname, and one that reuses an existing nickname:

   ```powershell
   Disconnect-MgGraph
   Connect-MgGraph -Scopes "User.Create"
   $negativeBatch = @(
       [pscustomobject]@{ DisplayName = "Negative Test New";  MailNickname = "negtest-$([guid]::NewGuid().ToString('N').Substring(0,6))" },
       [pscustomobject]@{ DisplayName = "Negative Test Dupe"; MailNickname = $newHires[0].MailNickname }
   )
   $negativeResults = [System.Collections.Generic.List[object]]::new()
   foreach ($row in $negativeBatch) {
       $upn = "$($row.MailNickname)@$tenantDomain"
       try {
           $tempPassword = New-TempPassword
           $created = New-MgUser -AccountEnabled:$false -DisplayName $row.DisplayName -MailNickname $row.MailNickname `
               -UserPrincipalName $upn -PasswordProfile @{ Password = $tempPassword; ForceChangePasswordNextSignIn = $true } -ErrorAction Stop
           $allCreatedIds.Add($created.Id)
           $negativeResults.Add([pscustomobject]@{ RequestedUpn = $upn; ReturnedId = $created.Id; Status = "Created"; Error = $null })
       }
       catch { $negativeResults.Add([pscustomobject]@{ RequestedUpn = $upn; ReturnedId = $null; Status = "Failed"; Error = $_.Exception.Message }) }
       finally { if (Get-Variable -Name tempPassword -ErrorAction SilentlyContinue) { Remove-Variable tempPassword } }
   }
   $negativeResults | Format-Table -Wrap
   ```

1. If the **Pick an account** dialog appears, select your administrator account.

1. Confirm that the new row shows **Created**, and the duplicate row shows **Failed** with the error **Another object with the same value for property userPrincipalName already exists**.

**You have successfully confirmed that one failed row doesn't affect the rest of the batch.**

### Task 5: Create a mail contact and a guest user

A **mail contact** routes mail to an external address and appears in the address book, but it can't sign in. A **guest** is a business-to-business (B2B) identity that can sign in and be granted access.

1. On **SEA-DEV1**, in Microsoft Edge, go to the **Microsoft 365 admin center** at `https://admin.cloud.microsoft`. Select **Users** > **Contacts**, then select **Add a contact**.

1. Enter the following, then select **Add**:
   - **Display name:** `Woodgrove Bank - AP Desk`
   - **Email:** `ap-desk@woodgrovebank.com`

1. Confirm that **Contact added** appears, then close the pane.

   > [!NOTE]
   > The contact can take up to 30 minutes to appear in the **Contacts** list.

1. Go to the **Microsoft Entra admin center** at `https://entra.microsoft.com`. Select **Entra ID** > **Users** > **All users**.

1. Select the **New user** arrow, then select **Invite external user**.

1. On the **Basics** tab, enter the following:
   - **Email:** `lab3-guest@fabrikam.com`
   - **Display name:** `Lab 3 Guest`

1. Clear **Send invite message**, because no one can redeem an invitation sent to a sample address. Select **Review + invite**, then select **Invite**.

1. In **All users**, search for `Lab 3 Guest`, then select it. On the **Overview** tab, confirm that **User type** is **Guest** and that the B2B invitation shows **Pending acceptance**.

1. (Optional) To see a guest redeem an invitation, repeat steps 5–7 with an external Microsoft account that you control and **Send invite message** selected. On **SEA-DEV2**, open an InPrivate window, select **Accept invitation** in the invitation email, sign in with that account, and select **Accept**. On **SEA-DEV1**, refresh the guest's **Overview** and confirm that the invitation shows **Accepted**. When you finish, delete that guest so no personal account stays in the lab tenant.

**You have successfully created a mail contact and a guest user.**

---

## Exercise 2: Delegate an administrative task

**Estimated time:** 20 minutes.

### Scenario

An administrator's reach has two dimensions: *where* they can act and *when* they can act. You scope a helpdesk role to the new hires with an administrative unit, then prove the scope with one allowed and one denied password reset. You then configure an eligible role in PIM, run one activation through approval and deactivation, and correlate its audit trail.

**Roles used:** **Privileged Role Administrator** (your administrator account), **Helpdesk Administrator** scoped to an administrative unit (Allan Deyoung), and **Message Center Reader** through PIM (Joni Sherman, approved by Lynne Robbins).

### Task 1: Create an administrative unit and scope a role to it

An administrative unit with assigned membership changes only when you change it, which suits one small, stable group. For details, see [Administrative units in Microsoft Entra ID](https://learn.microsoft.com/entra/identity/role-based-access-control/administrative-units).

1. On **SEA-DEV1**, in the Microsoft Entra admin center, select **Entra ID** > **Roles & admins** > **Admin units**, then select **Add**.

1. On the **Properties** tab, enter the name `Regional Operations`. Leave **Restricted management administrative unit** set to **No**.

1. Select the **Assign roles** tab, then select **Helpdesk Administrator**. Select **Allan Deyoung**, then select **Add**.

1. Select **Review + create**, then select **Create**.

1. In the **Admin units** list, select **Regional Operations**. On the **Users** page, select **Add member**.

1. Search for and select each new hire: **Priya Shah**, **Jamie Chen**, **Alex Morgan**, **Dana Reyes**, and **Sam Okafor**. Select **Select**.

1. Dana Reyes starts today. Select **Entra ID** > **Users** > **Dana Reyes** > **Edit properties** > **Settings**, select **Account enabled**, and then select **Save**.

   > [!NOTE]
   > A password can't be reset on a disabled account, even by an in-scope administrator.

**You have successfully created an administrative unit and scoped the Helpdesk Administrator role to it.**

### Task 2: Verify the allowed and denied outcomes

1. Switch to **SEA-DEV2**. In Microsoft Edge, open an InPrivate window, go to `https://entra.microsoft.com`, and sign in as `AllanD@<yourtenant>.onmicrosoft.com` with the user password provided by your lab hoster.

1. Select **Entra ID** > **Users**, then select **Dana Reyes**. Select **Reset password**, and then select **Reset password** again. Confirm that a temporary password appears. This is the allowed outcome.

1. Go back to **Users**, and select **Megan Bowen**, who isn't in the administrative unit. Select **Reset password**, and attempt the same reset. Confirm that the reset is denied because you don't have the required level of administrative privilege. This is the denied outcome.

   > [!NOTE]
   > Allan can still find and open Megan. An administrative unit scopes management permissions, not read access to the directory.

1. Close the InPrivate window.

**You have successfully verified the allowed and denied outcomes of a scoped role.**

### Task 3: Configure an eligible role in PIM

An **eligible** assignment requires the user to activate the role before they gain its permissions. Role settings apply to every assignment of the role, so you configure them first.

1. Switch to **SEA-DEV1**. In the Microsoft Entra admin center, select **ID Governance** > **Privileged Identity Management** > **Microsoft Entra roles**.

1. Under **Manage**, select **Settings**. Search for `Message Center`, select **Message Center Reader**, and then select **Edit**. Don't select **Message Center Privacy Reader**.

1. On the **Activation** tab, configure the following:
   - **Activation maximum duration (hours):** `4`
   - **On activation, require:** **Azure MFA**
   - **Require justification on activation:** Selected (the default)
   - **Require approval to activate:** Selected

1. Select **Select approver(s)**, select **Lynne Robbins** and **Patti Fernandez**, and then select **Select**.

1. Select **Update**. Confirm that **Role setting update succeeded** appears.

1. Under **Manage**, select **Roles**, then select **Message Center Reader**. Select **Add assignments**.

1. On the **Membership** tab, select **No member selected**. Search for and select **Joni Sherman**, select **Select**, and then select **Next**.

1. On the **Setting** tab, select **Eligible**, leave **Permanently eligible** selected, and then select **Assign**. Confirm that Joni Sherman was successfully assigned to the role.

**You have successfully configured an eligible role in PIM.**

### Task 4: Activate, approve, and correlate the audit trail

1. Switch to **SEA-DEV2**. Open an InPrivate window, go to `https://entra.microsoft.com`, and sign in as `JoniS@<yourtenant>.onmicrosoft.com` with the user password provided by your lab hoster.

1. Select **ID Governance** > **Privileged Identity Management** > **My roles**. On the **Eligible assignments** tab, find **Message Center Reader**, then select **Activate**.

1. In **Reason**, enter `Review Microsoft 365 message center posts for upcoming service changes.` Leave **Duration (hours)** at `4`, then select **Activate**. Confirm that **Your activation request is scheduled** appears. The request now waits for approval.

1. Close the InPrivate window. Open a new InPrivate window, go to `https://entra.microsoft.com`, and sign in as `LynneR@<yourtenant>.onmicrosoft.com`.

1. Select **ID Governance** > **Privileged Identity Management** > **Approve requests**. Under **Requests for role activations**, select Joni's **Message Center Reader** request, then select **Approve**.

1. In the **Approve Request** pane, review the requester, role, reason, and start and end times. In **Justification**, enter `Approved for message center review.` and then select **Confirm**.

1. Close the InPrivate window. Open a new InPrivate window, and sign in as Joni again.

1. Select **ID Governance** > **Privileged Identity Management** > **My roles** > **Active assignments**. Confirm that **Message Center Reader** shows the state **Activated** with an end time four hours after the approval.

1. Select **Deactivate**, and then select **Deactivate** again. Wait for all three stages to complete. Confirm that **Message Center Reader** no longer appears in **Active assignments**, but still appears in **Eligible assignments**.

1. Select **Microsoft Entra roles**, and then under **Activity**, select **My audit**. Confirm that you can see the request, Lynne's approval, the activation, and the deactivation.

1. Close the InPrivate window.

1. Switch to **SEA-DEV1**. In the Microsoft Entra admin center, select **Entra ID** > **Monitoring & health** > **Audit logs**.

1. Select the **Remove member from role completed (PIM deactivate)** event. On the **Activity** tab, scroll to the additional details and note the **RoleAssignmentRequestId**.

1. Close the event, and select the **Add member to role completed** event for the activation. Confirm that it shows the same **RoleAssignmentRequestId**, even though its **Correlation ID** is different.

   > [!NOTE]
   > PIM processes approval and activation asynchronously, so one activation cycle can produce several correlation IDs. Use **RoleAssignmentRequestId** to connect the events. For details, see [View audit history for Microsoft Entra roles in PIM](https://learn.microsoft.com/entra/id-governance/privileged-identity-management/pim-how-to-use-audit-log).

**You have successfully activated, approved, deactivated, and correlated a PIM role activation.**

### If something doesn't work

| Symptom | Likely cause | Recovery |
| --- | --- | --- |
| A Graph command reports **Failed** after a dropped connection | The account may still have been created. | In **Entra ID** > **Users**, look for the requested user principal name before you rerun the command. |
| Allan's reset of Dana is denied with a message that the account might be disabled | Dana's account is still disabled. | Complete the last step of Exercise 2, Task 1, then retry. |
| Adding the PIM assignment fails with **The role is not found** | The assignment was added before the role settings were saved, or from the wrong role. | Confirm that **Role setting update succeeded** appeared, then add the assignment again from **Roles** > **Message Center Reader**. |
| Joni's request doesn't appear for Lynne | The request is still processing. | Select **Refresh** in **Approve requests**. |
| Activation completes without a multifactor authentication prompt | Joni signed in with strong authentication in the same session. | No action. The requirement still applied. |

---

## Summary

In this lab, you validated a new-hire file, bulk-created the accounts with a least-privileged Microsoft Graph scope, and verified each account by object ID and group membership. You confirmed that one failed row doesn't affect the rest of a batch. You created a mail contact and a guest user and compared the two. You then created an administrative unit, scoped a helpdesk role to it, and verified one allowed and one denied password reset. Finally, you configured an eligible role in PIM, ran one activation through approval and deactivation, and correlated its audit trail by **RoleAssignmentRequestId**.

You keep everything you created here. Lab 4 builds on this tenant to control sign-in and protect communications.
