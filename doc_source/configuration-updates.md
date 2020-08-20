# Configuration update management in AWS Control Tower<a name="configuration-updates"></a>

It is the responsibility of the members of your central cloud administrators' team to keep your landing zone updated\. Updating your landing zone ensures that AWS Control Tower is patched and updated\. In addition, to protect your landing zone from potential compliance issues, the members of the central cloud administrator team should resolve drift issues as soon as they're detected and reported\.

**Note**  
 The AWS Control Tower console indicates when your landing zone needs to be updated\. If you don't see an option to update, your landing zone is already up to date\.

## About Updates<a name="about-updates"></a>

Updates are required to correct governance drift, or to move to a new version of AWS Control Tower\. To perform a complete update of AWS Control Tower, you must update your landing zone first and then update the enrolled accounts individually\. You may need to perform three types of updates at different times\.
+ **A landing zone update:** Most often this type of update is performed by choosing **Update** on the **Settings** page\. You may need to perform a landing zone update to repair certain types of drift, and you can choose **Repair** when necessary\.
+ **An update of one or more individual accounts:** You must update accounts if the associated information changes, or if certain types of drift have occurred\. Accounts may be updated by following the manual process, or with an automated approach\. Both are described in later sections of this page\.
+ **A full update:** A full update includes an update of your landing zone, followed by an update of all the enrolled accounts in your registered OU\. Full updates are required with a new release of AWS Control Tower such as 2\.3, 2\.4, and so forth\.

## Update Your Landing Zone<a name="update-controltower"></a>

The easiest way to update your AWS Control Tower landing zone is through the **Settings** page\. Navigate to the **Settings** page by choosing **Settings** in the left navigation\.

The **Settings** page shows you the current version of your landing zone, and it lists any updated versions that may be available\. You can choose the **Update** button if you need to update your version\. If the **Update** button appears greyed\-out, you do not need to update\.

**Note**  
Alternatively, you can update your landing zone manually\. The update takes approximately the same amount of time, whether you use the **Update ** button or the manual process\. To perform a manual update of your landing zone only, see steps 1 and 2 that follow\.

The following procedure walks you through the steps of a full update for AWS Control Tower manually\. To update an individual account, start at Step 3\.

**To update your landing zone manually**

1. Open a web browser, and navigate to the AWS Control Tower console at [https://us\-west\-2\.console\.aws\.amazon\.com/controltower/home/update](https://console.aws.amazon.com/controltower/home/update)\.

1. Review the information in the wizard and choose **Update**\. This updates the backend of the landing zone as well as your shared accounts\. This process can take a little more than an hour\.

1. Update your member accounts\. From the navigation pane, choose **Accounts**\.

1. Choose **Enroll account** to open the AWS Service Catalog console and the Account Factory product\.

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

## Resolve Drift<a name="resolve-drift"></a>

When you create your AWS Control Tower landing zone, the landing zone and all the OUs, accounts, and resources are compliant with all of the governance rules enforced by your guardrails, whether mandatory or elective\. As you and your organization members use the landing zone, changes in compliance status may occur\. Some changes may be accidental, and some may be made intentionally to respond to time\-sensitive operational events\. Regardless, changes can complicate your compliance story\.

Resolving drift helps to ensure your organization's compliance with governance regulations\. Drift resolution is a regular operations task for your master account administrators\.

Drift detection is automatic in AWS Control Tower\. It helps you identify resources that need changes or configuration updates that must be made to resolve the drift\.

To repair most types of drift, choose **Repair** on the **Settings** page\. The **Repair** button becomes selectable when drift has occurred\. For more information, see [Detect and resolve drift in AWS Control Tower](drift.md)\.

## Deploying AWS Control Tower to a New AWS Region<a name="deploying-to-new-region"></a>

This section describes the behavior you can expect when you deploy your AWS Control Tower landing zone into a new AWS Region\. Generally, this type of deployment is performed through the **Update** function of the AWS Control Tower console\.

**Note**  
We recommend that you avoid expanding your AWS Control Tower landing zone into AWS Regions in which you do not require your workloads to run\.

During deployment into a new AWS Region, AWS Control Tower updates the landing zone, which means that it *baselines* your landing zone to operate actively in the new Region\. Individual accounts within your organizational units \(OUs\) that are managed by AWS Control Tower are not updated as part of this landing zone update process\. Therefore, you must apply updates to your accounts individually\. 

Certain significant behavioral changes in AWS Control Tower detective guardrails are expected as a result of a deployment to a new AWS Region:
+ *What exists stays the same\.* Guardrail behavior, detective as well as preventive, is unchanged for existing accounts, in existing OUs, in existing Regions\.
+ *You can’t apply new detective guardrails to existing OUs containing accounts that are not updated\.* When you’ve deployed your AWS Control Tower landing zone into the new Region \(by updating it\), you must update existing accounts in your existing OUs before you can enable new detective guardrails on those OUs and accounts\.
+ *Your existing detective guardrails begin working in the new Region as soon as you update the accounts\.* When you update your AWS Control Tower landing zone to deploy into the new Region and then update an account, the detective guardrails that already are enabled on the OU will begin working on that account in the new Region\. 
+ *Update only to Regions in which you need to run\.* It can be time\-consuming to deploy landing zones into new Regions if your OUs manage a lot of member accounts\. We recommend that you avoid expanding your AWS Control Tower deployment into AWS Regions in which you do not require your workloads to run\.

**Updating accounts using automation**

One method of updating individual accounts after deployment to a new Region is by using the API framework of AWS Service Catalog and the AWS CLI to update the accounts in a batch process\. You'd call the [https://docs.aws.amazon.com/servicecatalog/latest/dg/API_UpdateProvisionedProduct.html](https://docs.aws.amazon.com/servicecatalog/latest/dg/API_UpdateProvisionedProduct.html) API of AWS Service Catalog for each account\. You can write a script to update the accounts, one by one, with this API\. More information about this approach is available in a [blog post](http://aws.amazon.com/blogs/field-notes/enabling-guardrails-in-new-aws-regions-the-aws-control-tower-supports/)\.

You must wait for each account update to succeed before beginning the next account update\. Therefore, the process may take a long time if you have a lot of accounts, but it is not complicated\. For more information about this approach, see the [Walkthrough: Automated Account Provisioning in AWS Control Tower](automated-provisioning-walkthrough.md)\. 

**Note**  
The [Video Walkthrough](automated-provisioning-walkthrough.md#automated-provisioning-video) is designed for automated account provisioning, but the steps also apply to account updating\. Use the `UpdateProvisionedProduct` API instead of the `ProvisionProduct` API\.

A further step of automation is to check for **Succeed** status of the AWS Control Tower `UpdateLandingZone` lifecycle event\. Use it as a trigger to begin updating individual accounts as described in the video\. A lifecycle event marks the completion of a sequence of activites, so the occurrence of this event means that a landing zone update is complete\. The landing zone update must be complete before account updates begin\. For more information about working with lifecycle events, see [ Lifecycle Events](https://docs.aws.amazon.com/controltower/latest/userguide/lifecycle-events.html)\.