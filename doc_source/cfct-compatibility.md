# Manifest version upgrades<a name="cfct-compatibility"></a>

 For information about the latest version of *Customizations for AWS Control Tower* \(CfCT\), see the [CHANGELOG\.md file](https://github.com/aws-solutions/aws-control-tower-customizations/blob/master/CHANGELOG.md) in the GitHub repository\. 

**Warning**  
 Version 2\.2\.0 of *Customizations for AWS Control Tower* \(CfCT\) introduced a manifest schema \(version *2021\-03\-15*\) to align with related AWS service APIs\. The manifest schema allows a single manifest\.yaml file to manages supported resources \(AWS CloudFormation templates and SCPs\) through decoupled DevOps workflows\.   
 We strongly recommend that you update the manifest schema from version *2020\-01\-01* to version *2021\-03\-15* or later\.  
 CfCT continues to support version *2021\-03\-15* and *2020\-01\-01* of the `manifest.yaml` file\. No changes to your existing configuration are required\. However, version *2020\-01\-01* is at **End of Support**\. We no longer provide updates or add enhancements to version *2020\-01\-01*\. The Root OU and nested OU features aren't supported in version *2020\-01\-01*\. 

**Deprecated properties in manifest version *2021\-03\-15:***

```
organization_policies
policy_file
apply_to_accounts_in_ou

cloudformation_resources
template_file
deploy_to_account
deploy_to_ou
ssm_parameters
```

## Mandatory upgrade steps<a name="mandatory-upgrade-steps"></a>

When you upgrade to the manifest schema version *2021\-03\-15* version, here are the changes you must make to update your files\. The next sections outline mandatory and recommended changes for the transition\.

**Organizations polices**

1. Move the SCPs under **organization\_policies** under new property **resources**\.

1. Change the **policy\_file** property to new property **resource\_file\.**

1. Change the **apply\_to\_accounts\_in\_ou** to new property **deployment\_targets**\. The OU list should be defined under sub\-property **organizational\_units**\. The **accounts** sub\-property is not supported for organizations policies\.

1. Add a new property **deploy\_method** with the value **scp**\. 

**AWS CloudFormation resources**

1. Move the CloudFormation resources under **cloudformation\_resources** under new property **resources**\.

1. Change the **template\_file** property to new property **resource\_file**\.

1. Change the **deploy\_to\_ou** to new property **deployment\_targets**\. The OU list should be defined under sub\-property **organizational\_units**\.

1. Change the **deploy\_to\_accounts** to new property **deployment\_targets**\. The account list should be defined under sub\-property **accounts**\.

1. Change the **ssm\_parameters** property to new property **export\_outputs**\.

## Highly recommended upgrade steps<a name="highly-recommended-upgrade-steps"></a>

**AWS CloudFormation parameters**

1. Change the **parameter\_file** property to new property **parameters**\.

1. Remove the file path in the value of the **parameter\_file** property\.

1. Copy the parameter key and parameter value from the existing parameter JSON file into the new format for the **parameters** property\. This would help you manage them in the manifest file\.

**Note**  
The **parameter\_file** property is supported in manifest version *2021\-03\-15*\.