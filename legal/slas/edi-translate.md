---
title: EDI Translate Service Level Agreement
sidebarTitle: EDI Translate
slug: /legal/slas/edi-translate
tags:
  - legal
description: EDI Translate SLA
updatedDate: "2022-11-01"
---

# EDI Translate Service Level Agreement

This EDI Translate Service Level Agreement (“SLA”) is a policy governing the use of EDI Translate (“EDI Translate”) and applies separately to each account using EDI Translate. In the event of a conflict between the terms of this SLA and the terms of the [Stedi Customer Agreement](/docs/legal/customer-agreement) or other agreement with us governing your use of our Services (the “Agreement”), the terms and conditions of this SLA apply, but only to the extent of such conflict. Capitalized terms used herein but not defined herein shall have the meanings set forth in the Agreement.

#### Service Commitment

Stedi will use commercially reasonable efforts to make EDI Translate available with a Monthly Uptime Percentage, during any monthly billing cycle, of at least 99.9% (the “Service Commitment”). In the event EDI Translate does not meet the Service Commitment, you will be eligible to receive a Service Credit as described below.

#### Service Credits

Service Credits are calculated as a percentage of the total charges paid by you for EDI Translate for the monthly billing cycle in which the Monthly Uptime Percentage fell within the ranges set forth in the table below:

| Monthly Uptime Percentage                          | Service Credit Percentage |
| -------------------------------------------------- | ------------------------- |
| Less than 99.9% but greater than or equal to 95.0% | 10%                       |
| Less than 95.0% but greater than or equal to 90.0% | 25%                       |
| Less than 90.0%                                    | 100%                      |

We will apply any Service Credits only against future EDI Translate payments otherwise due from you. At our discretion, we may issue the Service Credits to the credit card you used to pay for the billing cycle in which the unavailability occurred. Service Credits will not entitle you to any refund or other payment from Stedi. Service Credits will be applicable and issued only if the credit amount for the applicable monthly billing cycle is greater than one dollar ($1 USD). Service Credits may not be transferred or applied to any other account. Unless otherwise provided in the Agreement, your sole and exclusive remedy for any unavailability or non-performance or other failure by us to provide EDI Translate is the receipt of Service Credits (if eligible) in accordance with the terms of this SLA.

#### Credit Request and Payment Procedures

To receive Service Credits, you will need to submit a claim by opening a case. To be eligible, the credit request must be received by us by the end of the second billing cycle after which the incident occurred and must include:

(i) the words “SLA Credit Request” in the subject line;

(ii) the billing cycle with respect to which you are claiming Service Credits, together with the Monthly Uptime Percentage for the billing cycle and the specific dates, times, and Availabilities for each 5-minute interval with less than 100% Availability throughout the billing cycle;

(iii) your Request logs that document the errors for your claimed outage (any confidential or sensitive information in these logs should be removed or replaced with asterisks).

If the Monthly Uptime Percentage of such credit request is confirmed by us and is less than the Service Commitment, then we will issue the Service Credits to you within one billing cycle following the month in which the credit request occurred. Your failure to provide the credit request and other information as required above will disqualify you from receiving Service Credits.

#### EDI Translate SLA Exclusions

The Service Commitment does not apply to any unavailability, suspension or termination of EDI Translate, or any other EDI Translate performance issues: (i) caused by factors outside of our reasonable control, including any force majeure event; (ii) that result from any voluntary actions or inactions from you or any third party; (iii) that result from you not following the best practices described in the EDI Translate documentation on the Stedi Site; (iv) that result from your equipment, software or other technology; or (v) arising from our suspension or termination of your right to use EDI Translate in accordance with the Agreement (collectively, the “EDI Translate SLA Exclusions”). If availability is impacted by factors other than those explicitly used in our Monthly Uptime Percentage calculation, then we may issue a Service Credit considering such factors at our discretion.

#### Definitions

- “Availability” is calculated for each 5-minute interval as the percentage of Requests processed by EDI Translate that do not fail with Errors. If you did not make any Requests in a given 5-minute interval, that interval is assumed to be 100% available.
- “Request” is a request to any EDI Translate API.
- An “Error” is any Request that returns a 500 or 503 error code.
- “Monthly Uptime Percentage” is calculated as the average of the Availability for all 5-minute intervals in a monthly billing cycle. Monthly Uptime Percentage measurements exclude downtime resulting directly or indirectly from any EDI Translate SLA Exclusion.
- A “Service Credit” is a dollar credit, calculated as set forth above, that we may credit back to an eligible account for payment.

#### Example

- There are 8,640 5-minute intervals in a 30 day month.
- During 864 of the 8,640 5-minute intervals, you send 1,000 requests to the EDI Translate API - 100 of which fail
- Each of these 864 5-minute intervals are considered to be 90% available (100/1,000 = 90%)
- For the rest of the month, you do not experience any issues with the service (and each interval of non-usage is considered to be 100% available)
- Assuming all the other 7,776 5-minute intervals are 100% available, the uptime for the month is 99% ((864 _ .9)+(7,776 _ 1))/ 8,640 = .99
- In this case, the EDI Translate API is in breach of the Monthly Uptime Percentage of 99.9%, and therefore you will be credited 10% of your EDI Translate's spend for that month's billing cycle, which will be applied to a future month's bill.
