# The manifest file<a name="the-manifest-file"></a>

The `manifest.yaml` file is a text file that describes your AWS resources\. The following example shows the structure of the manifest file\.

```
---
region: String
version: 2021-03-15

resources:
  #set of CloudFoundation resources or SCP policies
...
```

As shown in the previous code example, the first two lines of the manifest file specify the values of the **region** and the **version** keywords\. Here are the definitions of those keywords\.

**region** – A text string for the AWS Control Tower default Region\. This value must be a valid AWS Region name \(such as `us-east-1`, `eu-west-1`, or `ap-southeast-1`\)\. The AWS Control Tower home Region is the default when you create custom AWS Control Tower resources \(such as AWS CloudFormation StackSets\), unless a more resource\-specific Region is specified\.

```
region:your-home-region
```

**version** – The manifest schema version number\. The latest supported version is 2021\-03\-15\.

```
version: 2021-03-15
```

**Note**  
We strongly recommend you use the latest version\. To update manifest properties in the latest version, refer to [Manifest version upgrades](cfct-compatibility.md)\.

The next keyword shown in the previous example is the **resources** keyword\. The **resources** section of the manifest file is highly structured\. It contains a detailed list of AWS resources, which will be deployed automatically by the CfCT pipeline\. These descriptions of resources and their available parameters are given in the next section\.