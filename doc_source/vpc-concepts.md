# AWS Control Tower and VPCs<a name="vpc-concepts"></a>

## <a name="vpc-ct-facts"></a>

This section is intended primarily for network administrators\. Your company’s network administrator usually is the person who selects the overall CIDR range for your AWS Control Tower organization\. The network administrator then allocates subnets from within that range for specific purposes\.

Here are some essential facts about AWS Control Tower VPCs:
+ The VPC created by AWS Control Tower when you provision an account in Account Factory is not the same as the AWS default VPC\.
+ When AWS Control Tower sets up a new account in a supported AWS Region, AWS Control Tower automatically deletes the default AWS VPC, and it sets up a new VPC configured by AWS Control Tower\.
+ Each AWS Control Tower account is allowed one VPC that's created by AWS Control Tower\. An account can have additional AWS VPCs within the account limit\.
+ Every AWS Control Tower VPC has three Availability Zones\. By default, each Availability Zone is assigned one public subnet and two private subnets\. Therefore, each AWS Control Tower VPC contains nine subnets by default, divided into three Availability Zones\.
+ Each of the nine subnets in your AWS Control Tower VPC is assigned a unique range, of equal size\.
+ The number of subnets in a VPC is configurable\. For more information about how to change your VPC subnet configuration, see [the Account Factory topic](https://docs.aws.amazon.com/controltower/latest/userguide/account-factory.html)\.
+ Because the IP addresses do not overlap, the nine subnets within your AWS Control Tower VPC can communicate with each other in an unrestricted manner\.

**Important**  
If you provision Account Factory accounts with VPC internet access settings enabled, that Account Factory setting overrides the guardrail [Disallow internet access for an Amazon VPC instance managed by a customer](data-residency-guardrails.md#disallow-vpc-internet-access)\. To avoid enabling internet access for newly provisioned accounts, you must change the setting in Account Factory\. For more information, see [Walkthrough: Configure AWS Control Tower Without a VPC](configure-without-vpc.md)\.

If the default configuration or capabilities of the AWS Control Tower VPC do not meet your needs, you can use other AWS services to configure your VPC\. For more information about how to work with VPCs and AWS Control Tower see [Building a Scalable and Secure Multi\-VPC AWS Network Infrastructure](https://d1.awsstatic.com/whitepapers/building-a-scalable-and-secure-multi-vpc-aws-network-infrastructure.pdf)\.

**Note**  
If you set the Account Factory VPC configuration so that public subnets are enabled when provisioning a new account, Account Factory configures VPC to create a NAT Gateway\. You will be billed for your usage by Amazon VPC\.

When working with VPCs, AWS Control Tower makes no distinction at the Region level\. Every subnet is allocated from the exact CIDR range that you specify\. The VPC subnets can exist in any Region\.