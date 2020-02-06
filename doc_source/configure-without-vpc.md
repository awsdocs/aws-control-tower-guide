# Walkthrough: Configuring AWS Control Tower Without a VPC<a name="configure-without-vpc"></a>

This topic walks through how to configure your AWS Control Tower accounts without a VPC\.

If your workload does not require a VPC, you can do the following:
+ You can delete the AWS Control Tower master account virtual private cloud \(VPC\)\. This VPC was created when you set up your landing zone\.
+ You can change your Account Factory settings so that new AWS Control Tower accounts are created without an associated VPC\.

## Delete the AWS Control Tower Master Account VPC<a name="delete-master-vpc"></a>

Outside of AWS Control Tower, every AWS customer has a default VPC, which you can view on the Amazon Virtual Private Cloud \(Amazon VPC\) console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\. You’ll recognize the default VPC, because its name always includes the word *\(default\)* at the end of the name\.

When you set up a AWS Control Tower landing zone, AWS Control Tower deletes your AWS default VPC and creates a new AWS Control Tower default VPC\. The new VPC is associated with your AWS Control Tower master account\. This topic refers to that new VPC as the *Control Tower master account VPC*\.

When you view your AWS Control Tower master account VPC in the Amazon VPC console, you will *not* see the word *\(default\)* at the end of the name\. If you have more than one VPC, you must use the assigned CIDR range to identify the correct AWS Control Tower master account VPC\.

You can delete the AWS Control Tower master account VPC, but if you later need a VPC in AWS Control Tower, you must create it yourself\.

**To delete the AWS Control Tower master account VPC**

1. Open the Amazon VPC console at [https://console\.aws\.amazon\.com/vpc/](https://console.aws.amazon.com/vpc/)\.

1. Search for **VPC** or select **VPC** from the AWS Service Catalog options\. You then see the **VPC Dashboard**\.

1. From the menu on the left, choose **Your VPCs**\. You then see a list of all your VPCs\.

1. Identify the AWS Control Tower master account VPC by its CIDR range\.

1. To delete the VPC, choose **Actions** and then choose **Delete VPC**\.

An AWS *\(default\)* VPC already exists in every region for the AWS Control Tower master account\. To follow security best practices, if you choose to delete the AWS Control Tower master account VPC, it's best also to delete the AWS default VPC associated with the master account from all AWS Regions\. Therefore, to secure the master account, remove the default VPC from each Region, as well as removing the VPC created by Control Tower in your AWS Control Tower home region\.

## Create an Account in AWS Control Tower Without a VPC<a name="create-without-vpc"></a>

If your end user workloads do not require VPCs, you can use this method to set up user accounts that don't have VPCs created for them automatically\.

From the AWS Control Tower dashboard, you can view and edit your network configurations settings\. After you change the settings so that AWS Control Tower accounts are created without an associated VPC, all new accounts are created without a VPC until you change the settings again\.

**To configure Account Factory for creating accounts without VPCs**

1. Open a web browser, and navigate to the AWS Control Tower console at [https://console\.aws\.amazon\.com/controltower](https://console.aws.amazon.com/controltower)\.

1. Choose **Account Factory** from the menu on the left\.

1. You then see the Account Factory page with the **Network Configuration** section\.

1. Note the current settings if you intend to restore them later\.

1. Choose the **Edit** button in the **Network Configuration** section\.

1.  In the **Edit account factory network configuration** page, go to the **VPC Configuration options for new accounts** section\.

   1. Turn off the **Internet\-accessible subnet** toggle switch\.

   1. Set the **Maximum number of private subnets** value to 0\.

   1. Change the **Address range \(CIDR\) restriction for account VPCs** value to `10.0.0.0/16`

   1. Clear every checkbox in the **Regions for VPC creation** column\.

1. Choose **Save**\.

### Possible Errors<a name="error-notes"></a>

Be aware of these possible errors that could occur when you delete your AWS Control Tower master account VPC or reconfigure Account Factory to create accounts without VPCs\. 
+ Your existing master account may have dependencies or resources in the AWS Control Tower master account VPC, which can cause a *deletion failure* error\. 
+ If you leave the default CIDR in place when setting up to launch new accounts without a VPC, your request fails with an error that *the CIDR is not valid*\.