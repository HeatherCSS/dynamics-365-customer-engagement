---
title: How the automatic record creation rule works in an agent scenario
description: Understand how automatic record creation rules work in agent scenarios and learn how to customize them.
author: Soumyasd27
ms.author: sdas
ms.reviewer: shujoshi
ms.topic: conceptual
ms.collection:
ms.date: 04/30/2024
ms.custom: bap-template
---

# How the automatic record creation rule works in an agent scenario

With automatic record creation, you can create multiple cases from an email sent to multiple mailboxes. As an administrator, you can [enable this feature](arc-multiple-cases.md#enable-creation-of-multiple-cases-from-an-email-sent-to-multiple-mailboxes) to ensure that [multiple cases (one for each queue-enabled mailbox) get created when multiple mailboxes are added as recipients in the email](arc-multiple-cases.md#how-automatic-record-creation-rules-work-to-create-multiple-cases-from-an-email-sent-to-multiple-mailboxes).

This article lists the various agent scenarios and explains how automatic record creation rule works when an agent does a reply all or reply only to the customer using a case timeline. For example, when an email is sent to multiple mailboxes Q1, Q2, and Q3 and cases get created for each mailbox Case 1, Case 2, and Case 3, an agent can respond from the timeline of any of the cases (Case 1, Case 2, or Case 3).

Agents can also reply or reply all from Inbox or directly from the email by accessing it from the **My Activities** view. When the agent sends a reply, replies all or forwards an email from any of these entry points, the reply email is related to all cases and records that were related to the original email, unless removed from the related attribute manually.  

The following flowchart explains the agent scenario.

:::image type="content" source="../media/arc_agent-scenario.png" alt-text="Flowchart explaining agent scenario.":::

## Agent selects reply

When an agent selects reply from the timeline of one of the cases (Case 1, Case 2, or Case 3), the replied email gets correlated with all the records related to the previous email, such as cases, accounts, or any other entity records. The **To** field is set to only the customer in this scenario, but the related attribute shows all the related records: Case 1, Case 2, and Case 3.  

The replied email gets associated with all the cases related to the previous email and is displayed on the timeline of all the cases.  

If the previous email was manually linked to any other entity record like account, by adding an account in the related attribute, the reply email is also correlated with that account and shows on the timeline of the account. The agent can remove the records from the related attribute from the email form manually before sending the email if they want the replied email to be associated to only the case from which the reply email was sent.

This is the default behavior, irrespective of whether the agent replies to the email from a case timeline, Inbox, or by navigating to the email form from the **My Activities** view. Further communication between the customer and agent is displayed in all cases that were created from the previous email.  

> [!NOTE]
> If you want the reply email to be related only to the case from which it was sent so that agents working on other cases can’t view the email on their case timeline, you could opt for [timeline customization](#timeline-customization).

If you perform timeline customization, then:

- If the original email was set to other entity records manually (by setting the related attribute of the email) like accounts, leads, and so on, the reply email isn't associated with these and isn't displayed on the timeline for these records. The reply email is displayed on the timeline of the case from where it was sent.

- If Case 2 adds any mailboxes in response to the reply email, duplicate cases are created for those mailboxes.

## Agent selects reply all

When an agent selects reply all from Case 1's timeline and removes mailbox 3, the replied email gets associated with all the three cases: Case 1, Case 2, and Case 3. If the original email was related to any other entity records like account, the replied email is associated with that account. All these cases and related accounts see duplicate entries of the same email: one with the state as **Sent**, and the other with the state as **Received**.  

If you don’t want the reply email to be shown on Case 3 (that originated in mailbox 3) since mailbox 3 was removed, you need to manually remove the case that originated in mailbox 3 from the related attribute before sending the email.

## Agent forwards an email

When an agent forwards an email to another mailbox 4, from the Case 1 timeline, the forwarded email gets associated with the existing cases (Case 1, Case 2, and Case 3) and any other entity records (like account), if the original email is associated with the account. A new case is created for mailbox 4 to which the email was forwarded, and the email is displayed on the new case as well.

> [!NOTE]
> If you want the forwarded email to be associated with only Case 1 (since the Case 1 timeline was used) and not with other cases (Case 2 and Case 3), you’ll need to perform [timeline customization](#timeline-customization). The forwarded email will still be associated with the new case that’s created if the email was forwarded to mailbox 4.  

## Agent initiates a new email

Agents can also initiate a new email from the case timeline (by selecting the + button on the timeline). When an agent sends a new email to a customer from the case timeline, the **Related** attribute of the email is set to the case only if the administrator has enabled the "OrgDBOrg" setting "AddParentEntityToRelatedOnNewActivityCreate".  

For steps on how to enable the setting, go to [How to change default environment database settings](/power-platform/admin/environment-database-settings). This is applicable to only those scenarios where an agent adds a new email from the case timeline. It doesn’t apply to scenarios where an agent selects reply or reply all to an existing email from the case timeline.  

## Other scenarios

When a customer replies (ER1) to the original email E1 from the Sent items folder and to the same mailboxes (Q1, Q2, Q3), and the automatic record creation rule finds that the original email E1 is only set to Case 2 and Case 3 currently, as the agent removed Case 1 from the Related field of original email, the automatic record creation rule creates a duplicate case for Q1 since it didn’t find any case that was created in Q1 and associated to the original email, E1.

> [!NOTE]
> We recommend that agents shouldn’t remove the case from the **Related** field for the original email even if the case is irrelevant. Instead, they should mark it as a duplicate and resolve it.

## Timeline customization

An incoming email from a customer might get converted to multiple cases. When an agent replies to a customer from the timeline of a particular case or forwards an email from a particular case, you might want the response email to be associated with that case only. In such cases, you must update the ribbon buttons by performing the following steps:

1. Download and install [Ribbon Workbench](https://www.develop1.net/public/rwb/ribbonworkbench.aspx).

1. Create a web resource with the code to replace the ribbon button actions:  

    1. In [Power Apps](https://make.powerapps.com), go to **Advanced Settings** > **Customizations** > **Customize the System**.
    
    1. Select **Web resources** > **New**, and fill in the following information:
    
        - **Name**: fis_customers
        
        - **Type**: Script
        
        - **File**: (See attached file named Fis_Customers.js)

1. Create an unmanaged solution and add the **Activities** entity, with no dependencies.

1. Open **Ribbon Workbench** from **Solutions** and open the solution created in Step 3.

1. Select **Reply** in the subgrid ribbon.

1. Select **Customize Command**.

1. Select the web resource created in Step 2 as the library, and update the **Function Name** to start with **Activities.EmailCommandsFIS** instead of **Activities.EmailCommands**.

1. Repeat steps 5-7 for **Forward**.

1. Select **Publish** to save your changes.

## See also

[Create multiple cases from an email sent to multiple mailboxes](arc-multiple-cases.md#create-multiple-cases-from-an-email-sent-to-multiple-mailboxes)

[Enable creation of multiple cases from an email sent to multiple mailboxes](arc-multiple-cases.md#enable-creation-of-multiple-cases-from-an-email-sent-to-multiple-mailboxes)

[How the automatic record creation rule works in a customer scenario](arc-customer-scenario.md#how-the-automatic-record-creation-rule-works-in-a-customer-scenario)

[Sample - ARC Multi-Related - Remove Unreferenced Queue](sample-arc-multiple-cases.md#sample---arc-multi-related---remove-unreferenced-queue)
