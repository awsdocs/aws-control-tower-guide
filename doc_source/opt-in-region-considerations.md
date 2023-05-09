# Considerations for activating AWS opt\-in Regions<a name="opt-in-region-considerations"></a>

Although most AWS Regions are active by default for your AWS account, certain Regions are activated only when you manually select them\. This document refers to those Regions as *opt\-in Regions*\. In contrast, Regions that are active by default, as soon as your AWS account is created, are referred to as *commercial Regions*, or simply, *Regions*\.

The term *opt\-in* has a historical basis\. Any AWS Regions introduced after March 20, 2019 are considered to be opt\-in Regions\. Opt\-in Regions have higher security requirements than commercial Regions, regarding the sharing of IAM data through accounts that are active in opt\-in Regions\. All of the data managed through the IAM service is considered identity data, including users, groups, roles, policies, identity providers, their associated data \(for example, X\.509 signing certificates or context\-specific credentials\), and other account\-level settings, such as password policy and the account alias\.

You can activate opt\-in Regions automatically during landing zone setup, by selecting them\. Your landing zone becomes active in all selected Regions\.

If you choose to select an opt\-in Region as your AWS Control Tower home Region, activate it first by following the steps in [Enabling a Region](https://docs.aws.amazon.com/general/latest/gr/rande-manage.html#rande-manage-enable), when signed in to the AWS Management Console\. To bring your own existing Log Archive and Audit accounts from an opt\-in Region, manually activate that Region first\.

The AWS opt\-in Regions include 7 Regions in which AWS Control Tower is available:
+ US West \(N\. California\) Region, us\-west\-1 
+ Asia Pacific \(Hong Kong\) Region, ap\-east\-1
+ Asia Pacific \(Jakarta\) Region, ap\-southeast\-3
+ Asia Pacific \(Osaka\) Region, ap\-northeast\-3 
+ Europe \(Milan\) Region, eu\-south\-1 
+ Africa \(Cape Town\) Region, af\-south\-1
+ Middle East \(Bahrain\) Region, me\-south\-1 

AWS Control Tower has some controls that work differently in the opt\-in Regions than in commercial Regions\. For more information, see [Control limitations](limits.md#control-limitations)\. Here are some considerations to keep in mind as you deploy workloads into opt\-in Regions\.

**Governing or activating?**  
Remember that governing a Region is an action that you can select from the AWS Control Tower console, so that controls can be appplied in the Region\. Activating or deactivating an opt\-in Region is a different action that you can choose in the AWS console, which opens the Region to your account, so that you can deploy resources and workloads in the Region\.

**Behavioral considerations**
+ If you choose to govern opt\-in Regions, we recommend that you do not deactivate \(opt\-out of\) any of your governed opt\-in Regions, because it can lead to failure of your workloads\. AWS Control Tower does not allow deactivation of a governed Region from within the AWS Control Tower console, but be sure that you do not deactivate governed Regions from a source outside of AWS Control Tower, such as the AWS Billing console or AWS SDK\. 
+ When AWS Control Tower extends governance to an opt\-in Region, it activates \(opts\-in\) to the Region in all member accounts\. When you remove a Region from governance, AWS Control Tower does not deactivate \(opt\-out of\) the Region in the member accounts\.
+ During Region deselection, AWS Control Tower skips removing resources from an opt\-in Region if that Region was deactivated manually for an account from a source outside AWS Control Tower, such as the AWS Billing console or AWS SDK\. We recommend that you remove resources from the Regions you’ve deactivated, or you may receive unexpected billing charges for those resources\.
+ If your landing zone is decommissioned, AWS Control Tower cleans up resources in all the governed Regions, including the opt\-in Regions\. However, AWS Control Tower does not deactivate the opt\-in Regions\. You can deactivate the opt\-in Regions as an additional step after decommissioning\.
+ If your home Region is an opt\-in Region, and if you intend to enroll existing accounts as your Log Archive and Audit accounts, you must manually activate the opt\-in Region before you can select it as the home Region for your landing zone\. See [Enabling a Region](https://docs.aws.amazon.com/general/latest/gr/rande-manage.html#rande-manage-enable)\.
+ If AWS Control Tower is set up with an opt\-in Region as your home Region, and if you visit the AWS Control Tower service from the AWS console in any other Region, the console does not redirect you automatically to the home Region\.
+ The underlying API has capacity limits, which may increase latency from a few minutes to many hours, depending on the number of Regions, accounts, and service load\. As a best practice, opt\-in only to those the AWS Regions where you will run workloads, and opt\-in one Region at a time\.

**Important limitations for governance and controls**
+ If you currently have enabled an AWS Control Tower control that is not supported in an opt\-in Region, you will not be able to extend AWS Control Tower governance into that opt\-in Region until the control is supported in that Region\. For more information see [Control limitations](limits.md#control-limitations)\. 
+ If you extend AWS Control Tower governance into an opt\-in Region in which a specific control is not supported, you will not be able to enable that control in any Region until the control is supported in all the Regions you are governing with AWS Control Tower For more information see [Control limitations](limits.md#control-limitations)
+ If all 22 commercial Regions where AWS Control Tower is available are activated, including opt\-in Regions, the upper limit on the number of accounts per organizational unit \(OU\), when extending governance to an OU, is reduced\. The limit is 220 instead of 300 accounts\. This reduction is due to StackSet limitations\. If you require to extend governance to OUs with more than 220 accounts, reduce the number of activated Regions\.