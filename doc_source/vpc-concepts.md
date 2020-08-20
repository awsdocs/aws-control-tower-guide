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

If the default configuration or capabilities of the AWS Control Tower VPC do not meet your needs, you can use other AWS services to configure your VPC\. For more information about how to work with VPCs and AWS Control Tower see [Building a Scalable and Secure Multi\-VPC AWS Network Infrastructure](https://d1.awsstatic.com/whitepapers/building-a-scalable-and-secure-multi-vpc-aws-network-infrastructure.pdf)\.

**Note**  
If you set the Account Factory VPC configuration so that public subnets are enabled when provisioning a new account, Account Factory configures VPC to create a NAT Gateway\. You will be billed for your usage by Amazon VPC\.

## CIDR and Peering for VPC and AWS Control Tower<a name="vpc-ct-cidr"></a>

When you choose a CIDR range for your VPC, AWS Control Tower validates the IP address ranges according to the RFC 1918 specification\. Account Factory allows a CIDR block of up to `/16` in the ranges of: 
+ `10.0.0.0/8`
+ `172.16.0.0/12`
+ `192.168.0.0/16`
+ `100.64.0.0/10` \(only if your internet provider allows usage of this range\)

The `/16` delimiter allows up to 65,536 distinct IP addresses\.

You can assign any valid IP addresses from the following ranges:
+ `10.0.x.x to 10.255.x.x`
+ `172.16.x.x – 172.31.x.x`
+ `192.168.0.0 – 192.168.255.255` \(no IPs outside of `192.168` range\)

If the range you specify is outside of these, AWS Control Tower provides an error message\.

The default CIDR range is `172.31.0.0/16`\.

When AWS Control Tower creates a VPC using the CIDR range you select, it assigns the identical CIDR range to *every VPC* for every account you create within the organizational unit \(OU\)\. Due to the default overlap of IP addresses, this implementation does not initially permit peering among any of your AWS Control Tower VPCs in the OU\.

**Subnets**

Within each VPC, AWS Control Tower divides your specified CIDR range evenly into nine subnets\. None of the subnets within a VPC overlap\. Therefore, they all can communicate with each other, within the VPC\.

In summary, by default, subnet communication within the VPC is unrestricted\. The best practice for controlling communication among your VPC subnets, if needed, is to set up access control lists with rules that define the permitted traffic flow\. Use security groups for control of traffic among specific instances\. For more information about setting up security groups and firewalls in AWS Control Tower, see [Walkthrough: Setting Up Security Groups in AWS Control Tower With AWS Firewall Manager](firewall-setup-walkthrough.md)\.

**Peering**

AWS Control Tower does not restrict VPC\-to\-VPC peering for communication across multiple VPCs\. However, by default, all AWS Control Tower VPCs have the same default CIDR range\. To support peering, you can modify the CIDR range in the settings of Account Factory so that the IP addresses do not overlap\.

If you change the CIDR range in the settings of Account Factory, all new accounts that are subsequently created by AWS Control Tower \(using Account Factory\) are assigned the new CIDR range\. The old accounts are not updated\. For example, you can create an account, then change the CIDR range and create a new account, and the VPCs allocated to those two accounts can be peered\. Peering is possible because their IP address ranges are not identical\.

For information about how to change account settings for VPCs, see the [ Account Factory documentation](https://docs.aws.amazon.com/controltower/latest/userguide/account-factory.html#configuring-account-factory-with-VPC-settings) on updating an account\.

For information about how to configure AWS Control Tower without a VPC, see [Walkthrough: Configuring AWS Control Tower Without a VPC](configure-without-vpc.md)\.

## Notes on VPC and CIDR<a name="bad-peering-options"></a>

Some network administrators may realize that it is possible to peer two subnets in two different VPCs \(that is, in two different accounts\) without changing the CIDR settings for accounts\. Because the nine subnets in a VPC do not overlap, peering technically is possible from *\[VPC1, subnet 1\]* to *\[VPC2, subnet 2\]*, for example\. However, this approach depends on an implementation detail of how subnet ranges are allocated within a VPC\. We don't recommend this method of peering, because it could fail at any time\.

When working with VPCs, AWS Control Tower makes no distinction at the Region level\. Every subnet is allocated from the exact CIDR range that you specify\. The VPC subnets can exist in any Region\.