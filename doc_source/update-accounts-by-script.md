# Provision and update accounts using automation<a name="update-accounts-by-script"></a>

You can provision or update individual accounts in AWS Control Tower by several methods:
+ You can provision and customize accounts with *AWS Control Tower Account Factory for Terraform* \(AFT\)\. For more information, see [Overview of AWS Control Tower Account Factory for Terraform](aft-overview.md)\.
+ You can update accounts with *Customizations for AWS Control Tower* \(CfCT\)\. For more information, see [Customizations for AWS Control Tower \(CfCT\) overview](cfct-overview.md)\.
+ **Script automation**: If you prefer to use an API approach, you can update accounts using the [API framework](https://docs.aws.amazon.com/servicecatalog/latest/dg/API_Reference.html) of AWS Service Catalog and the AWS CLI to update the accounts in a batch process\. You'd call the [https://docs.aws.amazon.com/servicecatalog/latest/dg/API_UpdateProvisionedProduct.html](https://docs.aws.amazon.com/servicecatalog/latest/dg/API_UpdateProvisionedProduct.html) API of AWS Service Catalog for each account\. You can write a script to update the accounts, one by one, with this API\. More information about this approach, when adding Regions for governance, is available in a blog post, [Enabling guardrails in new AWS Regions](http://aws.amazon.com/blogs/architecture/field-notes-enabling-guardrails-in-new-aws-regions-the-aws-control-tower-supports/)\.

  You must wait for each account update to succeed before beginning the next account update\. Therefore, the process may take a long time if you have a lot of accounts, but it is not complicated\. For more information about this approach, see the [Walkthrough: Automate Account Provisioning in AWS Control Tower by AWS Service Catalog APIs](automated-provisioning-walkthrough.md)\.

**Video walkthrough**  
The [Video Walkthrough](automated-provisioning-walkthrough.md#automated-provisioning-video) is designed for automated account provisioning with a script, but the steps also apply to account updating\. Use the `UpdateProvisionedProduct` API instead of the `ProvisionProduct` API\.

A further step of automation by script is to check for **Succeed** status of the AWS Control Tower `UpdateLandingZone` lifecycle event\. Use it as a trigger to begin updating individual accounts as described in the video\. A lifecycle event marks the completion of a sequence of activities, so the occurrence of this event means that a landing zone update is complete\. The landing zone update must be complete before account updates begin\. For more information about working with lifecycle events, see [Lifecycle Events](https://docs.aws.amazon.com/controltower/latest/userguide/lifecycle-events.html)\.

**Also see:**
+ [Using AWS CloudShell to work with AWS Control Tower](using-aws-with-cloudshell.md)\.
+ [Automate tasks in AWS Control Tower](automating-tasks.md)\.