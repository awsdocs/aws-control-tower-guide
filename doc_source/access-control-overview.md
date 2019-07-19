# Overview of Managing Access Permissions to Your AWS Control Tower Resources<a name="access-control-overview"></a>

Every AWS resource is owned by an AWS account, and permissions to create or access a resource are governed by permissions policies\. An account administrator can attach permissions policies to IAM identities \(that is, users, groups, and roles\), and some services \(such as AWS Lambda\) also support attaching permissions policies to resources\. 

**Note**  
An *account administrator* \(or administrator\) is a user with administrator privileges\. For more information, see [IAM Best Practices](https://docs.aws.amazon.com/IAM/latest/UserGuide/best-practices.html) in the *IAM User Guide*\.

When granting permissions, you decide who is getting the permissions, the resources they get permissions for, and the specific actions that you want to allow on those resources\.

**Topics**
+ [AWS Control Tower Resources and Operations](#access-control-resources)
+ [Understanding Resource Ownership](#access-control-owner)
+ [Managing Access to Resources](#access-control-manage-access-intro)
+ [Specifying Policy Elements: Actions, Effects, and Principals](#access-control-specify-controltower-actions)
+ [Specifying Conditions in a Policy](#specifying-conditions)

## AWS Control Tower Resources and Operations<a name="access-control-resources"></a>

In AWS Control Tower, the primary resource is a *landing zone*\. AWS Control Tower also supports an additional resource type, *guardrails*\. However, for AWS Control Tower, you can manage guardrails only in the context of an existing landing zone\. Guardrails are referred to as a *subresource*\.

## Understanding Resource Ownership<a name="access-control-owner"></a>

The AWS account owns the resources that are created in the account, regardless of who created the resources\. Specifically, the resource owner is the AWS account of the [principal entity](https://docs.aws.amazon.com/IAM/latest/UserGuide/id_roles_terms-and-concepts.html) \(that is, the AWS account root user, an IAM user, or an IAM role\) that authenticates the resource creation request\. The following examples illustrate how this works:
+ If you use the AWS account root user credentials of your AWS account to set up a landing zone, your AWS account is the owner of the resource\.
+ If you create an IAM user in your AWS account and grant permissions to set up a landing zone to that user, the user can set up a landing zone as long as their account meets the prerequisites\. However, your AWS account, to which the user belongs, owns the landing zone resource\.
+ If you create an IAM role in your AWS account with permissions to set up a landing zone, anyone who can assume the role can set up a landing zone\. Your AWS account, to which the role belongs, owns the landing zone resource\. 

## Managing Access to Resources<a name="access-control-manage-access-intro"></a>

A *permissions policy* describes who has access to what\. The following section explains the available options for creating permissions policies\.

**Note**  
This section discusses using IAM in the context of AWS Control Tower\. It doesn't provide detailed information about the IAM service\. For complete IAM documentation, see [What Is IAM?](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) in the *IAM User Guide*\. For information about IAM policy syntax and descriptions, see [AWS IAM Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

Policies attached to an IAM identity are referred to as *identity\-based* policies \(IAM polices\)\. Policies attached to a resource are referred to as *resource\-based* policies\. AWS Control Tower supports only identity\-based policies \(IAM policies\)\. 

**Topics**
+ [Identity\-Based Policies \(IAM Policies\)](#access-control-manage-access-intro-iam-policies)
+ [Resource\-Based Policies](#access-control-manage-access-intro-resource-policies)

### Identity\-Based Policies \(IAM Policies\)<a name="access-control-manage-access-intro-iam-policies"></a>

You can attach policies to IAM identities\. For example, you can do the following:
+ **Attach a permissions policy to a user or a group in your account** – To grant a user permissions to create an AWS Control Tower resource, such as setting up a landing zone, you can attach a permissions policy to a user or group that the user belongs to\.
+ **Attach a permissions policy to a role \(grant cross\-account permissions\)** – You can attach an identity\-based permissions policy to an IAM role to grant cross\-account permissions\. For example, the administrator in Account A can create a role to grant cross\-account permissions to another AWS account \(for example, Account B\) or an AWS service as follows:

  1. Account A administrator creates an IAM role and attaches a permissions policy to the role that grants permissions on resources in Account A\.

  1. Account A administrator attaches a trust policy to the role identifying Account B as the principal who can assume the role\.

  1. Account B administrator can then delegate permissions to assume the role to any users in Account B\. Doing this allows users in Account B to create or access resources in Account A\. The principal in the trust policy can also be an AWS service principal if you want to grant an AWS service permissions to assume the role\.

  For more information about using IAM to delegate permissions, see [Access Management](https://docs.aws.amazon.com/IAM/latest/UserGuide/access.html) in the *IAM User Guide*\.

The following is an example policy that allows a user to set up a landing zone in your AWS account\.

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "*",
            "Resource": "*"
        }
    ]
}
```

For more information about using identity\-based policies with AWS Control Tower, see [Using Identity\-Based Policies \(IAM Policies\) for AWS Control Tower](access-control-managing-permissions.md)\. For more information about users, groups, roles, and permissions, see [Identities \(Users, Groups, and Roles\)](https://docs.aws.amazon.com/IAM/latest/UserGuide/id.html) in the *IAM User Guide*\. 

### Resource\-Based Policies<a name="access-control-manage-access-intro-resource-policies"></a>

Other services, such as Amazon S3, also support resource\-based permissions policies\. For example, you can attach a policy to an S3 bucket to manage access permissions to that bucket\.

## Specifying Policy Elements: Actions, Effects, and Principals<a name="access-control-specify-controltower-actions"></a>

Currently, AWS Control Tower doesn't have an API\. You can set up and manage your landing zone through the AWS Control Tower console\. To set up your landing zone, you must be an IAM user with administrative permissions as defined in a IAM policy\.

The following are the most basic policy elements:
+ **Resource** – In a policy, you use an Amazon Resource Name \(ARN\) to identify the resource to which the policy applies\. For more information, see [AWS Control Tower Resources and Operations](#access-control-resources)\.
+ **Action** – You use action keywords to identify resource operations that you want to allow or deny\.
+ **Effect** – You specify the effect when the user requests the specific action—this can be either allow or deny\. If you don't explicitly grant access to \(allow\) a resource, access is implicitly denied\. You can also explicitly deny access to a resource, which you might do to make sure that a user cannot access it, even if a different policy grants access\.
+ **Principal** – In identity\-based policies \(IAM policies\), the user that the policy is attached to is the implicit principal\. For resource\-based policies, you specify the user, account, service, or other entity that you want to receive permissions \(applies to resource\-based policies only\)\. AWS Control Tower doesn't support resource\-based policies\.

To learn more about IAM policy syntax and descriptions, see [AWS IAM Policy Reference](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies.html) in the *IAM User Guide*\.

## Specifying Conditions in a Policy<a name="specifying-conditions"></a>

When you grant permissions, you can use the IAM policy language to specify the conditions when a policy should take effect\. For example, you might want a policy to be applied only after a specific date\. For more information about specifying conditions in a policy language, see [Condition](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#Condition) in the *IAM User Guide*\. 

To express conditions, you use predefined condition keys\. There are no condition keys specific to AWS Control Tower\. However, there are AWS\-wide condition keys that you can use as appropriate\. For a complete list of AWS\-wide keys, see [Available Keys for Conditions](https://docs.aws.amazon.com/IAM/latest/UserGuide/reference_policies_elements.html#AvailableKeys) in the *IAM User Guide*\. 