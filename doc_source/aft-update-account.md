# Update an existing account<a name="aft-update-account"></a>

You may update exsting accounts that AFT has provisioned by updating previously submitted account requests\. The `git push` action triggers the account provisioning workflow, which processes an account update request\.

When you're updating an existing account, AFT allows you to update the `ManagedOrganizationalUnit` field of the `control_tower_parameters` input, and other fields that are NOT part of the `control_tower_parameters` input\. The input to `control_tower_parameters`, which you provided during provisioning, cannot be changed\.

**Note**  
You can update existing AWS Control Tower accounts that were created outside of AFT by specifying the account in the `aft-account-request` repository\.

**To update an account not provisioned by AFT**

To update an existing AWS account with AFT, the account must meet these prerequisites:
+ The AWS account must be part of the AWS Control Tower organization\.
+ The AWS account must be enrolled in AWS Control Tower\.