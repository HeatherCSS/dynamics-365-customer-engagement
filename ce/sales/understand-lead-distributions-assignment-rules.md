---
title: Understand record distribution in assignment rules
description: Learn how lead, opportunity, and insight round robin and load balancing distribution works in Dynamics 365 Sales assignment rules.
ms.date: 10/20/2023
ms.topic: conceptual
author: udaykirang
ms.author: udag
ms.reviewer: udag
ms.custom:
 - ai-gen-docs-bap
 - ai-gen-desc
 - ai-seo-date:10/20/2023
 - bap-template
---

# Understand record distribution in assignment rules

In Dynamics 365 Sales, assignment rules automatically distribute, or assign, new and updated leads, opportunities, and insights to your sales team. The automated process saves you time and effort and optimizes the workload across your sales team.

Assignment rules can distribute these records in either of two ways: round robin or load balancing.

- In [round robin distribution](#round-robin-distribution), the primary consideration is when sellers were last assigned a record.
- In [load balancing distribution](#load-balancing-distribution), the primary consideration is their capacity, or how many active records they're working on.

Depending on its definition, a rule that prioritizes load balancing might still use round robin criteria to assign records to sellers.

It's important to understand both the differences between them and how other criteria&mdash;like your sellers' workload and schedules&mdash;affect how records are assigned.

[!INCLUDE [sales-work-assignment](../includes/sales-work-assignment.md)]

## Round robin distribution

Round robin assignment distributes a new or updated record fairly among the sellers who meet the rule's criteria. It gives the record to the seller who's waited the longest for a new lead, opportunity, or insight, including records that were assigned manually or by an add-in.

Let's look at an example to understand round robin distribution.

A lead comes into the system at 1:33 PM. Based on the selection criteria that are defined in the assignment rule, three sellers can potentially work on the lead:

| Seller | Last assigned a lead |
|--------|----------------------|
| Miriam | 10:02 AM |
| Sanjay | 10:31 AM |
| Susana | 11:17 AM |

Miriam's last assignment is earlier than Sanjay's and Susana's. She's been waiting longest, so the lead is assigned to her and her last assignment time is updated.

Another lead comes into the system at 1:50 PM. This time, only Miriam and Sanjay have the required attributes to work on it:

| Seller | Last assigned a lead |
|--------|----------------------|
| Miriam | 1:33 PM |
| Sanjay | 10:31 AM |

But this time, Sanjay has been waiting longer than Miriam. Sanjay gets the lead.

## Load balancing distribution

Load balancing assignment finds the sellers who meet the rule's criteria and gives the record to the one who can take on more work. This method makes sure that all salespeople have a fair share of work and reduces uneven workloads.

Let's look at an example to understand load balancing distribution.

A lead comes into the system. Based on the selection criteria that are defined in the assignment rule, three sellers can potentially work on the lead:

| Seller | Available capacity |
|--------|--------------------|
| Miriam | 10 |
| Sanjay | 12 |
| Susana | 15 |

Susana can handle more work right now than Miriam or Sanjay. Susana gets the lead, and her available capacity is now 14.

Continuing with this example, let's assume that Miriam, Sanjay, and Susana can all potentially work on the next few incoming leads.

Two new leads come into the system. Susana still has the most available capacity, so the leads are assigned to her. Afterward, their available capacity looks like this:

| Seller | Available capacity |
|--------|--------------------|
| Miriam | 10 |
| Sanjay | 12 |
| Susana | 12 |

One more lead comes into the system. Sanjay and Susana are equally available. Susana got a lead more recently, so the new one is assigned to Sanjay based on round robin criteria. Now their available capacity looks like this:

| Seller | Available capacity |
|--------|--------------------|
| Miriam | 10 |
| Sanjay | 11 |
| Susana | 12 |

Miriam closes three leads, increasing her available capacity to 13. A new lead comes into the system. Miriam now has the most capacity, so it's assigned to her.

## Other distribution criteria

Assignment rules prioritize fairness in different ways depending on whether you select round robin or load balancing distribution. But fairness isn't the only consideration when records need to be assigned. They can also consider whether sellers are available to take on more work&mdash;that is, sellers' workload and work schedule.

### Consider seller workload

In your assignment rule definitions, you can select **Assign *record type* based on seller capacity**. This option distributes records only to sellers who have the capacity to work on them. If no one does, the records are left unassigned.

Let's look again at our sales team, a little later on the same day.

A new lead comes into the system. Based on the selection criteria that are defined in the assignment rule, four sellers can potentially work on it:

| Seller | Last assignment | Available capacity |
| ------ | --------------- | ------------------ |
| Sanjay | 2:37 PM | 0 |
| Susana | 2:57 PM | 4 |
| David | 3:02 PM | 1 |
| Miriam | 2:35 PM | &ndash;2 |

With round robin distribution alone, Miriam would get the lead because she's been waiting longer for an assignment than the others. However, you told the rule to **Assign leads based on seller capacity**, and Miriam's available capacity right now is &ndash;2. Sanjay has been waiting next longest, but his available capacity is 0. The lead goes to Susana.

If no seller who met the criteria had available capacity greater than zero, the lead would be left unassigned.

### Consider seller work schedule

In your assignment rule definitions, you can select **Assign if a seller is available within *N* hours**, where *N* is a whole number from 1 to 120. This option distributes records only to sellers whose work schedule shows they're available within the time you select. If no one is, the records are left unassigned. [Learn how to set sellers' availability](wa-manage-seller-availability.md).

The rule considers matched sellers first by assignment priority (load balancing or round robin) and then by who is available, from now up to the maximum time allowed.

As shown in the following diagram, matched sellers are divided into six buckets of 24 hours each based on their work schedules. If no sellers fall in the first bucket, round robin distribution supersedes load balancing even if load balancing distribution is prioritized. The rule continues to evaluate the buckets from top to bottom. When it encounters a nonempty bucket, the rule assigns the lead to the sellers in the bucket based on round robin criteria.

:::image type="content" source="./media/sa-ar-lead-distribution-buckets.png" alt-text="Diagram illustrating how seller availability is evaluated in 24-hour buckets.":::

Let's look at an example to understand how sellers' work schedules affect the assignment of records.

In your assignment rules, you prioritize round robin distribution and set a time limit of 48 hours.

A new lead comes into the system on a Friday evening from an inquiry form on your website. Several sellers meet the criteria of an assignment rule, but none of them are working now. Next, the rule considers matching sellers who are available within the next 24 hours. That day is Saturday. Ordinarily, Sanjay would be working, but his calendar says he's on vacation. No other sellers are available, so the rule looks at the next 24 hours. No one is working on Sunday either. The rule has reached the limit of 48 hours, so the lead is left unassigned. If the time limit had been 60 hours, the lead would have been assigned to the first seller who was available on Monday morning.

### Examples

Let's look at a few more examples in detail to understand how seller availability and workload affect round robin and load balancing distribution. We'll use the following notation for a seller's availability at the time of routing:

- 0D - Currently available
- 1D - Earliest available within 24 hours
- 2D - Earliest available from 24 to 48 hours
- 3D - Earliest available from 48 to 72 hours
- 4D - Earliest available from 72 to 96 hours
- 5D - Earliest available from 96 to 120 hours

#### Example 1: Round robin, consider seller availability only

The rule matched the following sellers:

| Seller | Availability | Last assigned a lead |
|--------|--------------|----------------------|
| Burt | 1D | 2:37 PM |
| Maya | 1D | 2:15 PM |
| Vivek | 0D | 3:02 PM |
| Maria | 2D | 3:10 PM |
| Sal | 0D | 2:29 PM |

Two sellers, Vivek and Sal, are available first. Vivek got a lead more recently than Sal, so the rule distributes the lead to Sal.

The next leads to come in are assigned to Vivek, then to Sal, and so on, until the availability of the matched sellers changes. The sellers' capacity isn't a consideration.

#### Example 2: Load balancing, consider seller availability only

Again, the rule matched the following sellers:

| Seller | Availability | Available capacity |
|--------|--------------|--------------------|
| Burt | 1D | 14 |
| Maya | 1D | 20 |
| Vivek | 0D | 5 |
| Maria | 2D | 10 |
| Sal | 0D | 2 |

Again, Vivek and Sal are available first. This time, Vivek has the higher capacity, so the rule distributes the lead to him.

The next leads to come in are assigned to Vivek until his capacity falls below Sal's. Following leads go to Sal until his capacity falls below Vivek's, and so on, until the availability of the matched sellers changes. In this scenario, the sellers' capacity isn't a consideration. Vivek's and Sal's capacity can fall below zero as long as they're available before the others.

#### Example 3: Load balancing, consider seller availability *and* capacity

Once again, the rule matched the following sellers:

| Seller | Availability | Available capacity |
|--------|--------------|--------------------|
| Burt | 1D | 14 |
| Maya | 1D | 20 |
| Vivek | 0D | &ndash;1 |
| Maria | 2D | 10 |
| Sal | 0D | 0 |

Because **Assign leads based on seller capacity** has been selected in the assignment rule definition, the rule ignores Vivek and Sal even though they're available first. That leaves Burt, Maya, and Maria:

| Seller | Availability | Available capacity |
|--------|--------------|--------------------|
| Burt | 1D | 14 |
| Maya | 1D | 20 |
| Maria | 2D | 10 |

Burt and Maya are available before Maria, so Maria is dropped from consideration:

| Seller | Availability | Available capacity |
|--------|--------------|--------------------|
| Burt | 1D | 14 |
| Maya | 1D | 20 |

The rule is configured to prioritize load balancing. However, load balancing distribution only works with sellers who are available at the time of routing. Because Burt and Maya aren't available now, the rule uses round robin criteria instead.

With capacity no longer a consideration, the rule considers who's been waiting for a lead longer:

| Seller | Last assigned a lead |
|--------|----------------------|
| Burt | 2:37 PM |
| Maya | 2:15 PM |

The rule assigns the lead to Maya. The next leads to come in are assigned to Burt, then to Maya, and so on, until the availability of the matched sellers changes.

### See also

- [Create and activate an assignment rule](create-and-activate-assignment-rule.md)
- [Set capacity for sellers](manage-sales-teams.md#set-capacity-for-sellers)
