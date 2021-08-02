# Configuration update management in AWS Control Tower<a name="configuration-updates"></a>

It is the responsibility of the members of your central cloud administrators' team to keep your landing zone updated\. Updating your landing zone ensures that AWS Control Tower is patched and updated\. In addition, to protect your landing zone from potential compliance issues, the members of the central cloud administrator team should resolve drift issues as soon as they're detected and reported\.

**Note**  
 The AWS Control Tower console indicates when your landing zone needs to be updated\. If you don't see an option to update, your landing zone is already up to date\.

The following table contains a list of AWS Control Tower landing zone update releases, with links to descriptions of each release\.


| **Version** | Release date | Description | 
| --- | --- | --- | 
| 2\.7 | 4\-8\-2021 | [Landing zone version 2\.7](https://docs.aws.amazon.com/controltower/latest/userguide/2021-all.html#version-2.7) | 
| 2\.6 | 12\-29\-2020 | [Landing zone version 2\.6](https://docs.aws.amazon.com/controltower/latest/userguide/January-June-2020.html#config-aggregator-12-2020) | 
| 2\.5 | 11\-18\-2020 | [Landing zone version 2\.5](https://docs.aws.amazon.com/controltower/latest/userguide/January-June-2020.html#region-expansion-11-19-20)  | 
| 2\.4 | None | None | 
| 2\.3 | 3\-5\-2020 | [Landing zone version 2\.3](https://docs.aws.amazon.com/controltower/latest/userguide/January-June-2020.html#Available_in_Sydney) | 
| 2\.2 | 11\-13\-19 | [Landing zone version 2\.2](https://docs.aws.amazon.com/controltower/latest/userguide/January-December-2019.html#Version-2-2)  | 
| 2\.1 | 6\-24\-19 | [Landing zone version 2\.1](https://docs.aws.amazon.com/controltower/latest/userguide/January-December-2019.html#Version-2-1) | 

## About Updates<a name="about-updates"></a>

Updates are required to correct governance drift, or to move to a new version of AWS Control Tower\. To perform a complete update of AWS Control Tower, you must update your landing zone first and then update the enrolled accounts individually\. You may need to perform three types of updates at different times\.
+ **A landing zone update:** Most often this type of update is performed by choosing **Update** on the **Landing zone settings** page\. You may need to perform a landing zone update to repair certain types of drift, and you can choose **Repair** when necessary\.
+ **An update of one or more individual accounts:** You must update accounts if the associated information changes, or if certain types of drift have occurred\. Accounts may be updated by a manual process, by choosing **Re\-register OU**, or with an automated scripting approach, described in a later section of this page\.
+ **A full update:** A full update includes an update of your landing zone, followed by an update of all the enrolled accounts in your registered OU\. Full updates are required with a new release of AWS Control Tower such as 2\.6, 2\.7, and so forth\.

## Update Your Landing Zone<a name="update-controltower"></a>

The easiest way to update your AWS Control Tower landing zone is through the **Landing zone settings** page, which you can reach by choosing **Landing zone settings** in the left navigation of the AWS Control Tower dashboard\.

The **Landing zone settings** page shows you the current version of your landing zone, and it lists any updated versions that may be available\. You can choose the **Update** button if you need to update your version\. 

**Note**  
Alternatively, you can update your landing zone manually\. The update takes approximately the same amount of time, whether you use the **Update** button or the manual process\. To perform a manual update of your landing zone only, see steps 1 and 2 that follow\.

### Manual updates<a name="manual-update"></a>

The following procedure walks you through the steps of a full update for AWS Control Tower manually\. To update an individual account, start at Step 3\.

**To update your landing zone manually, with any number of accounts per OU**

1. Open a web browser, and navigate to the AWS Control Tower console at [https://console\.aws\.amazon\.com/controltower/home/update](https://console.aws.amazon.com/controltower/home/update)\.

1. Review the information in the wizard and choose **Update**\. This updates the backend of the landing zone as well as your shared accounts\. This process can take a little more than half an hour\.

1. Update your member accounts \(must be used for an OU that contains over 300 accounts\)\. From the navigation pane, choose **Accounts**\.

1. Choose **Enroll account** to open the Account Factory product\.

1. From the navigation pane, choose **Provisioned products list**\.

1. For each account listed, perform the following steps to update all your member accounts:

   1. From the menu for the account, choose **Provisioned product details**\.

   1. Make a note of the following parameters:
      + **SSOUserEmail** \(Available in provisioned product details\)
      + **AccountEmail** \(Available in provisioned product details\)
      + **SSOUserFirstName** \(Available in SSO\)
      + **SSOUSerLastName** \(Available in SSO\)
      + **AccountName** \(Available in SSO\)

   1. From **Actions**, choose **Update**\.

   1. Choose the radio button next to the **Version** of the product you want to update, and choose **Next**\.

   1. Provide the parameter values that were mentioned previously\. For **ManagedOrganizationalUnit** choose the OU that the account is in\. You can find this information in the AWS Control Tower console, under **Accounts**\.

   1. Choose **Next**\.

   1. Review your changes, and then choose **Update**\. This process can take a few minutes per account\.

**Optionally Re\-register OU to update accounts**  
For registered AWS Control Tower OUs with fewer than 300 accounts, you can go to the **OU page** in the dashboard and select **Re\-register OU** to update the accounts in that OU\.

## Resolve Drift<a name="resolve-drift"></a>

Drift often occurs as you and your organization members use the landing zone\.

Drift detection is automatic in AWS Control Tower\. Automated scans of your SCPs help you identify resources that need changes or configuration updates that must be made to resolve the drift\. 

To repair most types of drift, choose **Repair** on the **Landing zone settings** page\. Also, you can repair some types of drift by choosing to ** Re\-register** an OU\. For more information about types of drift and how to resolve them, see [Types of Governance Drift](drift.md#governance-drift) and [Detect and resolve drift in AWS Control Tower](drift.md)\. 

## Provisioning and updating accounts using script automation<a name="update-accounts-by-script"></a>

You can provision or update individual accounts by using the [API framework](https://docs.aws.amazon.com/servicecatalog/latest/dg/API_Reference.html) of AWS Service Catalog and the AWS CLI to update the accounts in a batch process\. You'd call the [https://docs.aws.amazon.com/servicecatalog/latest/dg/API_UpdateProvisionedProduct.html](https://docs.aws.amazon.com/servicecatalog/latest/dg/API_UpdateProvisionedProduct.html) API of AWS Service Catalog for each account\. You can write a script to update the accounts, one by one, with this API\. More information about this approach, when adding Regions for governance, is available in a blog post, [Enabling guardrails in new AWS Regions](http://aws.amazon.com/blogs/architecture/field-notes-enabling-guardrails-in-new-aws-regions-the-aws-control-tower-supports/)\.

You must wait for each account update to succeed before beginning the next account update\. Therefore, the process may take a long time if you have a lot of accounts, but it is not complicated\. For more information about this approach, see the [Walkthrough: Automated Account Provisioning in AWS Control Tower](automated-provisioning-walkthrough.md)\. 

**Note**  
The [Video Walkthrough](automated-provisioning-walkthrough.md#automated-provisioning-video) is designed for automated account provisioning, but the steps also apply to account updating\. Use the `UpdateProvisionedProduct` API instead of the `ProvisionProduct` API\.

A further step of automation is to check for **Succeed** status of the AWS Control Tower `UpdateLandingZone` lifecycle event\. Use it as a trigger to begin updating individual accounts as described in the video\. A lifecycle event marks the completion of a sequence of activities, so the occurrence of this event means that a landing zone update is complete\. The landing zone update must be complete before account updates begin\. For more information about working with lifecycle events, see [ Lifecycle Events](https://docs.aws.amazon.com/controltower/latest/userguide/lifecycle-events.html)\.