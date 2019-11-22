# AWS Control Tower and VPCs<a name="vpc-concepts"></a>

## <a name="vpc-ct-facts"></a>

This section is intended primarily for network administrators\. Your company’s network administrator usually is the person who selects the overall CIDR range for your AWS Control Tower organization\. The network administrator then allocates subnets from within that range for specific purposes\.

Here are some essential facts about AWS Control Tower and VPCs:
+ Each AWS Control Tower account is allowed one VPC\.
+ Every VPC has three Availability Zones\. By default, each Availability Zone contains one public subnet and two private subnets\. Therefore, each VPC contains nine subnets by default\.
+ Each of the nine subnets in your VPC is assigned a unique range, of equal size\.
+ Because the IP addresses do not overlap, the nine subnets within your VPC can communicate with each other in an unrestricted manner\.

The best practice for controlling communication among your VPC subnets, if needed, is to set up access control lists with rules that define the permitted traffic flow\. Use security groups for control of traffic among specific instances\.

The number of subnets in a VPC is configurable\. For more information about how to change your VPC subnet configuration, see [the Account Factory topic](https://docs.aws.amazon.com/controltower/latest/userguide/account-factory.html)\.

## CIDR and Peering for VPC and AWS Control Tower<a name="vpc-ct-cidr"></a>

When you choose a CIDR range for your VPC, AWS Control Tower applies *Carrier Grade NAT \(CGN\)* and Account Factory validates the IP address ranges according to the RFC 1918 specification\. Account Factory allows the IP ranges of `10.0.0.0/16`, `172.16.0.0/16`, and `192.168.0.0/16`\. If the range you specify is outside of that, AWS Control Tower provides an error message\.

When AWS Control Tower creates a VPC using the CIDR range you select, it assigns the identical CIDR range to your master account and to *every VPC* for every account you create within the organizational unit \(OU\)\. This implementation does not permit peering from any of your VPCs to any other VPC within your AWS Control Tower OU\.

Within each VPC, AWS Control Tower divides your specified CIDR range evenly into nine subnets\. None of the subnets within a VPC overlap\. Therefore, they all can communicate with each other\.

In summary, by default, subnet communication within the VPC is unrestricted, and VPC\-to\-VPC peering is not possible\.

The default CIDR range is `172.31.0.0/16`\.

## Options for VPC Peering in AWS Control Tower<a name="ct-peering-options"></a>

Instead of peering, AWS Control Tower offers [VPC endpoint services](https://docs.aws.amazon.com/vpc/latest/userguide/vpce-gateway.html) through [AWS PrivateLink](https://aws.amazon.com/privatelink) as the recommended solution for VPC peering among AWS Control Tower VPCs\. Packets can be sent directly from a specific IP address in one VPC to another specific IP address within another VPC\. 

However, another option is available\. Within AWS Control Tower, if you change the CIDR range in the settings of Account Factory, all new accounts that are subsequently created by AWS Control Tower \(using Account Factory\) are assigned the new CIDR range\. The old accounts are not updated\. For example, you can create an account, then change the CIDR range and create a new account, and the VPCs allocated to those two accounts can be peered\. Peering is possible because their IP address ranges are not identical\. For information about how to change account settings, see the [ Account Factory documentation](https://docs.aws.amazon.com/controltower/latest/userguide/account-factory.html#updating-account-factory-accounts) on updating an account\.

## Notes on VPC and CIDR<a name="bad-peering-options"></a>

Some network administrators may realize that it is possible to peer two subnets in two different VPCs \(that is, in two different accounts\) without changing the CIDR settings for accounts\. Because the nine subnets in a VPC do not overlap, peering technically is possible from *\[VPC1, subnet 1\]* to *\[VPC2, subnet 2\]*, for example\. However, this approach depends on an implementation detail of how subnet ranges are allocated within a VPC\. We don't recommend this method of peering, because it could fail at any time\.

When working with VPCs, AWS Control Tower makes no distinction at the Region level\. Every subnet is allocated from the exact CIDR range that you specify\. The VPC subnets can exist in any Region\.