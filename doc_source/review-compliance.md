# How can administrators review compliance?<a name="review-compliance"></a>

Compliance with detective controls is determined according to data retrieved from the AWS Config aggregator in the AWS Control Tower Audit account\. You can review compliance status in the AWS Control Tower console, by subscribing to SNS topics that send email messages to the Audit account, or both\.

**Detective control status**

To view the compliance status of detective controls in the AWS Control Tower console, select **Controls** in the left navigation, choose the control name from the controls table, and then scroll to the **Accounts** section on that control details page\. Accounts may show a control compliance status of **Unknown** if any detective controls are misconfigured\. For example, status **Unknown** often can appear due to account drift, such as **Moved account** drift\. The **Unknown** status also can appear as a result of SCP drift\.

**Preventive control status**

The compliance status of preventive controls on an OU may be viewed on the **OU detail** page, by scrolling to the **Enabled controls** section\. If any preventive controls are misconfigured for an OU, the **State** field for that OU may show the state of **Registration failed**, in the **Details** section near the top of the page\. Preventive control misconfiguration is caused most often by SCP drift, which can occur if the control's SCP is modified or detached from the OU by means of the AWS Organizations console\.

The control compliance status also can be viewed on other pages:
+ the AWS Control Tower **Dashboard** page, by scrolling to the **Controls** section near the bottom of the page
+ the **Control details** page, which you can view by selecting the name of a control on another page

**Nested OUs and compliance**

When an OU shows a status of **Noncompliant**, it means that one of the accounts directly under the OU contains noncompliant resources\. The compliance status of an OU is not influenced by the compliance status of nested OUs under the OU, or the compliance status of any accounts that are not directly under the OU\.

**Other resources**

If an account has any non\-compliant resources, that account may be shown with **Noncompliant** status on the **OU** or **Account** page in the AWS Control Tower console\. Details about the specific resources that have caused the non\-compliant status are shown on the **Account details** page\.

If an account shows **Compliant** status, that means it has no resources that are non\-compliant; therefore, no resource details are shown on the **Account details** page, only an empty table\.

**Receive compliance status updates**

To receive updates about compliance, you can subscribe to SNS topics that send notifications when resource compliance status changes\. See [Compliance notifications by SNS in the audit account](receive-notifications.md), later in this chapter\.

For more information on how AWS Control Tower collects information about resources, see the [AWS Config Aggregator Documentation](https://docs.aws.amazon.com/config/latest/developerguide/aggregate-data.html)\.

**Drift changes the compliance status for OU and account resources**  
Drifted resources may be shown with status **Unknown** in the **Compliance** status field of the AWS Control Tower console\. The **Unknown** state indicates that AWS Control Tower cannot determine the compliance status of the resource, because drift is present\. Drift is not necessarily a detective control compliance violation\. For more information about drift, see [Detect and resolve drift in AWS Control Tower](drift.md)\.