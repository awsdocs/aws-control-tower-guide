# Recommendations for setting up groups, roles, and policies<a name="roles-recommendations"></a>

As you set up your landing zone, it's a good idea to decide ahead of time which users will require access to certain accounts and why\. For example, a security account should be accessible only to the security team, the management account should be accessible only to the cloud administrators' team, and so forth\.

For more information about this topic, see [Identity and Access Management in AWS Control Tower](auth-access.md)\.

**Recommended restrictions**

You can restrict the scope of administrative access to your organizations by setting up an IAM role or policy that allows administrators to manage AWS Control Tower actions only\. The recommended approach is to use the IAM Policy `arn:aws:iam::aws:policy/service-role/AWSControlTowerServiceRolePolicy`\. With the `AWSControlTowerServiceRolePolicy` role enabled, an administrator can manage AWS Control Tower only\. Be sure to include appropriate access to AWS Organizations for managing your preventive guardrails, and SCPs, and access to AWS Config, for managing detective guardrails, in each account\.

When you're setting up the shared audit account in your landing zone, we recommend that you assign the `AWSSecurityAuditors` group to any third\-party auditors of your accounts\. This group gives its members read\-only permission\. An account must not have write permissions on the environment that it is auditing, because it can violate compliance with Separation of Duty requirements for auditors\. 

You can impose conditions in your role trust policies, to restrict the accounts and resources that interact with certain roles in AWS Control Tower\. We strongly recommend that you restrict access to the `AWSControlTowerAdmin` role, because it allows wide access permissions\. for more information, see [Optional conditions for your role trust relationships](roles-how.md#conditions-for-role-trust)\.