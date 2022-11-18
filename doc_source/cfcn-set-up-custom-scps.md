# Set up a configuration package for service control policies<a name="cfcn-set-up-custom-scps"></a>

This section explains how to create a configuration package for service control policies \(SCPs\)\. The two main parts of this process are \(1\) prepare the manifest file, and \(2\) prepare your folder structure\.

## Step 1: Edit the manifest\.yaml file<a name="cfct-byo-scp-step-1"></a>

Use the sample `manifest.yaml` file as your starting point\. Enter all necessary configurations\. Add the `resource_file` and `deployment_targets` details\.

The following snippet shows the default manifest file\.

```
---
region: us-east-1
version: 2021-03-15

resources: []
```

The value for `region` is added automatically during deployment\. It must match the Region where you deployed CfCT\. This Region must be the same as the AWS Control Tower region\.

To add a custom SCP in the `example-configuration` folder in the zip package stored in the Amazon S3 bucket, open the `example-manifest.yaml` file and begin editing\.

```
---
region: your-home-region
version: 2021-03-15

resources:
  - name: test-preventive-controls
    description: To prevent from deleting or disabling resources in member accounts
    resource_file: policies/preventive-controls.json
    deploy_method: scp
    #Apply to the following OU(s)
    deployment_targets:
      organizational_units: #array of strings
        - OUName1
        - OUName2 

…truncated…
```

The following snippet shows an example of a customized manifest file\. You can add more than one policy in a single change\.

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

## Step 2: Create a folder structure<a name="cfct-byo-scp-step-2"></a>

You can skip this step if you are using an Amazon S3 URL for the resource file and using **parameters** with key/value pairs\.

You must include an SCP policy in JSON format to support the manifest, because the manifest file references the JSON file\. Ensure that the file paths match the path information provided in the manifest file\. 
+ A *policy* JSON file contains the SCPs to be deployed to OUs\.

The following snippet shows the folder structure for the sample manifest file\.

```
- manifest.yaml
- policies/
   - block-s3-public.json
```

The following snippet is an example of a `block-s3-public.json` policy file\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Sid": "GuardPutAccountPublicAccessBlock",
      "Effect": "Deny",
      "Action": "s3:PutAccountPublicAccessBlock",
      "Resource": "arn:aws:s3:::*"
  ]
}
```