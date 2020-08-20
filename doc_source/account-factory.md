# Provision and manage accounts with Account Factory<a name="account-factory"></a>

This chapter includes an overview and procedures for provisioning new accounts in your AWS Control Tower landing zone\. AWS Control Tower provides three methods for creating member accounts:
+ through the Account Factory console that is part of AWS Service Catalog\.
+ through the **Enroll account** feature within AWS Control Tower\. 
+ from your AWS Control Tower landing zone's master account, using Lambda code and appropriate IAM roles\.

The standard way to provision accounts is through Account Factory, a console\-based product that's part of the AWS Service Catalog\. If your landing zone is not in a state of drift, you can use **Enroll account**\. Also, some customers may prefer to configure new accounts programmatically using IAM roles and Lambda functions\.

With the appropriate user group permissions, provisioners can specify standardized baselines and network configurations for all accounts in your organization\.

## Permissions for Configuring and Provisioning Accounts<a name="configure-provision-new-account"></a>

The AWS Control Tower account factory enables cloud administrators and AWS Single Sign\-On end users to provision accounts in your landing zone\. By default, AWS SSO users that provision accounts must be in the **AWSAccountFactory** group or the master group\. 

**Note**  
Exercise caution when working from the master account, as you would when using any account that has generous permissions across your organization\.

