# Manage access to resources<a name="access-control-manage-access-intro"></a>

A *permissions policy* describes who has access to what\. The following section explains the available options for creating permissions policies\.

**Note**  
This section discusses using IAM in the context of AWS Control Tower\. It doesn't provide detailed information about the IAM service\. For complete IAM documentation, see [What Is IAM?](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) in the *IAM User Guide*\. For information about IAM policy syntax and descriptions, see [AWS IAM Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

Policies attached to an IAM identity are referred to as *identity\-based* policies \(IAM polices\)\. Policies attached to a resource are referred to as *resource\-based* policies\.

**Note**  
 AWS Control Tower supports only identity\-based policies \(IAM policies\)\. 

**Topics**
+ [About identity\-based policies \(IAM policies\)](#access-control-manage-access-intro-iam-policies)
+ [Create roles and assign permissions](#assign-permissions)
+ [Resource\-based policies](#access-control-manage-access-intro-resource-policies)

## About identity\-based policies \(IAM policies\)<a name="access-control-manage-access-intro-iam-policies"></a>

You can attach policies to IAM identities\. For example, you can do the following:
+ **Attach a permissions policy to a user or a group in your account** – To grant a user permissions to create an AWS Control Tower resource, such as setting up a landing zone, you can attach a permissions policy to a user or group that the user belongs to\.
+  **Attach a permissions policy to a role \(grant cross\-account permissions\)** – You can attach an identity\-based permissions policy to an IAM role to grant cross\-account permissions\. For example, an administrator for one AWS account \(*Account A*\) can create a role that grants cross\-account permissions to another AWS account \(*Account B*\), or the administrator can create a role that grants permissions to another AWS service\.

  1. The Account A administrator creates an IAM role and attaches a permissions policy to the role that grants permissions to manage resources in Account A\.

  1. The Account A administrator attaches a trust policy to the role\. The policy identifies Account B as the principal who can assume the role\.

  1. As principal, the Account B administrator can give any user in Account B permission to assume the role\. By assuming the role, users in Account B can create or gain access to resources in Account A\.

  1. To grant an AWS service the ability \(permissions\) to assume the role, the principal that you specify in the trust policy can be an AWS service\.

## Create roles and assign permissions<a name="assign-permissions"></a>

Roles and permissions give you access to resources, in AWS Control Tower and in other AWS services, including programmatic access to resources\.

To provide access, add permissions to your users, groups, or roles:
+ Users and groups in AWS IAM Identity Center \(successor to AWS Single Sign\-On\):

  Create a permission set\. Follow the instructions in [Create a permission set](https://docs.aws.amazon.com/singlesignon/latest/userguide/howtocreatepermissionset.html) in the *AWS IAM Identity Center \(successor to AWS Single Sign\-On\) User Guide*\.
+ Users managed in IAM through an identity provider:

  Create a role for identity federation\. Follow the instructions in [Creating a role for a third\-party identity provider \(federation\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-idp.html) in the *IAM User Guide*\.
+ IAM users:
  + Create a role that your user can assume\. Follow the instructions in [Creating a role for an IAM user](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_create_for-user.html) in the *IAM User Guide*\.
  + \(Not recommended\) Attach a policy directly to a user or add a user to a user group\. Follow the instructions in [Adding permissions to a user \(console\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_users_change-permissions.html#users_change_permissions-add-console) in the *IAM User Guide*\.

For more information about using IAM to delegate permissions, see [Access Management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) in the *IAM User Guide*\.

**Note**  
When setting up an AWS Control Tower landing zone, you'll need a user or role with the **AdministratorAccess** managed policy\. \(arn:aws:iam::aws:policy/AdministratorAccess\)

**To create a role for an AWS service \(IAM console\)**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane of the IAM console, choose **Roles**, and then choose **Create role**\.

1. Choose the **AWS service** role type\.

1. Choose the use case for your service\. Use cases are defined by the service to include the trust policy that the service requires\.

1. Choose **Next**\.

1. If possible, select the policy to use for the permissions policy\. Otherwise, choose **Create policy** to open a new browser tab and create a new policy from scratch\. For more information, see [Creating IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html#access_policies_create-start) in the *IAM User Guide*\.

1. After you create the policy, close that tab and return to your original tab\. Select the check box next to the permissions policies that you want the service to have\.

   Depending on the use case that you selected, the service might let you do any of the following:
   + Nothing, because the service defines the permissions for the role\.
   + Choose from a limited set of permissions\.
   + Choose from any permissions\.
   + Select no policies at this time\. However, you can create the policies later, and then attach them to the role\.

1. \(Optional\) Set a [permissions boundary](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_boundaries.html)\. This is an advanced feature that is available for service roles, but not for service\-linked roles\. 

   Expand the **Permissions boundary** section and choose **Use a permissions boundary to control the maximum role permissions**\. IAM includes a list of the AWS managed and customer managed policies in your account\. Select the policy to use for the permissions boundary or choose **Create policy** to open a new browser tab and create a new policy from scratch\. For more information, see [Creating IAM policies](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_create.html#access_policies_create-start) in the *IAM User Guide*\. After you create the policy, close that tab and return to your original tab to select the policy to use for the permissions boundary\.

1. Choose **Next**\.

1. For **Role name**, the degree of role name customization is defined by the service\. If the service defines the role's name, this option is not editable\. In other cases, the service might define a prefix for the role and let you enter an optional suffix\. Some services let you specify the entire name of your role\.

   If possible, enter a role name or role name suffix to help you identify the purpose of this role\. Role names must be unique within your AWS account\. Because role names are case insensitive, you cannot create roles named both **PRODROLE** and **prodrole**\. Various entities might reference the role\. Therefore, you cannot edit the name of the role after it has been created\.

1. \(Optional\) For **Description**, enter a description for the new role\.

1. Choose **Edit** in the **Step 1: Select trusted entities** or **Step 2: Select permissions** sections to edit the use cases and permissions for the role\. 

1. \(Optional\) Add metadata to the user by attaching tags as key\-value pairs\. For more information about using tags in IAM, see [Tagging IAM resources](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_tags.html) in the *IAM User Guide*\.

1. Review the role, and then choose **Create role**\.

**To use the JSON policy editor to create a policy**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation column on the left, choose **Policies**\. 

   If this is your first time choosing **Policies**, the **Welcome to Managed Policies** page appears\. Choose **Get Started**\.

1. At the top of the page, choose **Create policy**\.

1. Choose the **JSON** tab\.

1. Enter or paste a JSON policy document\. For details about the IAM policy language, see [IAM JSON policy reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

1. Resolve any security warnings, errors, or general warnings generated during [policy validation](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_policies_policy-validator.html), and then choose **Review policy**\. 
**Note**  
You can switch between the **Visual editor** and **JSON** tabs any time\. However, if you make changes or choose **Review policy** in the **Visual editor** tab, IAM might restructure your policy to optimize it for the visual editor\. For more information, see [Policy restructuring](https://docs.aws.amazon.com/IAM/latest/UserGuide/troubleshoot_policies.html#troubleshoot_viseditor-restructure) in the *IAM User Guide*\.

1. On the **Review policy** page, enter a **Name** and an optional **Description** for the policy that you are creating\. Review the policy **Summary** to see the permissions that are granted by your policy\. Then choose **Create policy** to save your work\.

**To use the visual editor to create a policy**

1. Sign in to the AWS Management Console and open the IAM console at [https://console\.aws\.amazon\.com/iam/](https://console.aws.amazon.com/iam/)\.

1. In the navigation pane on the left, choose **Policies**\. 

   If this is your first time choosing **Policies**, the **Welcome to Managed Policies** page appears\. Choose **Get Started**\.

1. Choose **Create policy**\.

1. On the **Visual editor** tab, choose **Choose a service**\. Then choose an AWS service to add to the policy\. You can use the search box at the top to limit the results in the list of services\. You can choose only one service within a visual editor permissions block\. To grant access to more than one service, add multiple permissions blocks by choosing **Add additional permissions**\.

1. Choose **Select actions** and then choose the actions to add to the policy\. The visual editor shows the actions that are available in the service that you selected in the previous step\.

   You can choose actions in the following ways:
   + Use check boxes to select all actions for the service or all actions in one of the predefined **Access level groups**\.
   + Expand each of the **Access level groups** to choose individual actions\.
   + Choose **add actions** to enter a specific action or use wildcards \(`*`\) to specify multiple actions\.

   By default, the policy that you are creating allows the actions that you choose\. To deny the chosen actions instead, choose **Switch to deny permissions**\. Because [IAM denies by default](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_evaluation-logic.html), we recommend as a security best practice that you allow permissions to only those actions and resources that a user needs\. This is sometimes called "allow listing\." You should create a JSON statement to deny permissions \("deny listing"\) only if you want to override a permission that is separately allowed by another statement or policy\. We recommend that you limit the number of deny permissions to a minimum because they can increase the difficulty of troubleshooting permissions\.

1. If the actions that you selected in the previous steps do not support choosing [specific resources](https://docs.aws.amazon.com/IAM/latest/UserGuide/access_controlling.html#access_controlling-resources), then **All resources** is selected for you\. In that case, you cannot edit this section\.

   If you chose one or more actions that support [ resource\-level permissions](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_aws-services-that-work-with-iam.html), then the visual editor lists those resources You can then choose **Resources** to specify resources for your policy\.

   You can choose resources in the following ways:
   + Choose **Add ARN** to provide the details about your resource\. Instead of entering a value, you can also choose **Any** to provide permissions for any value for the specified setting\. For example, if you selected the Amazon EC2 **Read** access level group, then the actions in your policy support the `instance` resource type\. You must provide the **Region**, **Account**, and **InstanceId** values for your resource\. If you provide your account ID but choose **Any** for the Region and instance ID, then the policy grants permissions to any instance in your account\.
   + Choose **Add ARN** to specify resources by their [Amazon Resource Name \(ARN\)](https://docs.aws.amazon.com/general/latest/gr/aws-arns-and-namespaces.html)\. You can include a wildcard \(**\***\) in any field of the ARN \(between each pair of colons\)\. For more information, see [IAM JSON Policy Elements: Resource](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_resource.html) in the *IAM User Guide*\.
   + Choose **Any** from the far right of the resource section to grant permissions to any resources of a specific type\.
   + Choose **All resources** to choose all resources for that service\. 

1. \(Optional\) Choose **Specify request conditions \(optional\)** to add conditions to the policy that you are creating\. Conditions limit the effect of a JSON policy statement\. For example, you can specify that a user is allowed to perform the actions on the resources only when the user request happens within a certain time range\. You can also use commonly used conditions to limit whether a user must be authenticated using a multi\-factor authentication \(MFA\) device, or if the request must originate from within a certain range of IP addresses\. For lists of all of the context keys that you can use in a policy condition, see [Actions, resources, and condition keys for AWS services](https://docs.aws.amazon.com/service-authorization/latest/reference/reference_policies_actions-resources-contextkeys.html) in the *Service Authorization Reference*\.

   You can choose conditions in the following ways:
   + Use check boxes to select commonly used conditions\.
   + Choose **Add condition** to specify other conditions\. Choose the condition **Key**, **Qualifier**, and **Operator**, and then enter a **Value**\. To add more than one value, choose **Add new value**\. You can consider the values as being connected by a logical "OR" operator\. When you are finished, choose **Add**\.

   To add more than one condition, choose **Add condition** again\. Repeat as needed\. Each condition applies only to this one visual editor permissions block\. All the conditions must be true for the permissions block to be considered a match\. In other words, consider the conditions to be connected by a logical "AND" operator\.

   For more information about the **Condition** element, see [IAM JSON policy elements: Condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements_condition.html) in the *IAM User Guide*\.

1. To add more permissions blocks, choose **Add additional permissions**\. For each block, repeat steps 2 through 5\.

1. When you are finished, choose **Review policy**\.
**Note**  
You can switch between the **Visual editor** and **JSON** tabs any time\. However, if you make changes or choose **Review policy** in the **Visual editor** tab, IAM might restructure your policy to optimize it for the visual editor\. For more information, see [Policy restructuring](https://docs.aws.amazon.com/IAM/latest/UserGuide/troubleshoot_policies.html#troubleshoot_viseditor-restructure) in the *IAM User Guide*\.

1. On the **Review policy** page, enter a **Name** and an optional **Description** for the policy that you are creating\. Review the policy summary to make sure that you have granted the intended permissions, and then choose **Create policy** to save your new policy\.

**To grant programmatic access**

Users need programmatic access if they want to interact with AWS outside of the AWS Management Console\. The way to grant programmatic access depends on the type of user that's accessing AWS:
+ If you manage identities in IAM Identity Center, the AWS APIs require a profile, and the AWS Command Line Interface requires a profile or an environment variable\.
+ If you have IAM users, the AWS APIs and the AWS Command Line Interface require access keys\. Whenever possible, create temporary credentials that consist of an access key ID, a secret access key, and a security token that indicates when the credentials expire\.

To grant users programmatic access, choose one of the following options\.


****  

| Which user needs programmatic access? | To | By | 
| --- | --- | --- | 
|  Workforce identity \(Users managed in IAM Identity Center\)  | Use short\-term credentials to sign programmatic requests to the AWS CLI or AWS APIs \(directly or by using the AWS SDKs\)\. |  Following the instructions for the interface that you want to use: [\[See the AWS documentation website for more details\]](http://docs.aws.amazon.com/controltower/latest/userguide/access-control-manage-access-intro.html)  | 
| IAM | Use short\-term credentials to sign programmatic requests to the AWS CLI or AWS APIs \(directly or by using the AWS SDKs\)\. | Following the instructions in [Using temporary credentials with AWS resources](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_temp_use-resources.html) in the IAM User Guide\. | 
| IAM | Use long\-term credentials to sign programmatic requests to the AWS CLI or AWS APIs \(directly or by using the AWS SDKs\)\.\(Not recommended\) | Following the instructions in [Managing access keys for IAM users](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_credentials_access-keys.html) in the IAM User Guide\. | 

### Protect against attackers<a name="protect"></a>

For more information about how to help protect against attackers when you grant permissions to other AWS service principals, see [Optional conditions for your role trust relationships](roles-how.md#conditions-for-role-trust)\. By adding certain conditions to your policies, you can help prevent a specific type of attack, known as a *confused deputy* attack, which occurs if an entity coerces a more\-privileged entity to perform an action, such as with cross\-service impersonation\. For general information about policy conditions, also see [Specifying conditions in a policy](access-control-overview.md#specifying-conditions)\.

For more information about using identity\-based policies with AWS Control Tower, see [Using identity\-based policies \(IAM policies\) for AWS Control Tower](access-control-managing-permissions.md)\. For more information about users, groups, roles, and permissions, see [Identities \(Users, Groups, and Roles\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html) in the *IAM User Guide*\. 

## Resource\-based policies<a name="access-control-manage-access-intro-resource-policies"></a>

Other services, such as Amazon S3, also support resource\-based permissions policies\. For example, you can attach a policy to an S3 bucket to manage access permissions to that bucket\. AWS Control Tower does not support resource\-based policies\.