# Nested OUs in AWS Control Tower<a name="nested-ous"></a>

This chapter lists the expectations and considerations you'll want to be aware of when working with nested OUs in AWS Control Tower\. In most ways, working with nested OUs is the same as working with a flat OU structure\. The **Register** and **Re\-register** features work with nested OUs, except for the changed behaviors that are noted in this chapter\.

## Video Walkthrough<a name="nested-ou-video"></a>

This video \(4:46\) describes how to manage nested OU deployments in AWS Control Tower\. For better viewing, select the icon at the lower right corner of the video to enlarge it to full screen\. Captioning is available\.

For guidance regarding best practices for nested OUs and your landing zone, see the blog post [Organizing your AWS Control Tower landing zone with nested OUs](http://aws.amazon.com/blogs/mt/organizing-your-aws-control-tower-landing-zone-with-nested-ous/)\.

## Expand from flat OU structure to nested OU structure<a name="flat-to-nested"></a>

If you created your AWS Control Tower landing zone with a flat OU structure, you can expand it to a nested OU structure\. 

**This process has four main steps:**

1. Create your desired nested OU structure in AWS Control Tower\.

1. Go to the AWS Organizations console and use their bulk move feature to move the accounts from the source OU \(flat\) into the destination OU \(nested\)\. Here’s how:

   1. Go to the OU from which you want to move accounts\.

   1. Select all the accounts in the OU\.

   1. Choose **Move**\.
**Note**  
This step must be done in the in AWS Organizations console because AWS Control Tower doesn’t have a **Move** feature\.

1. Go to the nested OU in AWS Control Tower and **Register** or **Re\-register** it\. All of the accounts in the nested OU will be enrolled\.
   + If you created the OU in AWS Control Tower, **Re\-register** the OU\.
   + If you created the OU in AWS Organizations, **Register** the OU for the first time\.

1. After your accounts are moved and enrolled, delete the empty top\-level OU, either from the AWS Organizations console or from the AWS Control Tower console\.

## Nested OU registration pre\-checks<a name="nested-ou-prechecks"></a>

To support successful registration of your nested OUs and their member accounts, AWS Control Tower performs a series of pre\-checks\. These same prechecks are performed when registering any top\-level OU or nested OU\. For more information, see [Common causes of failure during registration or re\-registration](importing-existing.md#common-eg-failures)\.
+ If all pre\-checks pass, AWS Control Tower begins registering your OU, automatically\.
+ If any pre\-checks fail, AWS Control Tower stops the registration process and provides you with a list of items that must be fixed before you can register your OU\. 

## Nested OUs and roles<a name="nested-ous-and-roles"></a>

AWS Control Tower deploys the `AWSControlTowerExecution` role to accounts under the target OU, and to accounts in all OUs nested under the target OU, even when your intention is to register the target OU only\. This role gives any user of the management account **Administrator** permissions on any account that has the `AWSControlTowerExecution` role\. The role can be used to perform actions that normally would be disallowed by AWS Control Tower guardrails\.

You can delete this role from unenrolled accounts that you don't plan to enroll\. If you delete this role, you cannot enroll the account with AWS Control Tower, or register the immediate parent OUs, unless you restore the role to the account\. To delete the `AWSControlTowerExecution` role from an account, you must be signed in under the `AWSControlTowerExecution` role, because no other IAM principals are allowed to delete roles managed by AWS Control Tower\.

For information about how to restrict role access, see [Optional conditions for your role trust relationships](roles-how.md#conditions-for-role-trust)\.

## What happens during registration and re\-registration of nested OUs and accounts<a name="nested-ous-and-accounts"></a>

When you register or re\-register a nested OU, AWS Control Tower enrolls all unenrolled accounts of the target OU, and it updates all enrolled accounts\. Here's what to expect\.

**AWS Control Tower performs the following tasks**
+ Adds the `AWSControlTowerExecution` role to all unenrolled accounts under this OU, and to all unenrolled accounts in its nested OUs\. 
+ Enrolls member accounts that are not enrolled\.
+ Re\-enrolls enrolled member accounts\.
+ Creates an SSO login for newly enrolled member accounts\.
+ Updates existing enrolled member accounts to reflect your landing zone changes\.
+ Updates guardrails that are configured for this OU and its member accounts\.

## Considerations for nested OU registration<a name="nested-ou-registration"></a>
+ You cannot register an OU under the core OU \(Security OU\)\.
+ Nested OUs must be registered separately\.
+ You cannot register an OU unless its parent OU is registered\.
+ You cannot register an OU unless all OUs higher in the tree have been registered successfully at some time \(some may have been deleted\)\.
+ You can register an OU that is under a drifted higher OU, but the drift is not repaired by that action\.

## Nested OU limitations<a name="nested-ou-limitations"></a>
+ OUs may be nested a maximum of 5 levels deep under the root\.
+ Nested OUs under the target OU must be registered or re\-registered separately\.
+ If the target OU is at Level 2 or below in the hierarchy, that is, if it is not a top\-level OU, preventive guardrails enabled on higher OUs are enforced on this OU and all OUs below it, automatically\.
+ OU registration failures do not propagate up the hierarchy tree\. You can see details about the states of nested OUs on the parent’s OU details page\.
+ OU registration failures do not propagate down the hierarchy tree\.
+ AWS Control Tower does not modify your VPC settings for any new or existing accounts\.

## Nested OUs and compliance<a name="nested-ou-compliance"></a>

From the AWS Control Tower console, you can view OUs and accounts that are non\-compliant, so you can understand compliance at a larger scale\.

**Considerations about compliance for nested OUs and accounts**
+ An OU's compliance is not determined based on the compliance of the OUs nested under it\.
+ A guardrail's compliance status is computed over all OUs on which the guardrail is enabled, including nested OUs\.
+ An OU is shown as noncompliant only if it has accounts that are noncompliant, regardless of where the OU sits in the OU hierarchy\.
+ If a nested OU is noncompliant, its parent OU is not automatically considered to be noncompliant\.

## Nested OUs and drift<a name="nested-ous-and-drift"></a>

In certain situations, drift can prevent the registration of nested OUs\.

**Expectations for drift and nested OUs**
+ You can enable guardrails on OUs with drifted parents, but not on drifted OUs directly\.
+ You are allowed to enable detective guardrails under a drifted OU, as long as it’s not a top\-level drifted OU\.
+ Mandatory guardrails are enabled on top\-level OUs only\. Mandatory guardrails are skipped when you register a nested OU\.
+ One mandatory guardrail protects AWS Config resources; therefore, that guardrail must be in a non\-drifted state to register nested OUs\. If drifted, AWS Control Tower blocks registration of nested OUs\.
+ If the top\-level OU is in drift, the guardrail that protects AWS Config resources may be in drift\. In this situation, AWS Control Tower blocks any action that requires creation or update of AWS Config resources, including application of detective guardrails\.

## Nested OUs and guardrails<a name="nested-ous-and-guardrails"></a>

When you enable a guardrail on a registered OU, preventive and detective guardrails have different behaviors\.

**Preventive guardrails**
+ Preventive guardrails are enforced on nested OUs\.
+ Mandatory preventive guardrails are enforced on all accounts under the OU and its nested OUs\.
+ Preventive guardrails affect all accounts and OUs nested under the target OU, even if those accounts and OUs are not registered\.

**Detective guardrails**
+ Nested OUs do not inherit detective guardrails automatically; these must be enabled separately\.
+ Detective guardrails are deployed only to registered accounts in your landing zone’s operating Regions\.

**Enabled guardrail states and inheritance**

 You can view inherited guardrails for each OU, on the **OU details** page\. 

**Inherited status**
+ The status **Inherited** indicates that the guardrail is enabled by inheritance only, and it has not been applied directly to the OU\.
+ The status **Enabled** means the guardrail is enforced on this OU, regardless of its state on  other OUs\.
+ The status **Failed** means the guardrail is not enforced on this OU, regardless of its state on  other OUs\.

**Note**  
The status **Inherited** indicates that the guardrail was applied to an OU higher in the tree, and it is enforced on this OU, but it was not added directly to this OU\.

**If your landing zone is not the current version**  
 Each row in the **Enabled guardrails** table represents one enabled guardrail on one, individual OU\.

## Nested OUs and the root<a name="nested-ous-and-root"></a>

The root is not an OU, and it cannot be registered or re\-registered\. You also can’t create accounts directly in the root\. The root cannot be noncompliant or have a lifecycle state, such as *registered* or *in drift*\. 

However, the root is the top\-level container for all accounts and OUs\. In the context of nested OUs, it is the node that all other OUs are “nested” under\.