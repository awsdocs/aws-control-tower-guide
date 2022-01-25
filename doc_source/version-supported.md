# Terraform version supported<a name="version-supported"></a>

AFT supports Terraform version `0.15.x` or later\. You must provide a Terraform version as an input parameter for the AFT deployment process, as shown in the example that follows\.

```
version_number = "0.15.1"
```

## Terraform distributions<a name="terraform-distributions"></a>

AFT supports three Terraform distributions\.
+ Terraform OSS
+ Terraform Cloud
+ Terraform Enterprise

These distributions are explained in the sections that follow\. You must provide the Terraform distribution of your choice as an input parameter during the AFT bootstrap process\. See [Deploy AWS Control Tower Account Factory for Terraform \(AFT\)](aft-getting-started.md) for more information on AFT deployment and input parameters\.

When using Terraform Cloud or Terraform Enterprise, ensure that the [API token](https://www.terraform.io/cloud-docs/users-teams-organizations/api-tokens) you are using for `terraform_token` is a User or Team API Token - an Organization Token is not supported for all [required APIs](https://www.terraform.io/cloud-docs/api-docs/configuration-versions). Avoid checking-in this value to your to VCS by using a [terraform variable](https://www.terraform.io/cloud-docs/workspaces/variables/managing-variables).

```
  # Sensitive variable managed in Terraform Cloud:
  terraform_token = var.terraform_cloud_token
```

### Terraform Open Source Software \(Terraform OSS\)<a name="terraform-oss"></a>

When you select Terraform OSS as your distribution, AFT manages the Terraform backend for you in the AFT management account\. AFT downloads the `terraform-cli` of your specified Terraform version to run during the AFT deployment and the AFT pipeline phases\. The resulting Terraform state configuration is stored in an Amazon S3 bucket, named with the following form:

```
aft-backend-[account_id]-primary-region
```

AFT also creates an Amazon S3 bucket that replicates your Terraform state configuration in another AWS Region, for disaster recovery purposes, named with the following form:

```
aft-backend-[account_id]-secondary-region
```

We recommend that you enable multi\-factor authentication \(MFA\) for delete functions on these Terraform state Amazon S3 buckets\. See [the Terraform documentation](https://www.terraform.io/docs/cli/index.html) to learn more about Terraform OSS\.

To select Terraform OSS as your distribution, provide the following input parameter:

```
distribution = "oss"
```

### Terraform Cloud<a name="terraform-cloud"></a>

When you select Terraform Cloud as your distribution, AFT creates workspaces for the following components in your Terraform Cloud organization, and it triggers API\-driven workflow for the resulting Terraform runs\.
+ Account request
+ AFT account provisioning customizations for accounts provisioned by AFT
+ Account customizations for accounts provisioned by AFT
+ Global customizations for accounts provisioned by AFT

The resulting Terraform state configuration is managed by Terraform Cloud\.

For selecting Terraform Cloud as your distribution, provide the following input parameters:
+  `distribution = "tfc"`
+ `terraform_token` – This parameter contains the value of your Terraform Cloud token\. AFT marks its value as sensitive and stores it as a secure string in the SSM parameter store, in the AFT management account\. We recommend that you periodically rotate the value of the Terraform token, according to your company's security policies and compliance guidelines\. Terraform token should be a User or Team level API token, Organization tokens are not supported.
+ `terraform_org_name` – This parameter contains the name of your Terraform Cloud organization\.

See [the Terraform documentation](https://www.terraform.io/docs/cloud/index.html) to learn more about how to set up Terraform Cloud\.

### Terraform Enterprise<a name="terraform-enterprise"></a>

When you select Terraform Enterprise as your distribution, AFT creates workspaces for the following components in your Terraform Enterprise organization, and it triggers API\-driven workflow for the resulting Terraform runs\.
+ Account request
+ AFT account provisioning customizations for accounts provisioned by AFT
+ Account customizations for accounts provisioned by AFT
+ Global customizations for accounts provisioned by AFT

The resulting Terraform state configuration is managed by your Terraform Enterprise setup\.

To select Terraform Enterprise as your distribution, provide the following input parameters:
+  `distribution = "tfe"`
+ `terraform_token` – This parameter contains the value of your Terraform Enterprise token\. AFT marks its value as sensitive and stores it as a secure string in the SSM parameter store, in the AFT management account\. We recommend that you periodically rotate the value of the Terraform token, according to your company's security policies and compliance guidelines\. Terraform token should be a User or Team level API token, Organization tokens are not supported.
+ `terraform_org_name` – This parameter contains the name of your Terraform Enterprise organization\.
+ `terraform_api_endpoint` – This parameter contains the URL of your Terraform Enterprise environment\. The value of this parameter must be in the format:

  ```
  https://{fqdn}/api/v2/
  ```

See [the Terraform documentation](https://www.terraform.io/docs/enterprise/index.html) to learn more about how to set up Terraform Enterprise\.
