# Overview of AWS Control Tower and VPCs<a name="vpc-concepts"></a>

## <a name="vpc-ct-facts"></a>

Here are some essential facts about AWS Control Tower VPCs:
+ The VPC created by AWS Control Tower when you provision an account in Account Factory is not the same as the AWS default VPC\.
+ When AWS Control Tower sets up a new account in a supported AWS Region, AWS Control Tower automatically deletes the default AWS VPC, and it sets up a new VPC configured by AWS Control Tower\.
+ Each AWS Control Tower account is allowed one VPC that's created by AWS Control Tower\. An account can have additional AWS VPCs within the account limit\.
+ Every AWS Control Tower VPC has three Availability Zones in all Regions except for the US West \(N\. California\) Region,`us-west-1`, and two Availability Zones in `us-west-1`\. By default, each Availability Zone is assigned one public subnet and two private subnets\. Therefore, in Regions except US West \(N\. California\) each AWS Control Tower VPC contains nine subnets by default, divided across three Availability Zones\. In US West \(N\. California\), six subnets are divided across two Availability Zones\.
+ Each of the subnets in your AWS Control Tower VPC is assigned a unique range, of equal size\.
+ The number of subnets in a VPC is configurable\. For more information about how to change your VPC subnet configuration, see [the Account Factory topic](https://docs.aws.amazon.com/controltower/latest/userguide/account-factory.html)\.
+ Because the IP addresses do not overlap, the six or nine subnets within your AWS Control Tower VPC can communicate with each other in an unrestricted manner\.

When working with VPCs, AWS Control Tower makes no distinction at the Region level\. Every subnet is allocated from the exact CIDR range that you specify\. The VPC subnets can exist in any Region\.

**Notes**

**Manage VPC costs**  
If you set the Account Factory VPC configuration so that public subnets are enabled when provisioning a new account, Account Factory configures VPC to create a NAT Gateway\. You will be billed for your usage by Amazon VPC\.

**VPC and control settings**  
If you provision Account Factory accounts with VPC internet access settings enabled, that Account Factory setting overrides the control [Disallow internet access for an Amazon VPC instance managed by a customer](data-residency-controls.md#disallow-vpc-internet-access)\. To avoid enabling internet access for newly provisioned accounts, you must change the setting in Account Factory\. For more information, see [Walkthrough: Configure AWS Control Tower Without a VPC](configure-without-vpc.md)\.