# Walkthrough: Manage AWS Control Tower Resources<a name="walkthrough-delete"></a>

This document provides instructions for how to remove AWS Control Tower resources individually, as part of regular maintenance and administrative tasks\. The procedures given in this chapter are intended only for removing individual resources, or a few resources, when needed\. It not the same as decommissioning your landing zone\.

**Two types of tasks may require you to remove resources:**
+ To delete resources as you manage your landing zone in ordinary situations\.
+ To clean up resources that remain after automated decommissioning\.

**Warning**  
Manually removing resources will not allow you to set up a new landing zone\. It is not the same as decommissioning\. If you intend to decommission your AWS Control Tower landing zone, follow the instructions on [Walkthrough: Decommission an AWS Control Tower Landing Zone](decommission-landing-zone.md) before you take any actions described in this chapter\. The instructions in this chapter can help you clean up resources that remain after automated decommissioning is complete\. Even if you delete all of your landing zone resources manually, it is not the same as decommissioning the landing zone, and you may incur unexpected charges\.

## Do I need decommissioning instead of deleting?<a name="about-decommissioning"></a>

If you no longer intend to use AWS Control Tower for your enterprise, or if you require a major redeployment of your organizational resources, you may want to decommission the resources created when you initially set up your landing zone\.
+ After the decommissioning process is complete, a few resource artifacts remain, such as Amazon S3 buckets and Amazon CloudWatch Logs log groups\.
+ You must clean up the remaining resources in your accounts manually before you set up another landing zone, and to avoid the possibility of unexpected charges\. For more information, see [Resources not removed during decommissioning](resources-not-removed.md)\.

**Warning**  
 We strongly recommend that you perform a decommissioning process *only if* you intend to stop using your landing zone\. This process cannot be undone\.

## About removing AWS Control Tower resources<a name="manual-decommissioning"></a>

The individual procedures in this chapter guide you through manual methods of removing AWS Control Tower resources\. These procedures can be followed when you need to delete a specific resource from your landing zone\.

Before performing these procedures, unless it's otherwise indicated, you must be signed in to the AWS Management Console in the home Region for your landing zone, and you must be signed in as an IAM user with administrative permissions for the management account that contains your landing zone\.

**Warning**  
These are destructive actions that can introduce governance drift into your AWS Control Tower setup\. They cannot be undone\.

**Topics**
+ [Do I need decommissioning instead of deleting?](#about-decommissioning)
+ [About removing AWS Control Tower resources](#manual-decommissioning)
+ [Delete SCPs](controltower-walkthrough-delete-scps.md)
+ [Delete StackSets and Stacks](controltower-walkthrough-delete-stacksets.md)
+ [Delete Amazon S3 Buckets in the Log Archive Account](controltower-walkthrough-delete-s3-buckets.md)
+ [Remove an Account Factory Portfolio and Product](controltower-walkthrough-cleanup-account-factory.md)
+ [Remove AWS Control Tower Roles and Policies](controltower-walkthrough-cleanup-identity.md)
+ [AWS Control Tower resource help](#control-tower-cleanup-help)

## AWS Control Tower resource help<a name="control-tower-cleanup-help"></a>

If you encounter any issues that you can't resolve when you remove AWS Control Tower resources, contact [AWS Support](https://aws.amazon.com/premiumsupport/)\.