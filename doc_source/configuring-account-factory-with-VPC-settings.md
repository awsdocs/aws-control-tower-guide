# Configure Account Factory with Amazon Virtual Private Cloud settings<a name="configuring-account-factory-with-VPC-settings"></a>

Account Factory allows you to create pre\-approved baselines and configuration options for accounts in your organization\. You can configure and provision new accounts through AWS Service Catalog\.

On the Account Factory page, you can see a list of organizational units \(OUs\) and their **allow list** status\. By default, all OUs are on the allow list, which means that accounts can be provisioned under them\. You can disable certain OUs for account provisioning through AWS Service Catalog\.

You can view the Amazon VPC configuration options available to your end users when they provision new accounts\. 

**To configure Amazon VPC settings in Account Factory**

1. As a central cloud administrator, sign into the AWS Control Tower console with administrator permissions in the management account\.

1. From the left side of the dashboard, select **Account Factory** to navigate to the Account Factory network configuration page\. There you can see the default network settings displayed\. To edit, select **Edit** and view the editable version of your Account Factory network configuration settings\. 

1. You can modify each field of the default settings as needed\. Choose the VPC configuration options you'd like to establish for all new Account Factory accounts that your end users may create, and enter your settings into the fields\. 
+ Choose **disabled** or **enabled** to create a public subnet in Amazon VPC\. By default, the internet\-accessible subnet is disallowed\.
**Note**  
If you set the account factory VPC configuration so that public subnets are **enabled** when provisioning a new account, account factory configures Amazon VPC to create a [NAT Gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html)\. You will be billed for your usage by Amazon VPC\. See [VPC Pricing](http://aws.amazon.com/vpc/pricing/) for more information\. 
+ Choose the maximum number of private subnets in Amazon VPC from the list\. By default, 1 is selected\. The maximum number of private subnets allowed is 2 per availability zone\.
+  Enter the range of IP addresses for creating your account VPCs\. The value must be in the form of a classless inter\-domain routing \(CIDR\) block \(for example, the default is `172.31.0.0/16`\)\. This CIDR block provides the overall range of subnet IP addresses for the VPC that Account Factory creates for your account\. Within your VPC, subnets are assigned automatically from the range you specify, and they are equal in size\. By default, subnets within your VPC do not overlap\. However, subnet IP address ranges in the VPCs of all your provisioned accounts could overlap\.
+ Choose a region or all the regions for creating a VPC when an account is provisioned\. By default all available regions are selected\.
+ From the list, choose the number of Availability Zones to configure subnets for in each VPC\. The default and recommended number is 3\.
+ Choose **Save**\.

 You can set up these configuration options to create new accounts that don't include a VPC\. See the [walkthrough](https://docs.aws.amazon.com/controltower/latest/userguide/configure-without-vpc.html)\.