# Step 1: Create your shared account email addresses<a name="step-one"></a>

If you're setting up your landing zone in a new AWS account, see [Setting up](setting-up.md)\.
+ To set up your landing zone with *new* shared accounts, AWS Control Tower requires two unique email addresses that aren't already associated with an AWS account\. Each of these email addresses will serve as a collaborative inbox \-\- a shared email account \-\- intended for the various users in your enterprise that will do specific work related to AWS Control Tower\.
+ If you are setting up AWS Control Tower for the first time, and if you are bringing existing security and log archive accounts into AWS Control Tower, you can enter the current email addresses of the existing AWS accounts\.

The email addresses are required for:
+ **Audit account** – This account is for your team of users that need access to the audit information made available by AWS Control Tower\. You can also use this account as the access point for third\-party tools that will perform programmatic auditing of your environment to help you audit for compliance purposes\.
+ **Log archive account** – This account is for your team of users that need access to all the logging information for all of your enrolled accounts within registered OUs in your landing zone\.

These accounts are set up in the **Security** OU when you create your landing zone\. As a best practice, we recommend that when you perform actions in these accounts, you should use an AWS SSO user with the appropriately scoped permissions\.

**Note**  
If you specify existing AWS accounts as your **audit** and **log archive** accounts, the existing accounts must pass some pre\-launch checks to ensure that no resources are in conflict with AWS Control Tower requirements\. If these checks are not successful, your landing zone setup may not succeed\. In particular, the accounts must not have existing AWS Config resources\. For more information, see [Considerations for bringing existing security or logging accounts](accounts.md#considerations-for-existing-shared-accounts)\.

For the sake of clarity, this *User Guide* always refers to the shared accounts by their default names: **log archive** and **audit**\. As you read this document, remember to substitute the customized names you give to these accounts initially, if you choose to customize them\. You can view your accounts with their customized names on the **Account details** page\.

**Note**  
We are changing our terminology regarding the default names of some AWS Control Tower organizational units \(OUs\) to align with the AWS multi\-account strategy\. You may notice some inconsistencies while we are making a transition to improve the clarity of these names\. The Security OU was formerly called the Core OU\. The Sandbox OU was formerly called the Custom OU\.