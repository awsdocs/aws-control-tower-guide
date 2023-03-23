# Overview of managing access permissions to your AWS Control Tower resources<a name="access-control-overview"></a>

Every AWS resource is owned by an AWS account, and permissions to create or gain access to a resource are governed by permissions policies\. An account administrator can attach permissions policies to IAM identities \(that is, users, groups, and roles\)\. Some services \(such as AWS Lambda\) also support attaching permissions policies to resources\. 

**Note**  
An *account administrator* \(or administrator\) is a user with administrator privileges\. For more information, see [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

When you are responsible for granting permissions to a user or role, you must know and track the *users and roles* that require permissions, the *resources* for which each user and role requires permissions, and the *specific actions* that must be allowed for operating those resources\.

**Topics**
+ [AWS Control Tower resources and operations](#access-control-resources)
+ [About resource ownership](#access-control-owner)
+ [Manage access to resources](access-control-manage-access-intro.md)
+ [Specifying policy elements: Actions, Effects, and Principals](#access-control-specify-controltower-actions)
+ [Specifying conditions in a policy](#specifying-conditions)

## AWS Control Tower resources and operations<a name="access-control-resources"></a>

In AWS Control Tower, the primary resource is a *landing zone*\. AWS Control Tower also supports an additional resource type, *controls*, sometimes referred to as *guardrails*\. However, for AWS Control Tower, you can manage controls only in the context of an existing landing zone\. Controls can be referred to as a *subresource*\.

Resources and subresources in AWS have unique Amazon Resource Names \(ARNs\) associated with them, as shown in the following example\.

## About resource ownership<a name="access-control-owner"></a>

The AWS account owns the resources that are created in the account, regardless of who created the resources\. Specifically, the resource owner is the AWS account of the [principal entity](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html) \(that is, the AWS account root user, an IAM Identity Center user, an IAM user, or an IAM role\) that authenticates the resource creation request\. The following examples illustrate how this works:
+ If you use the AWS account root user credentials of your AWS account to set up a landing zone, your AWS account is the owner of the resource\.
+ If you create an IAM user in your AWS account and grant permissions to set up a landing zone to that user, the user can set up a landing zone as long as their account meets the prerequisites\. However, your AWS account, to which the user belongs, owns the landing zone resource\.
+ If you create an IAM role in your AWS account with permissions to set up a landing zone, anyone who can assume the role can set up a landing zone\. Your AWS account, to which the role belongs, owns the landing zone resource\. 

## Specifying policy elements: Actions, Effects, and Principals<a name="access-control-specify-controltower-actions"></a>

Currently, AWS Control Tower doesn't have an API for setting up a landing zone, only for managing controls\. You can set up and manage your landing zone through the AWS Control Tower console\. To set up your landing zone, you must be an IAM user with administrative permissions as defined in a IAM policy\.

The following elements are the most basic ones you can identify in a policy:
+ **Resource** – In a policy, you use an Amazon Resource Name \(ARN\) to identify the resource to which the policy applies\. For more information, see [AWS Control Tower resources and operations](#access-control-resources)\.
+ **Action** – You use action keywords to identify resource operations that you want to allow or deny\. For information about types of actions available to be performed, see [Actions defined by AWS Control Tower](https://docs.aws.amazon.com/service-authorization/latest/reference/list_awscontroltower.html#awscontroltower-actions-as-permissions)\.
+ **Effect** – You specify the effect when the user requests the specific action—this can be either allow or deny\. If you don't explicitly grant access to \(allow\) a resource, access is implicitly denied\. You can also explicitly deny access to a resource, which you might do to make sure that a user cannot access it, even if a different policy grants access\.
+ **Principal** – In identity\-based policies \(IAM policies\), that user to which the policy is attached is the implicit principal\. For resource\-based policies, you specify the user, account, service, or other entity that you want to receive permissions \(applies to resource\-based policies only\)\. AWS Control Tower doesn't support resource\-based policies\.

To learn more about IAM policy syntax and descriptions, see [AWS IAM Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

## Specifying conditions in a policy<a name="specifying-conditions"></a>

When you grant permissions, you can use the IAM policy language to specify the conditions when a policy should take effect\. For example, you might want a policy to be applied only after a specific date\. For more information about specifying conditions in a policy language, see [Condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#Condition) in the *IAM User Guide*\. 

To express conditions, you can use predefined condition keys\. There are no condition keys specific to AWS Control Tower\. However, there are AWS\-wide condition keys that you can use as appropriate\. For a complete list of AWS\-wide keys, see [Available Keys for Conditions](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#AvailableKeys) in the *IAM User Guide*\. 