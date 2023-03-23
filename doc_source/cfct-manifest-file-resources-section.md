# The resources section of the manifest file<a name="cfct-manifest-file-resources-section"></a>

This topic describes the entries for the **resources** section of the manifest file in detail, where you'll define the resources required for your customizations\.

This section of the manifest file begins at the keyword **resources**, and it continues to the end of the file\.

The **resources** section of the manifest file specifies the AWS CloudFormation StackSets or AWS Organizations SCPs that CfCT deploys automatically, by means of the code pipeline\. You can list the OUs and accounts and, optionally, the Regions, in which to deploy stack instances\.

The stack instances are deployed at the account level rather than the OU level\. The SCPs are deployed at the OU level\. Refer to [Build Your Own Customizations](cfcn-byo-customizations.md) for details\.

The code example that follows is a pseudo template, which summarizes the possible entries available for the **resources** section in the manifest file\.

```
resources: # List of resources
  - name: [String]
    resource_file: [String] [Local File Path, S3 URL] 
    deployment_targets: # account and/or organizational unit names
      accounts: # array of strings, [0-9]{12}
        - 012345678912
        - AccountName1
      organizational_units: #array of strings
        - OuName1
        - OuName2 
    deploy_method: scp | stack_set
    parameters: # List of parameters [SSM, Alfred, Values]
      - parameter_key: [String]
        parameter_value: [String]  
    export_outputs: # list of ssm parameters to store output values
      - name: /org/member/test-ssm/app-id
        value: $[output_ApplicationId]    
    regions: #list of strings
    - [String]
```

**The remainder of this topic gives detailed definitions for the keywords shown in the previous code example\.**

**name** – The name that is associated with the AWS CloudFormation StackSets\. The string you provide assigns a more user\-friendly name for a stack set\.
+  **Type:** String
+ **Required:** Yes
+ **Valid Values:** a\-z, A\-Z, 0\-9, and an underscore \(\_\)\. Any other character is automatically replaced with an underscore \(\_\)\.

**description** – The description for the resource\.
+ **Type:** String
+ **Required:** No

**resource\_file** – This file can be specified as: \(1\) the relative location to the manifest file, \(2\) an Amazon S3 URL that points to an AWS CloudFormation template or AWS Organizations service control policy in JSON for creating AWS CloudFormation resources or SCPs, respectively\.
+ **Type:** String
+ **Required:** Yes 

1. The following example shows the `resource_file`, given as a relative location to the resource file inside the configuration package\.

   ```
   resources:
     - name: SecurityRoles
       resource_file: templates/custom-security.template
   ```

1. The following example shows the resource file given as an Amazon S3 URL 

   ```
   resources:
     - name: SecurityRoles
       resource_file: s3://my-bucket/[key-name]
   ```

1. The following example shows the resource file given as an Amazon S3 HTTPS URL 

   ```
   resources:
     - name: SecurityRoles
       resource_file: https://bucket-name.s3.Region.amazonaws.com/key-name
   ```
**Note**  
If you provide an Amazon S3 URL, verify that the bucket policy allows read access for the AWS Control Tower management account from which you are deploying CfCT\. If you provide an Amazon S3 HTTPS URL, verify that the path uses dot notation\. For example, `S3.us-west-1`\. CfCT does not support endpoints that contain a dash between S3 and the Region, such as `S3‐us-west-2`\.

1. The following example shows an Amazon S3 bucket policy and an ARN where resources are stored\.

   ```
   {
      "Version": "2012-10-17",
      "Statement": [
          {
           "Effect": "Allow",
           "Principal": {"AWS": "arn:aws:iam::AccountId:root"},
           "Action": "s3:GetObject",
           "Resource": "arn:aws:s3:::my-bucket/*”
          }
      ]
   }  
   ```

   You'll replace the *AccountId* variable shown in the example with the AWS account ID for the management account that is deploying CfCT\. For more examples, refer to [Bucket policy examples](https://docs.aws.amazon.com/AmazonS3/latest/userguide/example-bucket-policies.html) in the Amazon Simple Storage Service User Guide\.

**parameters** – Specifies the name and value for AWS CloudFormation parameters\.
+ **Type:** MapList
+ **Required:** No

The parameters section contains pairs of key/value parameters\. The following pseudo template outlines the **parameters** section\.

```
parameters:
  - parameter_key: [String]
    parameter_value: [String]
```
+ **parameter\_key** – The key associated with the parameter\.
  +  **Type:** String
  + **Required:** Yes \(under parameters property\)
  + **Valid Values:** a\-z, A\-Z, and 0\-9 
+ **parameter\_value** – The input value associated with the parameter\.
  + **Type:** String 
  +  **Required:** Yes \(under parameters property\) 

**deploy\_method** – The deployment method for deploying resource\(s\) into the account\. Currently, **deploy\_method** supports deploying resources using the `stack_set` option for resource deployment through AWS CloudFormation StackSets, or the `scp` option if you are deploying SCPs\.
+ **Type:** String 
+ **Valid Values:** `stack_set` \| `scp`
+ **Required:** Yes

**deployment\_targets** – List of accounts or Organizational Units \(OUs\), into which CfCT will deploy the AWS CloudFormation resources, specified as **accounts** or **organizational\_units**\.

**Note**  
 If you want to deploy an SCP, the target must be an OU, not an account\. 
+ **Type:** List of string `account name` or `account number` to indicate that this resource will be deployed into the given account list, or `OU names` to indicate that this resource will be deployed into the given OU list\.
+ **Required:** At least one of **accounts** or **organizational\_units**
  + **accounts:**

    **Type:** List of string `account name` or `account number` to indicate that this resource will be deployed into the given account list\.
  + **organizational\_units:**

    **Type:** List of string `OU names` to indicate that this resource will be deployed into a given OU list\. If you provide an OU that doesn’t contain accounts and the **accounts** property is not added, CfCT only creates the stack set\.
**Note**  
The organization’s management account ID is not an allowed value\. CfCT does not support deploying stack instances into the organization’s management account\.

**export\_outputs** – List of name/value pairs that denote SSM parameter keys\. These SSM parameter keys allow you to store template outputs into the SSM parameter store\. The output is intended for reference by other resources, defined earlier in the manifest file\.

```
export_outputs: # List of SSM parameters
  - name: [String]
    value: [String]
```
+ **Type:** List of **name** and **value** key pairs\. The **name** contains the `name` string of an SSM parameter store key, and **value** contains the parameter's `value` string\.
+ **Valid Values:** Any string or the `$[output_CfnOutput-Logical-ID]` variable where *CfnOutput\-Logical\-ID* corresponds to the template output variable\. For more information about the Outputs section in an AWS CloudFormation template, see [https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/outputs-section-structure.html](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/outputs-section-structure.html) in the *AWS CloudFormation User Guide*\.
+ **Required:** No 

For example, the following code snippet stores the template `VPCID` output variable into the SSM parameter key that's named `/org/member/audit/vpc_id`\.

```
export_outputs: # List of SSM parameters
  - name: /org/member/audit/VPC-ID
    value: $[output_VPCID]
```

**Note**  
The **export\_outputs** key name may contain a value other than `output`\. For example, if the **name** is `/org/environment-name`, the **value** may be `production`\.

**regions** – List of Regions in which CfCT will deploy the AWS CloudFormation stack instances\. 
+ **Type:** Any list of AWS commercial Region names, to indicate that this resource will be deployed into the given Region list\. If this keyword does not exist in the manifest file, the resources are deployed in the home Region only\.
+ **Required:** No 