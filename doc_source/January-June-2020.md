# January \- December 2020<a name="January-June-2020"></a>

In 2020, AWS Control Tower released the following updates:
+ [AWS Control Tower console now links to external AWS Config rules](#config-aggregator-12-2020)
+ [AWS Control Tower now available in additional Regions](#region-expansion-11-19-20)
+ [Guardrail update](#control-update)
+ [AWS Control Tower console shows more detail about OUs and accounts](#OU-account-detail)
+ [Use AWS Control Tower to set up new multi\-account AWS environments in AWS Organizations](#multiaccount-environments) 
+ [Customizations for AWS Control Tower solution](#Customizations)
+ [General availability of AWS Control Tower version 2\.3 ](#Available_in_Sydney)
+ [Single\-step account provisioning in AWS Control Tower ](#Single-step-provisioning)
+ [AWS Control Tower decommissioning tool](#Decommissioning-tool)
+ [AWS Control Tower lifecycle event notifications](#Lifecycle-event-notifications)

## AWS Control Tower console now links to external AWS Config rules<a name="config-aggregator-12-2020"></a>

 **December 29, 2020**

\(Update required for AWS Control Tower landing zone to version 2\.6\. For information, see [Update Your Landing Zone](update-controltower.md)\)

AWS Control Tower now includes an organization\-level aggregator, which assists in detecting external AWS Config rules\. This provides you with visibility in the AWS Control Tower console to see the existence of externally created AWS Config rules in addition to those AWS Config rules created by AWS Control Tower\. The aggregator allows AWS Control Tower to detect external rules and provide a link to the AWS Config console without the need for AWS Control Tower to gain access to unmanaged accounts\.

With this feature, you now have a consolidated view of detective controls applied to your accounts so you can track compliance and determine if you need additional controls for your account\. For information, see [How AWS Control Tower aggregates AWS Config rules in unmanaged OUs and accounts](roles-how.md#config-role-for-organizations)\.

## AWS Control Tower now available in additional Regions<a name="region-expansion-11-19-20"></a>

 **November 18, 2020**

\(Update required for AWS Control Tower landing zone to version 2\.5\. For information, see [Update Your Landing Zone](update-controltower.md)\)

AWS Control Tower is now available in 5 additional AWS Regions:
+ Asia Pacific \(Singapore\) Region
+ Europe \(Frankfurt\) Region
+ Europe \(London\) Region
+ Europe \(Stockholm\) Region
+ Canada \(Central\) Region

The addition of these 5 AWS Regions is the only change introduced for version 2\.5 of AWS Control Tower\.

AWS Control Tower is also available in US East \(N\. Virginia\) Region, US East \(Ohio\) Region, US West \(Oregon\) Region, Europe \(Ireland\) Region, and Asia Pacific \(Sydney\) Region\. With this launch AWS Control Tower is now available in 10 AWS Regions\.

This landing zone update includes all Regions listed and cannot be undone\. After updating your landing zone to version 2\.5, you must manually update all enrolled accounts for AWS Control Tower to govern in the 10 supported AWS Regions\. For information, see [Configure your AWS Control Tower Regions](region-how.md#deploying-to-new-region)\.

## Guardrail update<a name="control-update"></a>

**October 8, 2020**

\(No update required for AWS Control Tower landing zone\)

An updated version has been released for the mandatory control `AWS-GR_IAM_ROLE_CHANGE_PROHIBITED`\.

This change to the control is required because accounts that are being enrolled automatically into AWS Control Tower must have the `AWSControlTowerExecution` role enabled\. The previous version of the control prevents this role from being created\.

For more information, see [Control update](mandatory-controls.md#control-update-hotfix) in the AWS Control Tower User Guide Control reference\.

## AWS Control Tower console shows more detail about OUs and accounts<a name="OU-account-detail"></a>

**July 22, 2020**

\(No update required for AWS Control Tower landing zone\)

You can view your organizations and accounts that are not enrolled in AWS Control Tower, alongside organizations and accounts that are enrolled\. 

Within the AWS Control Tower console, you can view more detail about your AWS accounts and organizational units \(OUs\)\. The **Accounts** page now lists all accounts in your organization, regardless of OU or enrollment status in AWS Control Tower\. You can now search, sort, and filter across all tables\.

## Use AWS Control Tower to set up new multi\-account AWS environments in AWS Organizations<a name="multiaccount-environments"></a>

**April 22, 2020**

\(No update required for AWS Control Tower landing zone\)

AWS Organizations customers can now use AWS Control Tower to manage newly created organizational units \(OUs\) and accounts by taking advantage of these new capabilities: 
+ Existing AWS Organizations customers can now set up a new landing zone for new organizational units \(OUs\) in their existing management account\. You can create new OUs in AWS Control Tower and create new accounts in those OUs with AWS Control Tower governance\.
+ AWS Organizations customers can enroll existing accounts using the account enrollment process or through scripting\.

AWS Control Tower provides an orchestration service that uses other AWS services\. It's designed for organizations with multiple accounts and teams who are looking for the easiest way to set up their new or existing multi\-account AWS environment and govern at scale\. With an organization governed by AWS Control Tower, cloud administrators know that accounts in the organization are compliant with established policies\. Builders benefit because they can provision new AWS accounts quickly, without undue concerns about compliance\.

For information about setting up a landing zone, see [Plan your AWS Control Tower landing zone](planning-your-deployment.md)\. You can also visit the AWS Control Tower [product webpage](http://aws.amazon.com/controltower/) or visit YouTube to watch this video about [getting started with AWS Control Tower for AWS Organizations](https://www.youtube.com/watch?v=-n65I4M8cas)\. 

In addition to this change, the **Quick account provisioning** capability in AWS Control Tower was renamed to **Enroll account**\. It now permits enrollment of existing AWS accounts as well as creation of new accounts\. For more information, see [Enroll an existing account](quick-account-provisioning.md)\.

## Customizations for AWS Control Tower solution<a name="Customizations"></a>

**March 17, 2020**

\(No update required for AWS Control Tower landing zone\)

AWS Control Tower now includes a new reference implementation that makes it easy for you to apply custom templates and policies to your AWS Control Tower landing zone\. 

With customizations for AWS Control Tower, you can use AWS CloudFormation templates to deploy new resources to existing and new accounts within your organization\. You can also apply custom service control policies \(SCPs\) to those accounts in addition to the SCPs already provided by AWS Control Tower\. Customizations for AWS Control Tower pipeline integrate with AWS Control Tower lifecycle events and notifications \([Lifecycle Events in AWS Control Tower](lifecycle-events.md)\) to ensure that resource deployments stay in sync with your landing zone\.

The deployment documentation for this AWS Control Tower solution architecture is available through the [AWS Solutions web page](http://aws.amazon.com/solutions/implementations/customizations-for-aws-control-tower/)\. 

## General availability of AWS Control Tower version 2\.3<a name="Available_in_Sydney"></a>

**March 5, 2020** 

\(Update required for AWS Control Tower landing zone\. For information, see [Update Your Landing Zone](update-controltower.md)\.\)

AWS Control Tower is now available in the Asia Pacific \(Sydney\) AWS Region, in addition to the US East \(Ohio\), US East \(N\. Virginia\), US West \(Oregon\), and Europe \(Ireland\) Regions\. The addition of the Asia Pacific \(Sydney\) Region is the only change introduced for version 2\.3 of AWS Control Tower\.

If you have not used AWS Control Tower previously, you can launch it today in any of the supported Regions\. If you are already using AWS Control Tower and want to extend its governance features to the Asia Pacific \(Sydney\) Region in your accounts, go to the **Settings** page in your AWS Control Tower dashboard\. From there, update your landing zone to the latest release\. Then, update your accounts individually\. 

**Note**  
Updating your landing zone does not automatically update your accounts\. If you have more than a few accounts, the required updates can be time\-consuming\. For that reason, we recommend that you avoid expanding your AWS Control Tower landing zone into Regions in which you do not require your workloads to run\.

 For information about the expected behavior of detective controls as a result of a deployment to a new Region, see [Configure your AWS Control Tower Regions](region-how.md#deploying-to-new-region)\.

## Single\-step account provisioning in AWS Control Tower<a name="Single-step-provisioning"></a>

**March 2, 2020**

\(No update required for AWS Control Tower landing zone\)

AWS Control Tower now supports single\-step account provisioning through the AWS Control Tower console\. This feature allows you to provision new accounts from within the AWS Control Tower console\.

To use the simplified form, navigate to **Account Factory** in the AWS Control Tower console and then choose **Quick account provisioning**\. AWS Control Tower assigns the same email address to the provisioned account and to the single sign\-on \(IAM Identity Center\) user that is created for the account\. If you require these two email addresses to be different, you must provision your account through Service Catalog\.

Update accounts that you create through quick account provisioning by using Service Catalog and the AWS Control Tower account factory, just like updates to any other account\.

**Note**  
In April 2020, the **Quick account provisioning** capability was renamed to **Enroll account**\. In June 2022, the ability to create and update accounts in the AWS Control Tower console was separated from the ability to enroll AWS accounts\. For more information, see [Enroll an existing account](quick-account-provisioning.md)\.

## AWS Control Tower decommissioning tool<a name="Decommissioning-tool"></a>

**February 28, 2020 **

\(No update required for AWS Control Tower landing zone\)

AWS Control Tower now supports an automated decommissioning tool to assist you in cleaning up resources allocated by AWS Control Tower\. If you no longer intend to use AWS Control Tower for your enterprise, or if you require a major redeployment of your organizational resources, you may want to clean up the resources created when you initially set up your landing zone\. 

To decommission your landing zone by using a process that is mostly automated, contact AWS Support to get assistance with the additional steps that are required\. For more information about decommissioning, see [Walkthrough: Decommission an AWS Control Tower Landing Zone](decommission-landing-zone.md)\.

## AWS Control Tower lifecycle event notifications<a name="Lifecycle-event-notifications"></a>

**January 22, 2020**

\(No update required for AWS Control Tower landing zone\)

AWS Control Tower announces the availability of lifecycle event notifications\. A [lifecycle event](lifecycle-events.md) marks the completion of an AWS Control Tower action that can change the state of resources such as organizational units \(OUs\), accounts, and controls that are created and managed by AWS Control Tower\. Lifecycle events are recorded as AWS CloudTrail events and delivered to Amazon EventBridge as events\. 

AWS Control Tower records lifecycle events at the completion of the following actions that can be performed using the service: creating or updating a landing zone; creating or deleting an OU; enabling or disabling a control on an OU; and using account factory to create a new account or to move an account to another OU\.

AWS Control Tower uses multiple AWS services to build and govern a best practices multi\-account AWS environment\. It can take several minutes for an AWS Control Tower action to complete\. You can track lifecycle events in the CloudTrail logs to verify if the originating AWS Control Tower action completed successfully\. You can create an EventBridge rule to notify you when CloudTrail records a lifecycle event or to automatically trigger the next step in your automation workflow\.