The AWS Control Tower master account has a trust relationship with the **AWSControlTowerExecution** role, which enables account setup from the master account, including some automated account setup\. For more information about the **AWSControlTowerExecution** role, see [How AWS Control Tower Works With Roles to Create and Manage Accounts ](how-control-tower-works.md#roles-how)\.

To enroll an existing AWS account into AWS Control Tower, that account must have the **AWSControlTowerExecution** role enabled\. For more information about how to enroll an existing account, see [Enroll an existing AWS account](enroll-account.md)\.

## Automated Account Provisioning With IAM Roles<a name="automated-provisioning"></a>

To configure Account Factory accounts in a more automated way, you can create Lambda functions in the AWS Control Tower master account, which [assumes the **AWSControlTowerExecution** role ](https://aws.amazon.com/premiumsupport/knowledge-center/lambda-function-assume-iam-role/) in the member account\. Then, using the role, the master account performs the desired configuration steps in each member account\.

 If you're provisioning accounts using Lambda functions, the identity that will perform this work must have the following IAM permissions policy, in addition to `AWSServiceCatalogEndUserFullAccess`\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Sid": "AWSControlTowerAccountFactoryAccess",
            "Effect": "Allow",
            "Action": [
                "sso:GetProfile",
                "sso:CreateProfile",
                "sso:UpdateProfile",
                "sso:AssociateProfile",
                "sso:CreateApplicationInstance",
                "sso:GetSSOStatus",
                "sso:GetTrust",
                "sso:CreateTrust",
                "sso:UpdateTrust",
                "sso:GetPeregrineStatus",
                "sso:GetApplicationInstance",
                "sso:ListDirectoryAssociations",
                "sso:ListPermissionSets",
                "sso:GetPermissionSet",
                "sso:ProvisionApplicationInstanceForAWSAccount",
                "sso:ProvisionApplicationProfileForAWSAccountInstance",
                "sso:ProvisionSAMLProvider",
                "sso:ListProfileAssociations",
                "sso-directory:ListMembersInGroup",
                "sso-directory:AddMemberToGroup",
                "sso-directory:SearchGroups",
                "sso-directory:SearchGroupsWithGroupName",
                "sso-directory:SearchUsers",
                "sso-directory:CreateUser",
                "sso-directory:DescribeGroups",
                "sso-directory:DescribeDirectory",
                "sso-directory:GetUserPoolInfo",
                "controltower:CreateManagedAccount",
                "controltower:DescribeManagedAccount",
                "controltower:DeregisterManagedAccount",
                "s3:GetObject",
                "organizations:describeOrganization",
                "sso:DescribeRegisteredRegions"
            ],
            "Resource": "*"
        }
    ]
}
```

## Create or Enroll An Individual Account<a name="quick-account-provisioning"></a>

The **Enroll account** feature is available in AWS Control Tower for provisioning new accounts in your landing zone and for enrolling existing AWS accounts so that they are governed by AWS Control Tower\.

The **Enroll account** capability is available when your landing zone is not in a state of [drift](https://docs.aws.amazon.com/controltower/latest/userguide/drift.html)\. To view this capability:
+ Navigate to the **Account Factory** page in AWS Control Tower\.
+ Select the **Enroll account** item near the top of the page\.
+ You'll then see a **Create account** section, where you can fill in the required fields: *account email*, *account name*, *SSO user name*, and *organizational unit*\.
+ When you've filled in the information, select **Enroll account**\.

You'll see a flashbar confirming that your account enrollment process has been successfully submitted\. If an error has occurred, AWS Control Tower may ask you for corrections\. The account provisioning process may take several minutes\.

**Note**  
If you are enrolling an existing AWS account, be sure to type the existing email address correctly\. Otherwise, a new account will be created\.

Certain errors may require that you refresh the page and try again\. If your landing zone is in a state of drift, you may not be able to use the **Enroll account** capability successfully\. You'll need to provision new accounts through AWS Service Catalog until your landing zone drift has been resolved\. 

When you enroll accounts, you must be signed into an account with an IAM user that has the `AWSServiceCatalogEndUserFullAccess` policy enabled, and you cannot be signed in as **Root**\.

Accounts that you enroll must be updated by means of the AWS Service Catalog and the AWS Control Tower account factory, as you would update any other account\. Update procedures are given in the section called [Updating and Moving Account Factory Accounts with AWS Service Catalog](#updating-account-factory-accounts)\. 

## Provisioning Account Factory Accounts With AWS Service Catalog<a name="provision-as-end-user"></a>

The following procedure describes how to provision accounts as an AWS SSO end user, through AWS Service Catalog\. This procedure also is referred to as *advanced account provisioning*\. We recommend using the **Enroll account** capability whenever possible\.

**To provision accounts in Account Factory as an end user**

1. Sign in from your user portal URL\.

1. From **Your applications**, choose **AWS Account**\.

1. From the list of accounts, choose the account ID for your master account\. This ID may also have a label, for example, **\(Master\)**\. 

1. From **AWSServiceCatalogEndUserAccess**, choose **Management console**\. This opens the AWS Management Console for this user in this account\.

1. Ensure that you've selected the correct AWS Region for provisioning accounts, which should be your AWS Control Tower home region\.

1. Search for and choose **Service Catalog** to open the AWS Service Catalog console\.

1. From the navigation pane, choose **Products list**\.

1. Select **AWS Control Tower Account Factory**, then choose the **Launch** button\. This selection starts the wizard to provision a new account\.

1. Fill in the information, and keep the following in mind:
   + The **SSOUserEmail** can be a new email address, or the email address associated with an existing AWS SSO user\. Whichever you choose, this user will have administrative access to the account you're provisioning\.
   + The **AccountEmail** must be an email address that isn't already associated with an AWS account\. If you used a new email address in **SSOUserEmail**, you can use that email address here\.

1. When you're finished, choose **Next** until you get to the **Review** page of the wizard\. Do not define **TagOptions** and do not enable **Notifications**, otherwise the account can fail to be provisioned\.

1. Review your account settings, and then choose **Launch**\. Do not create a resource plan, otherwise the account will fail to be provisioned\.

1. Your account is now being provisioned\. It can take a few minutes to complete\. You can refresh the page to update the displayed status information\.
**Note**  
Only one account can be provisioned at a time\.

## Tips on Managing Account Factory Accounts<a name="closing-and-repurposing"></a>

Accounts that you provision through the AWS Control Tower Account Factory can be updated, they can be closed, or they can be repurposed\. For example, you can repurpose existing accounts for other workloads and other users by updating the email addresses and user parameters for the account\.

If you specify a new SSO user email address when you update the provisioned product associated with an account that was vended by account factory, AWS Control Tower creates a new SSO user account\. The previously created user account is not removed\. If you prefer to remove the previous SSO user email from AWS SSO, see [Disabling a User](https://docs.aws.amazon.com/singlesignon/latest/userguide/disableuser.html)\.

With Account Factory you also can change the organizational unit \(OU\) for an account, or you can unmanage an account, by following the procedures in this chapter\. For more information on unmanaging an account, see [Unmanaging a Member Account](#unmanage-account)\. Certain updates require that you or an administrator must [Sign in as a Root User](best-practices.md#root-login) to the account, to gain appropriate permissions\.

## Updating and Moving Account Factory Accounts with AWS Service Catalog<a name="updating-account-factory-accounts"></a>

The following procedure guides you through how to update your Account Factory account or move it to a new OU, through AWS Service Catalog, by updating the provisioned product\. You can update accounts with the **Enroll account** function in AWS Control Tower\. We recommend using the **Enroll account** capability whenever possible\.

**To update an Account Factory account or change its OU**

1. Sign in to the AWS Management Console and open the AWS Single Sign\-On console at [https://console\.aws\.amazon\.com/singlesignon/](https://console.aws.amazon.com/singlesignon/)\.

   Alternatively, you can sign in to your AWS Control Tower master account\.
**Note**  
You must be signed in as a user with the permissions to provision new products in AWS Service Catalog; for example, an AWS SSO user in either the **AWSAccountFactory** or **AWSServiceCatalogAdmins** groups\.

1. On the Account Factory page in AWS Control Tower, choose **Enroll account** to open the AWS Service Catalog console and the Account Factory product\.

1. From the navigation pane, choose **Provisioned products list**\. 

1. For each account listed, perform the following steps to update all your member accounts:

   1. From the drop\-down menu for the account, choose **Provisioned product details**\.

   1. Make a note of the following parameters:
      + **SSOUserEmail** \(Available in provisioned product details\)
      + **AccountEmail** \(Available in provisioned product details\)
      + **SSOUserFirstName** \(Available in SSO\)
      + **SSOUSerLastName** \(Available in SSO\)
      + **AccountName** \(Available in SSO\)

   1. From **Actions**, choose **Update**\.

   1. Choose the button next to the **Version** of the product you want to update, and choose **Next**\.

   1. Provide the parameter values that were mentioned previously\.
      + If you want to keep the existing OU, for **ManagedOrganizationalUnit**, choose the OU that the account was already in\.
      + If you want to migrate the account to a new OU, for **ManagedOrganizationalUnit**, choose the new OU for the account\.

       A central cloud administrator can find this information in the AWS Control Tower console, under **Accounts**\.

   1. Choose **Next**\.

   1. Review your changes, and then choose **Update**\. This process can take a few minutes per account\.

## Configuring Account Factory with Amazon Virtual Private Cloud Settings<a name="configuring-account-factory-with-VPC-settings"></a>

Account Factory enables you to create pre\-approved baselines and configuration options for accounts in your organization\. You can configure and provision new accounts through AWS Service Catalog\.

On the Account Factory page, you can see a list of organizational units \(OUs\) and their **allow list** status\. By default, all OUs are on the allow list, which means that accounts can be provisioned under them\. You can disable certain OUs for account provisioning through AWS Service Catalog\.

You can view the Amazon VPC configuration options available to your end users when they provision new accounts\. 

**To configure Amazon VPC settings in Account Factory**

1. As a central cloud administrator, sign into the AWS Control Tower console with administrator permissions in the master account\.

1. From the left side of the dashboard, select **Account Factory** to navigate to the Account Factory network configuration page\. There you can see the default network settings displayed\. To edit, select **Edit** and view the editable version of your Account Factory network configuration settings\. 

1. You can modify the each field of the default settings as needed\. Choose the VPC configuration options you'd like to establish for all new Account Factory accounts that your end users may create, and enter your settings into the fields\. 
+ Choose **disabled** or **enabled** to create a public subnet in Amazon VPC\. By default, the internet\-accessible subnet is disallowed\.
**Note**  
If you set the account factory VPC configuration so that public subnets are **enabled** when provisioning a new account, account factory configures Amazon VPC to create a [NAT Gateway](https://docs.aws.amazon.com/vpc/latest/userguide/vpc-nat-gateway.html)\. You will be billed for your usage by Amazon VPC\. See [VPC Pricing](http://aws.amazon.com/vpc/pricing/) for more information\. 
+ Choose the maximum number of private subnets in Amazon VPC from the list\. By default, 1 is selected\. The maximum number of private subnets allowed is 2\.
+  Enter the range of IP addresses for creating your account VPCs\. The value must be in the form of a classless inter\-domain routing \(CIDR\) block \(for example, the default is `172.31.0.0/16`\)\. This CIDR block provides the overall range of subnet IP addresses for the VPC that Account Factory creates for your account\. Within your VPC, subnets are assigned automatically from the range you specify, and they are equal in size\. By default, subnets within your VPC do not overlap\. However, subnet IP address ranges in the VPCs of all your provisioned accounts could overlap\.
+ Choose a region or all the regions for creating a VPC when an account is provisioned\. By default all available regions are selected\.
+ From the list, choose the number of Availability Zones to configure subnets for in each VPC\. The default and recommended number is 3\.
+ Choose **Save**\.

 You can set up these configuration options to create new accounts that don't include a VPC\. See the [walkthrough](https://docs.aws.amazon.com/controltower/latest/userguide/configure-without-vpc.html)\.

## Unmanaging a Member Account<a name="unmanage-account"></a>

If you created an account in Account Factory that you no longer want to be managed by AWS Control Tower in a landing zone, you can unmanage the account\. This can be done in the AWS Service Catalog console by an AWS SSO user in the **AWSAccountFactory** group\. For more information on AWS SSO users or groups, see [Managing Users and Access Through AWS Single Sign\-On](sso.md)\. The following procedure describes how to unmanage a member account\.

**To unmanage a member account**

1. Open the AWS Service Catalog console in your web browser at [https://console.aws.amazon.com/servicecatalog](https://console.aws.amazon.com/servicecatalog)\.

1. From the left navigation pane, choose **Provisioned products list**\.

1. From the list of provisioned accounts, choose the name of the account that you want AWS Control Tower to no longer manage\.

1. On the **Provisioned product details** page, from the **Actions** menu, choose **Terminate**\.

1. From the dialog box that appears, choose **Terminate**\.
**Important**  
The word *terminate* is specific to AWS Service Catalog\. When you terminate an Account Factory account in AWS Service Catalog, the account is not closed\. This action removes the account from its OU and your landing zone\.

1. The **Deregistering Managed Account** message displays\.

1. To update the displayed account status, refresh the page\. When the account has been unmanaged, its status changes to **terminated**\.

1. If you no longer need the terminated account, close it\. For information about closing AWS accounts, see [Closing an Account](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/close-account.html) in the *AWS Billing and Cost Management User Guide*

**Note**  
An unmanaged \(terminated\) account is not closed or deleted\. When the account has been unmanaged, the AWS SSO user that you selected when you created the account in Account Factory still has administrative access to the account\. If you do not want this user to have administrative access, you must change this setting in AWS SSO by updating the account in Account Factory and changing the AWS SSO user email address for the account\. For more information, see [Updating and Moving Account Factory Accounts with AWS Service Catalog](#updating-account-factory-accounts)\.

## Closing an Account Created in Account Factory<a name="delete-account"></a>

Accounts created in Account Factory are AWS accounts\. For information about closing AWS accounts, see [Closing an Account](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/close-account.html) in the *AWS Billing and Cost Management User Guide*\.

## Resource Considerations for Account Factory<a name="account-factory-considerations"></a>

When an account is provisioned with Account Factory, the following AWS resources are created within the account\.


| AWS service | Resource type | Resource name | 
| --- | --- | --- | 
| AWS CloudFormation | Stacks |  StackSet\-AWSControlTowerBP\-BASELINE\-CLOUDTRAIL\-\* StackSet\-AWSControlTowerBP\-BASELINE\-CLOUDWATCH\-\* StackSet\-AWSControlTowerBP\-BASELINE\-CONFIG\-\* StackSet\-AWSControlTowerBP\-BASELINE\-ROLES\-\* StackSet\-AWSControlTowerBP\-BASELINE\-SERVICE\-ROLES\-\*  | 
| AWS CloudTrail | Trail | aws\-controltower\-BaselineCloudTrail | 
| Amazon CloudWatch | CloudWatch Event Rules | aws\-controltower\-ConfigComplianceChangeEventRule | 
| Amazon CloudWatch | CloudWatch Logs | aws\-controltower/CloudTrailLogs /aws/lambda/aws\-controltower\-NotificationForwarder | 
| AWS Identity and Access Management | Roles | aws\-controltower\-AdministratorExecutionRole aws\-controltower\-CloudWatchLogsRole aws\-controltower\-ConfigRecorderRole aws\-controltower\-ForwardSnsNotificationRole aws\-controltower\-ReadOnlyExecutionRole  AWSControlTowerExecution | 
| AWS Identity and Access Management | Policies | AWSControlTowerServiceRolePolicy  | 
| Amazon Simple Notification Service | Topics | aws\-controltower\-SecurityNotifications | 
| AWS Lambda | Applications | StackSet\-AWSControlTowerBP\-BASELINE\-CLOUDWATCH\-\* | 
| AWS Lambda | Functions | aws\-controltower\-NotificationForwarder | 

## Guardrails and Account Resources<a name="guardrails-and-resources"></a>

Accounts created through the Account Factory in AWS Control Tower inherit the guardrails of the parent OU, and the associated resources are created\.

**Note**  
Accounts created outside of AWS Control Tower won't inherit guardrails from the parent OU in AWS Control Tower\. However, these unenrolled accounts *are* displayed in AWS Control Tower\.

When you enable guardrails with strongly recommended guidance, AWS Control Tower creates and manages certain additional AWS resources in your accounts\. Do not modify or delete resources created by AWS Control Tower\. Doing so could result in the guardrails entering an unknown state\. For more information, see [Guardrail Reference](guardrails-reference.md)\.