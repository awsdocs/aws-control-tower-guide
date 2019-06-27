# Managing Users and Access Through AWS Single Sign\-On<a name="sso"></a>

AWS Single Sign\-On is a cloud\-based service that simplifies managing SSO access to AWS accounts and business applications\. You can control SSO access and user permissions across all your AWS accounts in AWS Organizations\. You can also administer access to popular business applications and custom applications that support Security Assertion Markup Language \(SAML\) 2\.0\. In addition, AWS SSO offers a user portal where your users can find all their assigned AWS accounts, business applications, and custom applications in one place\. For more information, see *[AWS Single Sign\-On User Guide](https://docs.aws.amazon.com/singlesignon/latest/userguide/)*\.

In AWS Control Tower, AWS Single Sign\-On allows both central cloud administrators and end users to manage SSO access to multiple AWS accounts and business applications\. AWS Control Tower uses this service for the creation and management of user access to the accounts created in AWS Service Catalog\.

**Note**  
When you first set up AWS Control Tower, only the root user and any IAM users with the correct permissions can add AWS SSO users\. However, once end users have been added in the **AWSAccountFactory** group, they can create new SSO users from the Account Factory wizard\. For more information, see [Account Factory](account-factory.md)\.

Your landing zone is set up with a directory to manage user identities and single sign\-on to provide your users with federated access across accounts\. When you set up your landing zone, you have a default directory\. This directory is preconfigured with user groups and permission sets\.

The groups are designed for you to easily manage specialized roles within your shared accounts\. You can create new groups for your end users in your member accounts\. The permission sets available cover a broad range of distinct user permission use cases like read\-only access, AWS Control Tower admin access, and AWS Service Catalog access\. These permission sets enable your end users to quickly provision their own AWS accounts in your landing zone\.

For more information on how to use this service in the context of AWS Control Tower, see the following topics in the *AWS Single Sign\-On User Guide*\.
+ To add users, see [Add Users](https://docs.aws.amazon.com/singlesignon/latest/userguide/addusers.html)\. 
+ To add users to groups, see [Add Users to Groups](https://docs.aws.amazon.com/singlesignon/latest/userguide/adduserstogroups.html)\. 
+ To edit user properties, see [Edit User Properties](https://docs.aws.amazon.com/singlesignon/latest/userguide/edituser.html)\.
+ To add group, see [Add Groups](https://docs.aws.amazon.com/singlesignon/latest/userguide/addgroups.html)\. 

**Warning**  
When using AWS Control Tower, your AWS SSO directory is in US East \(N\. Virginia\)\. If you set up your landing zone in another Region and then navigate to the AWS SSO console, you must change the Region to the US East \(N\. Virginia\)\. Do not delete your AWS SSO configuration in US East \(N\. Virginia\)\.

## AWS SSO Groups for AWS Control Tower<a name="sso-groups"></a>

AWS Control Tower offers preconfigured groups to organize users that perform specific tasks in your accounts\. You can add users and assign them to these groups directly in AWS SSO\. Doing so matches permission sets to users in groups within your accounts\. The groups created when you set up your landing zone are as follows\.


**AWSAccountFactory**  

| Account | Permission sets | Description | 
| --- | --- | --- | 
| Master account | AWSServiceCatalogEndUserAccess | This group is only used in this account to provision new accounts using Account Factory\. | 


**AWSServiceCatalogAdmins**  

| Account | Permission sets | Description | 
| --- | --- | --- | 
| Master account | AWSServiceCatalogAdminFullAccess | This group is only used in this account to make administrative changes to Account Factory\. Users in this group can't provision new accounts unless they're also in the AWSAccountFactory group\. | 


**AWSControlTowerAdmins**  

| Account | Permission sets | Description | 
| --- | --- | --- | 
| Master account | AWSAdministratorAccess | Users of this group in this account are the only ones that can access the AWS Control Tower console\. | 
| Log archive account | AWSAdministratorAccess | Users will have administrator access in this account\. | 
| Audit account | AWSAdministratorAccess | Users will have administrator access in this account\. | 
| Member accounts | AWSOrganizationsFullAccess | Users will have full access to Organizations in this account\. | 


**AWSSecurityAuditPowerUsers**  

| Account | Permission sets | Description | 
| --- | --- | --- | 
| Master account | AWSPowerUserAccess | Users can perform application development tasks and can create and configure resources and services that support AWS aware application development\. | 
| Log archive account | AWSPowerUserAccess | Users can perform application development tasks and can create and configure resources and services that support AWS aware application development\. | 
| Audit account | AWSPowerUserAccess | Users can perform application development tasks and can create and configure resources and services that support AWS aware application development\. | 
| Member accounts | AWSPowerUserAccess | Users can perform application development tasks and can create and configure resources and services that support AWS aware application development\. | 


**AWSSecurityAuditors**  

| Account | Permission sets | Description | 
| --- | --- | --- | 
| Master account | AWSReadOnlyAccess | Users have read\-only access to all AWS services and resources in this account\. | 
| Log archive account | AWSReadOnlyAccess | Users have read\-only access to all AWS services and resources in this account\. | 
| Audit account | AWSReadOnlyAccess | Users have read\-only access to all AWS services and resources in this account\. | 
| Member accounts | AWSReadOnlyAccess | Users have read\-only access to all AWS services and resources in this account\. | 


**AWSLogArchiveAdmins**  

| Account | Permission sets | Description | 
| --- | --- | --- | 
| Log archive account | AWSAdministratorAccess | Users will have administrator access in this account\. | 


**AWSLogArchiveViewers**  

| Account | Permission sets | Description | 
| --- | --- | --- | 
| Log archive account | AWSReadOnlyAccess | Users have read\-only access to all AWS services and resources in this account\. | 


**AWSAuditAccountAdmins**  

| Account | Permission sets | Description | 
| --- | --- | --- | 
| Audit account | AWSAdministratorAccess | Users will have administrator access in this account\. | 