# CIDR and Peering for VPC and AWS Control Tower<a name="vpc-ct-cidr"></a>

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