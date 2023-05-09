# Set up a configuration package for AWS CloudFormation StackSets<a name="cfcn-byo-cfn-stacksets"></a>

This section explains how to set up a configuration package for AWS CloudFormation StackSets\. The two main parts of this process are: \(1\) prepare the manifest file, and \(2\) update the folder structure\.

## Step 1: Edit the existing manifest file<a name="cfcn-byo-cfn-stacksets-step-1"></a>

Add the new AWS CloudFormation StackSets information to the manifest file that you previously edited\.

Just for review, the following snippet contains the same customized manifest file that was shown previously to set up a configuration package for SCPs\. Now you can edit this file further, to include the details about your resources\.

```
---
region: us-east-1
version: 2021-03-15

resources:
  
  - name: block-s3-public-access
    description: To S3 buckets to have public access
    resource_file: policies/block-s3-public.json
    deploy_method: scp
    #Apply to the following OU(s)
    deployment_targets:
    organizational_units: #array of strings
    - OUName1
    - OUName2
```

The following snippet shows an edited sample manifest file that contains the `resources` details\. The order of `resources` determines the execution order for creating `resources` dependencies\. You can edit the following example manifest file according to your business requirements\.

```
---
region: your-home-region
version: 2021-03-15

…truncated…

resources:
  - name: stackset-1
    resource_file: templates/create-ssm-parameter-keys-1.template
    parameters:
      - parameter_key: parameter-1
        parameter_value: value-1
    deploy_method: stack_set
    deployment_targets:
      accounts: # array of strings, [0-9]{12}
        - account number or account name
        - 123456789123
      organizational_units: #array of strings, ou ids, ou-xxxx
        - OuName1
        - OUName2 
    export_outputs:
      - name: /org/member/test-ssm/app-id
        value: $[output_ApplicationId]
    regions:
      - region-name

  - name: stackset-2
    resource_file: s3://bucket-name/key-name
    parameters:
      - parameter_key: parameter-1
        parameter_value: value-1
    deploy_method: stack_set
    deployment_targets:
      accounts: # array of strings, [0-9]{12}
        - account number or account name
        - 123456789123
      organizational_units: #array of strings
        - OuName1
        - OUName2 
regions:
  - region-name
```

The following example shows that you can add more than one AWS CloudFormation resource in the manifest file\.

```
---
region: us-east-1
version: 2021-03-15

resources:
  - name: block-s3-public-access
    description: To S3 buckets to have public access
    resource_file: policies/block-s3-public.json
    deploy_method: scp
    #Apply to the following OU(s)
    deployment_targets:
      organizational_units: #array of strings
        - Custom
        - Sandbox

  - name: transit-network
    resource_file: templates/transit-gateway.template
    parameter_file: parameters/transit-gateway.json
    deploy_method: stack_set
    deployment_targets:
      accounts: # array of strings, [0-9]{12}
        - Prod
        - 123456789123 #Network
      organizational_units: #array of strings
        - Custom
    export_outputs:
      - name: /org/network/transit-gateway-id
        value: $[output_TransitGatewayID]
    regions:
      - us-east-1
```

## Step 2: Update the folder structure<a name="cfct-byo-cfn-stacksets-step-2"></a>

When you update the folder structure, you can include all supporting AWS CloudFormation template files and SCP policy files that are in the manifest file\. Verify that the file paths match what is provided in the manifest file\. 
+ A *template* file contains the AWS resources to be deployed in OUs and accounts\.
+ A *policy* file contains the input parameters used in the template file\.

The following example shows the folder structure for the sample manifest file created in [Step 1](#cfcn-byo-cfn-stacksets-step-1)\.

```
- manifest.yaml
- policies/
   - block-s3-public.json
- templates/
   - transit-gateway.template
```