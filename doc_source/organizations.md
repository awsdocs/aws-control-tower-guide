# Manage Accounts Through AWS Organizations<a name="organizations"></a>

AWS Organizations is an account management service that lets you consolidate multiple AWS accounts into an organization that you create and centrally manage\. With Organizations, you can create member accounts and invite existing accounts to join your organization\. You can organize those accounts into groups and attach policy\-based controls\. For more information, see *[AWS Organizations User Guide](https://docs.aws.amazon.com/organizations/latest/userguide/)*\.

In AWS Control Tower, Organizations helps centrally manage billing; control access, compliance, and security; and share resources across your member AWS accounts\. Accounts are grouped into logical groups, called organizational units \(OUs\)\. For more information on Organizations, see *[AWS Organizations User Guide](https://docs.aws.amazon.com/organizations/latest/userguide/)*\.

AWS Control Tower uses the following OUs:
+ **Root** – The parent container for all accounts and all other OUs in your landing zone\.
+ **Core** – This OU contains the log archive account, the audit account, and the resources they own\.
+ **Custom OU** – This OU is created when you set up your landing zone\. It and other child OUs in your landing zone contain your member accounts\. These are the accounts that your end users access to perform work on AWS resources\.

**Note**  
You can add additional OUs in your landing zone through the AWS Control Tower console on the **Organizational units** page\.

## Considerations<a name="ou-considerations"></a>

OUs created through AWS Control Tower can have guardrails applied to them\. OUs created outside of AWS Control Tower cannot, and they are not displayed in AWS Control Tower\.