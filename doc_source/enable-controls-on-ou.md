# Enable controls on an OU<a name="enable-controls-on-ou"></a>

Mandatory and inherited controls are enabled automatically according to an OU's configuration\. Optional controls can be enabled manually on your OUs, from the console, or by means of the [control APIs](https://docs.aws.amazon.com/controltower/latest/APIReference/Welcome.html)\. The following procedure describes the steps for enabling controls on an OU, from the console\.

**Important**  
When you enable optional controls, AWS Control Tower creates and manages AWS resources in your accounts\. Do not modify or delete resources created by AWS Control Tower\. Doing so could result in the controls entering an unknown state\.

**To enable controls in an OU, from the console**

1. Using a web browser, navigate to the AWS Control Tower console at [https://console\.aws\.amazon\.com/controltower](https://console.aws.amazon.com/controltower)\.

1. From the left navigation, choose **Controls**\.

1. Choose a control that you want to enable; for example, **Control: Detect Whether Encryption is Enabled for Amazon EBS Volumes Attached to Amazon EC2 Instances**\. This choice opens the control's details page\.

1. From **Organizational units enabled**, choose **Enable control on OU**\.

1. A new page is displayed that lists the names of your OUs\. Identify the OU on which you want to enable this control\.

1. Choose **Enable control on OU**\.

1. Your control is now enabled\. It may take several minutes for the change to complete\. When it does, you'll see that this control is applied to the OU you selected\.

**Note**  
You can enable preventive and detective controls concurrently\.

**To deactivate controls for an OU, from the console**

1. Using a web browser, navigate to the AWS Control Tower console at [https://console\.aws\.amazon\.com/controltower](https://console.aws.amazon.com/controltower)\.

1. From the left navigation, choose **Controls**\.

1. Choose a control that you want to deactivate; for example, **Control: Detect Whether Encryption is Enabled for Amazon EBS Volumes Attached to Amazon EC2 Instances**\. This choice opens the control's details page\.

1. From the **Organizational units enabled** tab, select the radio button next to the OU from which you want to remove the control\.

1. Choose **Disable control** at the upper right\.

1. Your control is now deactivated\. It may take several minutes for the change to complete\. When it does, you'll see that this control is no longer applied to the OU you selected\.

## Concurrent deployment for optional controls<a name="concurrent-optional-controls"></a>

When applying optional controls, you can deploy more than one control at a time, up to the StackSets limitation of 5000 concurrent operations\. For example, you can enable **Detect Whether MFA for the Root User is Enabled** and **Detect Whether Public Write Access to Amazon S3 Buckets is Allowed**, at the same time\.

You can apply and remove multiple optional controls without waiting for individual control operations to complete\. The only restricted times are when AWS Control Tower is in the process of landing zone setup, or while extending governance to a new organization\.

**Functionality available**
+ Apply and remove different detective controls on the same OU, concurrently\. 
+ Apply and remove different detective controls on different OUs, concurrently\. 
+ Apply and remove the same detective control on multiple OUs, concurrently\.
+ Apply and remove different preventive controls on the same OU, concurrently\.
+ Apply and remove different preventive controls on different OUs, concurrently\. 
+ Apply and remove the same preventive control on multiple OUs, concurrently\. 
+ You can apply and remove preventive and detective controls, concurrently\.

You can apply and remove multiple optional controls without waiting for individual control operations to complete\. The only restricted times are when AWS Control Tower is in the process of landing zone setup, or while extending governance to a new organization\.

When you apply preventive controls to nested OUs, the preventive controls affect all accounts and OUs nested under the target OU, even if those accounts and OUs are not registered with AWS Control Tower\. Preventive controls are implemented using Service Control Policies \(SCPs\), which are part of AWS Organizations\. Detective controls are implemented using AWS Config rules\. Controls remain in effect as you create new accounts or make changes to your existing accounts, and AWS Control Tower provides a summary report of how each account conforms to your enabled policies\. For a full list of available controls, see the [The AWS Control Tower controls library](controls-reference.md)\. 