# Account Factory<a name="account-factory"></a>

This chapter includes an overview and procedures for Account Factory, the AWS Service Catalog console\-based product used to provision new accounts in your landing zone\.

## Configuring and Provisioning Accounts Through AWS Service Catalog<a name="configure-provision-new-account"></a>

With Account Factory, central cloud administrators and AWS Single Sign\-On end users can provision accounts in your landing zone\. By default, AWS SSO users that do this work must be in the **AWSAccountFactory** or **AWSServiceCatalogAdmins** groups\. However, if you're provisioning accounts programmatically, the identity that will perform this work must have the following IAM permissions policy, in addition to `AWSServiceCatalogEndUserFullAccess`\.

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
                "s3:GetObject"
            ],
            "Resource": "*"
        }
    ]
}
```

For more information on using AWS SSO with AWS Control Tower, see [Managing Users and Access Through AWS Single Sign\-On](sso.md)\. The following procedure describes how to provision accounts as an AWS SSO end user\.

**To provision accounts in Account Factory as an end user**

1. Sign in your user portal URL\.

1. From **Your applications**, choose **AWS Account**\.

1. From the list of accounts, choose the account ID for your master account\. It also has the label, **\(Master\)**\.

1. From **AWSServiceCatalogEndUserAccess**, choose **Management console**\. This opens the AWS Management Console for this user in this account\.

1. Search for and choose **Service Catalog** to open the AWS Service Catalog console\.

1. From the navigation pane, choose **Products list**\.

1. From **AWS Control Tower Account Factory**, choose the drop\-down menu and select **Launch product**\. This starts the wizard to provision a new account\.

1. Fill in the information, and keep the following in mind:
   + The **SSOUserEmail** can be a new email address, or the email address associated with an existing AWS SSO user\. Whichever you choose, this user will have administrative access to the account you're provisioning\.
   + The **AccountEmail** must be an email address that isn't already associated with an AWS account, and it must be in the same domain as the email addresses used in your shared accounts\. If you used a new email address in **SSOUserEmail**, you can use that email address here\.

1. When you're finished, choose **NEXT** until you get to the **Review** page of the wizard\. Do not define **TagOptions** and do not enable **Notifications**, otherwise the account can fail to be provisioned\.

1. Review your account settings, and then choose **LAUNCH**\. Do not create a resource plan, otherwise the account will fail to be provisioned\.

1. Your account is now being provisioned\. It can take a few minutes to complete\. You can refresh the page to update the displayed status information\.

Accounts that you provision can be unmanaged or closed\. You can also repurpose accounts for other workloads and users by updating the email addresses for the account\. For more information on unmanaging an account, see [Unmanaging a Member Account](#unmanage-account)\.

### Updating Account Factory Accounts<a name="updating-account-factory-accounts"></a>

The following procedure guides you through how to update your Account Factory accounts\.

**To update an Account Factory account**

1. Sign in to the AWS Management Console and open the AWS Single Sign\-On console at [https://console\.aws\.amazon\.com/singlesignon/](https://console.aws.amazon.com/singlesignon/)\.
**Note**  
You must be signed in as a user with the permissions to provision new products in AWS Service Catalog; for example, an AWS SSO user in either the **AWSAccountFactory** or **AWSServiceCatalogAdmins** groups\.

1. Choose **Provision new account** to open the AWS Service Catalog console and the Account Factory product\.

1. From the navigation pane, choose **Provisioned products list**\.

1. For each account listed, perform the following steps to update all your member accounts:

   1. From the drop\-down menu for the account, choose **Provisioned product details**\.

   1. Make a note of the following parameters:
      + **SSOUserEmail**
      + **AccountEmail**
      + **SSOUserFirstName**
      + **SSOUSerLastName**
      + **AccountName**

   1. From **ACTIONS**, choose **Update**\.

   1. Choose the button next to the **Version** of the product you want to update, and choose **NEXT**\.

   1. Provide the parameter values that were mentioned previously\. For **ManagedOrganizationlUnit**, choose the OU that the account was already in\. A central cloud administrator can find this information in the AWS Control Tower console, under **Accounts**\.

   1. Choose **NEXT**\.

   1. Review your changes, and then choose **UPDATE**\. This process can take a few minutes per account\.

### Configuring Account Factory with Amazon Virtual Private Cloud Settings<a name="configuring-account-factory-with-VPC-settings"></a>

Account Factory enables you to create pre\-approved baselines and configuration options for accounts in your organization\. You can configure and provision new accounts through AWS Service Catalog\.

On the Account Factory page, you can view the Amazon VPC configuration options available to your end users when they provision new accounts\. You can see a list of organizational units \(OUs\) with their allow list status\. By default, all OUs are on the allow list for accounts to be provisioned under them\. You can disable certain OUs for account provisioning through AWS Service Catalog\.

**To configure Amazon VPC settings in Account Factory**

1. As a central cloud administrator, sign into the AWS Control Tower console with administrator permissions in the master account\.

1. To modify these settings, choose **Edit**\.

1. On the Edit Account Factory network configuration page, you can edit your Account Factory network configuration settings\. Choose the appropriate VPC configuration options for new accounts\. 

1. Choose **disabled** or **enabled** to create a public subnet in Amazon VPC\. By default, the internet\-accessible subnet is disallowed\.

1. Choose the maximum number of private subnets in Amazon VPC from the list\. By default, 0 is selected\. The allowed maximum number of private subnets are 2\.

1. Enter the range of addresses for creating your account VPCs\. The value must be in the form of classless inter\-domain routing \(CIDR\) block \(for example, `10.0.0.0/16`\)\. This is the primary CIDR block for your VPC\.

1. Choose a Region or all the Regions for creating a VPC when an account is provisioned\.

1. From the list, choose the number of Availability Zones to configure subnets for in each VPC\.

1. Choose **Save**\.

### Unmanaging a Member Account<a name="unmanage-account"></a>

If you created an account in Account Factory that you no longer want to be managed by AWS Control Tower in a landing zone, you can unmanage the account\. This can be done in the AWS Service Catalog console by an AWS SSO user in either the **AWSAccountFactory** or **AWSServiceCatalogAdmins** groups\. For more information on AWS SSO users or groups, see [Managing Users and Access Through AWS Single Sign\-On](sso.md)\. The following procedure describes how to unmanage a member account\.

**To unmanage a member account**

1. Open the AWS Service Catalog console in your web browser at [https://console.aws.amazon.com/servicecatalog](https://console.aws.amazon.com/servicecatalog)\.

1. From the left navigation pane, choose **Provisioned products list**\.

1. From the list of provisioned accounts, choose the name of the account that you want AWS Control Tower to no longer manage\.

1. On the **Provisioned product details** page, from the **ACTIONS** menu, choose **Terminate**\.

1. From the dialog box that appears, choose **TERMINATE**\.
**Important**  
The word *terminate* is specific to AWS Service Catalog\. When you terminate an Account Factory account in AWS Service Catalog, the account is not closed\. This action removes the account from its OU and your landing zone\.

1. The **Deregistering Managed Account** message displays\.

1. To update the displayed account status, refresh the page\. When the account has been unmanaged, its status changes to **terminated**\.

**Note**  
When the account has been unmanaged, the AWS SSO user that you selected when you created the account in Account Factory still has administrative access to the account\. If you do not want this user to have administrative access, you must change this setting in AWS SSO by updating the account in Account Factory and changing the AWS SSO user email address for the account\. For more information, see [Updating Account Factory Accounts](#updating-account-factory-accounts)\.

### Closing an Account Created in Account Factory<a name="delete-account"></a>

Accounts created in Account Factory are AWS accounts\. For information about closing AWS accounts, see [Closing an Account](https://docs.aws.amazon.com/awsaccountbilling/latest/aboutv2/close-account.html) in the *AWS Billing and Cost Management User Guide*\.

## Considerations for Account Factory<a name="account-factory-considerations"></a>

Accounts created through the Account Factory in AWS Control Tower inherit the guardrails of the parent OU\. Accounts created outside of AWS Control Tower won't inherit these guardrails\. These accounts also do not display in AWS Control Tower\.

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

When you enable guardrails with strongly recommended guidance, AWS Control Tower managed AWS resources are created in your accounts\. Do not modify or delete resources created by AWS Control Tower\. Doing so could result in the guardrails entering an unknown state\. For more information, see [Guardrail Reference](guardrails-reference.md)\.