# Manage Users and Access Through AWS IAM Identity Center \(successor to AWS Single Sign\-On\)<a name="sso"></a>

AWS IAM Identity Center \(successor to AWS Single Sign\-On\) is a cloud\-based service that simplifies how you manage IAM Identity Center access to AWS accounts and business applications\. You can control IAM Identity Center access and user permissions across all your AWS accounts in AWS Organizations\. You also can administer access to popular business applications and custom applications that support Security Assertion Markup Language \(SAML\) 2\.0\. Also, IAM Identity Center offers a user portal where your users can find all their assigned AWS accounts, business applications, and custom applications in one place\. For more information, see *[AWS IAM Identity Center \(successor to AWS Single Sign\-On\) User Guide](https://docs.aws.amazon.com/singlesignon/latest/userguide/)*\.

**Working With AWS IAM Identity Center and AWS Control Tower**

In AWS Control Tower, AWS IAM Identity Center \(successor to AWS Single Sign\-On\) allows central cloud administrators and end users to manage access to multiple AWS accounts and business applications\. AWS Control Tower uses this service to set up and manage access to the accounts created through Service Catalog\.

## <a name="sso-video"></a>

For a brief tutorial about how to set up your IAM Identity Center users and permissions in AWS Control Tower, you can view this video \(6:23\)\. For better viewing, select the icon at the lower right corner of the video to enlarge it to full screen\. Captioning is available\.

**About setting up AWS Control Tower with IAM Identity Center **

When you initially set up AWS Control Tower, only the root user user and any IAM users with the correct permissions can add IAM Identity Center users\. However, after end users have been added in the **AWSAccountFactory** group, they can create new IAM Identity Center users from the Account Factory wizard\. For more information, see [Provision and manage accounts with Account Factory](account-factory.md)\.

Your landing zone is set up with a preconfigured directory that helps you manage user identities and single sign\-on, so that your users have federated access across accounts\. When you set up your landing zone, this default directory is created to contain *user groups* and *permission sets*\.

**Note**  
You can delegate administration of AWS IAM Identity Center \(successor to AWS Single Sign\-On\) in your organization to an account other than the management account\. For more information, see this blog post, entitled, [Getting started with AWS SSO delegated administration](http://aws.amazon.com/blogs/security/getting-started-with-aws-sso-delegated-administration/)

**User Groups, Roles, and Permission Sets**

User groups manage specialized *roles* that are defined within your shared accounts\. Roles establish sets of permissions that belong together\. All members of a group inherit the permission sets, or roles, associated with the group\. You can create new groups for the end users of your member accounts, so that you can custom\-assign only the roles that are needed for the specific tasks a group performs\. 

The permission sets available cover a broad range of distinct user permission requirements, such as read\-only access, AWS Control Tower administrative access, and Service Catalog access\. These permission sets enable your end users to provision their own AWS accounts in your landing zone quickly, and in compliance with your enterprise's guidelines\.

For tips on planning your allocations of users, groups, and permissions, refer to [ Recommendations for setting up groups, roles, and policies ](roles-recommendations.md)

For more information on how to use this service in the context of AWS Control Tower, see the following topics in the *AWS IAM Identity Center \(successor to AWS Single Sign\-On\) User Guide*\.
+ To add users, see [Add Users](https://docs.aws.amazon.com/singlesignon/latest/userguide/addusers.html)\. 
+ To add users to groups, see [Add Users to Groups](https://docs.aws.amazon.com/singlesignon/latest/userguide/adduserstogroups.html)\. 
+ To edit user properties, see [Edit User Properties](https://docs.aws.amazon.com/singlesignon/latest/userguide/edituser.html)\.
+ To add a group, see [Add Groups](https://docs.aws.amazon.com/singlesignon/latest/userguide/addgroups.html)\. 

**Warning**  
AWS Control Tower sets up your IAM Identity Center directory in your home region\. If you set up your landing zone in another Region and then navigate to the IAM Identity Center console, you must change the Region to your home region\. Do not delete your IAM Identity Center configuration in your home region\.

## Things to Know About IAM Identity Center Accounts and AWS Control Tower<a name="sso-good-to-know"></a>

Here are some good things to know when working with IAM Identity Center user accounts in AWS Control Tower\.
+ If your AWS IAM Identity Center user account is disabled, you'll get an error message when trying to provision new accounts in Account Factory\. You can re\-enable your IAM Identity Center user in the IAM Identity Center console\.
+ If you specify a new IAM Identity Center user email address when you update the provisioned product associated with an account that was vended by Account Factory, AWS Control Tower creates a new IAM Identity Center user account\. The previously created user account is not removed\. If you prefer to remove the previous IAM Identity Center user email address from AWS IAM Identity Center, see [Disabling a User](https://docs.aws.amazon.com/singlesignon/latest/userguide/disableuser.html)\.
+ AWS IAM Identity Center has been [integrated with Azure Active Directory](http://aws.amazon.com/blogs/aws/the-next-evolution-in-aws-single-sign-on/), and you can connect your existing Azure Active Directory to AWS Control Tower\.
+ For more information about how the behavior of AWS Control Tower interacts with AWS IAM Identity Center and different identity sources, refer to the [Considerations for Changing Your Identity Source](https://docs.aws.amazon.com/singlesignon/latest/userguide/manage-your-identity-source-considerations.html) in the AWS IAM Identity Center documentation\.

## IAM Identity Center Groups for AWS Control Tower<a name="sso-groups"></a>

AWS Control Tower offers preconfigured groups to organize users that perform specific tasks in your accounts\. You can add users and assign them to these groups directly in IAM Identity Center\. Doing so matches permission sets to users in groups within your accounts\. The groups created when you set up your landing zone are as follows\.


**AWSAccountFactory**  

| Account | Permission sets | Description | 
| --- | --- | --- | 
| Management account | AWSServiceCatalogEndUserAccess | This group is only used in this account to provision new accounts using Account Factory\. | 


**AWSServiceCatalogAdmins**  

| Account | Permission sets | Description | 
| --- | --- | --- | 
| Management account | AWSServiceCatalogAdminFullAccess | This group is only used in this account to make administrative changes to Account Factory\. Users in this group can't provision new accounts unless they're also in the AWSAccountFactory group\. | 


**AWSControlTowerAdmins**  

| Account | Permission sets | Description | 
| --- | --- | --- | 
| Management account | AWSAdministratorAccess | Users of this group in this account are the only ones that have access to the AWS Control Tower console\. | 
| Log archive account | AWSAdministratorAccess | Users have administrator access in this account\. | 
| Audit account | AWSAdministratorAccess | Users have administrator access in this account\. | 
| Member accounts | AWSOrganizationsFullAccess | Users have full access to Organizations in this account\. | 


**AWSSecurityAuditPowerUsers**  

| Account | Permission sets | Description | 
| --- | --- | --- | 
| Management account | AWSPowerUserAccess | Users can perform application development tasks and can create and configure resources and services that support AWS aware application development\. | 
| Log archive account | AWSPowerUserAccess | Users can perform application development tasks and can create and configure resources and services that support AWS aware application development\. | 
| Audit account | AWSPowerUserAccess | Users can perform application development tasks and can create and configure resources and services that support AWS aware application development\. | 
| Member accounts | AWSPowerUserAccess | Users can perform application development tasks and can create and configure resources and services that support AWS aware application development\. | 


**AWSSecurityAuditors**  

| Account | Permission sets | Description | 
| --- | --- | --- | 
| Management account | AWSReadOnlyAccess | Users have read\-only access to all AWS services and resources in this account\. | 
| Log archive account | AWSReadOnlyAccess | Users have read\-only access to all AWS services and resources in this account\. | 
| Audit account | AWSReadOnlyAccess | Users have read\-only access to all AWS services and resources in this account\. | 
| Member accounts | AWSReadOnlyAccess | Users have read\-only access to all AWS services and resources in this account\. | 


**AWSLogArchiveAdmins**  

| Account | Permission sets | Description | 
| --- | --- | --- | 
| Log archive account | AWSAdministratorAccess | Users have administrator access in this account\. | 


**AWSLogArchiveViewers**  

| Account | Permission sets | Description | 
| --- | --- | --- | 
| Log archive account | AWSReadOnlyAccess | Users have read\-only access to all AWS services and resources in this account\. | 


**AWSAuditAccountAdmins**  

| Account | Permission sets | Description | 
| --- | --- | --- | 
| Audit account | AWSAdministratorAccess | Users have administrator access in this account\. | 