---
title: Public Guides Service Level Agreement
sidebarTitle: Public Guides
slug: /legal/slas/public-guides
tags:
  - legal
description: Public Guides SLA
updatedDate: "2022-11-01"
---

This Public Guides Service Level Agreement (“SLA”) is a policy governing the use of the Stedi Guides “Public Guides” functionality, as defined in the [Service Terms](https://www.stedi.com/docs/legal/service-terms#54-guides), and applies separately to each account using Public Guides. In the event of a conflict between the terms of this SLA and the terms of the [Stedi Customer Agreement](https://www.stedi.com/docs/legal/customer-agreement) or other agreement with us governing your use of our Services (the “Agreement”), the terms and conditions of this SLA apply, but only to the extent of such conflict. Capitalized terms used herein but not defined herein shall have the meanings set forth in the Agreement.

### Service Commitment

Stedi will use commercially reasonable efforts to make Public Guides available with a Monthly Uptime Percentage, during any monthly billing cycle, of at least 99.9% (the “Service Commitment”). In the event Public Guides does not meet the Service Commitment, you will be eligible to receive a Service Credit as described below.

### Service Credits

Service Credits are calculated as a percentage of the total charges paid by you for Public Guides for the monthly billing cycle in which the Monthly Uptime Percentage fell within the ranges set forth in the table below:

| Monthly Uptime Percentage                          | Service Credit Percentage |
| -------------------------------------------------- | ------------------------- |
| Less than 99.9% but greater than or equal to 95.0% | 10%                       |
| Less than 95.0% but greater than or equal to 90.0% | 25%                       |
| Less than 90.0%                                    | 100%                      |

We will apply any Service Credits only against future Public Guides payments otherwise due from you. At our discretion, we may issue the Service Credits to the credit card you used to pay for the billing cycle in which the unavailability occurred. Service Credits will not entitle you to any refund or other payment from Stedi. Service Credits will be applicable and issued only if the credit amount for the applicable monthly billing cycle is greater than one dollar ($1 USD). Service Credits may not be transferred or applied to any other account. Unless otherwise provided in the Agreement, your sole and exclusive remedy for any unavailability or non-performance or other failure by us to provide Public Guides is the receipt of Service Credits (if eligible) in accordance with the terms of this SLA.

### Credit Request and Payment Procedures

To receive Service Credits, you will need to submit a claim by opening a case. To be eligible, the credit request must be received by us by the end of the second billing cycle after which the incident occurred and must include:

(i) the words “SLA Credit Request” in the subject line;

(ii) the billing cycle with respect to which you are claiming Service Credits, together with the Monthly Uptime Percentage for the billing cycle and the specific dates, times, and Availabilities for each 5-minute interval with less than 100% Availability throughout the billing cycle;

(iii) your Request logs that document the errors for your claimed outage (any confidential or sensitive information in these logs should be removed or replaced with asterisks).

If the Monthly Uptime Percentage of such a credit request is confirmed by us and is less than the Service Commitment, then we will issue the Service Credits to you within one billing cycle following the month in which the credit request occurred. Your failure to provide the credit request and other information as required above will disqualify you from receiving Service Credits.

### Public Guides SLA Exclusions

The Service Commitment does not apply to any unavailability, suspension or termination of Public Guides, or any other Public Guides performance issues: (i) caused by factors outside of our reasonable control, including any force majeure event; (ii) that result from any voluntary actions or inactions from you or any third party; (iii) that result from you not following any best practices that may be described in the Public Guides documentation on the Stedi Site; (iv) that result from your equipment, software or other technology; or (v) arising from our suspension or termination of your right to use Public Guides in accordance with the Agreement (collectively, the “Public Guides SLA Exclusions”). If availability is impacted by factors other than those explicitly used in our Monthly Uptime Percentage calculation, then we may issue a Service Credit considering such factors at our discretion.

### Definitions

- “Availability” is calculated for each 5-minute interval as the percentage of Requests to the Stedi URLs of the Public Guides that do not fail with Errors. If you or your Guides Page End Users did not make any Requests in a given 5-minute interval, that interval is assumed to be 100% available.
- “Request” is a request to any Public Guides URL published from your Stedi account, or any validation request from EDI Inspector on a Public Guides page published from your Stedi account.
- An “Error” is any Request that returns a 404, 500, or a 503 error code.
- “Monthly Uptime Percentage” is calculated as the average of the Availability for all 5-minute intervals in a monthly billing cycle. Monthly Uptime Percentage. measurements exclude downtime resulting directly or indirectly from any Public Guides SLA Exclusion.
- A “Service Credit” is a dollar credit, calculated as set forth above, that we may credit back to an eligible account for payment.
- A “Guides Page End User” is [defined in the Service Terms](https://www.stedi.com/docs/legal/service-terms#54-guides).

### Examples

- There are 8,640 5-minute intervals in a 30 day month.
- During 864 of the 8,640 5-minute intervals, you and your Guides Page End Users send 100 requests – 90 of which succeed and 10 of which fail – to the Public Guides URLs for the guides.
- Each of these 864 5-minute intervals are considered to be 90% available (10/100 = 90%)
- For the rest of the month, you do not experience any issues with the service (and each interval of non-usage is considered to be 100% available)
- Assuming all the other 7,776 5-minute intervals are 100% available, the uptime for the month is 99%: ((864 usage minutes _ 0.90 average availability) + (7,776 non-usage minutes _ 100% availability)) / 8,640 = 0.99 monthly availability.
- In this case, the Public Guides API is in breach of the Monthly Uptime Percentage of 99.9%, and therefore you will be credited 10% of your Public Guides spend for that month&#39;s billing cycle, which will be applied to a future month&#39;s bill.
