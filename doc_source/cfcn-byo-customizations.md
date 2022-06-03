# Build your own customizations<a name="cfcn-byo-customizations"></a>

To build your own customizations, you can modify the `manifest.yaml` file by adding or updating service control policies \(SCPs\) and AWS CloudFormation resources\. For resources that must be deployed, you can add or remove accounts and OUs\. You can add or modify the templates in the package folders, create your own folders, and reference the templates or folders in the `manifest.yaml` file\.

This section explains the two main parts of building your own customizations:
+ how to set up your own configuration package for service control policies
+ how to set up your own configuration package for AWS CloudFormation stack sets

**JSON schema for the customization package**  
The JSON schema for the customization package for CfCT is located in the [source code repository on GitHub](https://github.com/aws-solutions/aws-control-tower-customizations)\. You can use the schema with many of your favorite development tools, and you may find it helpful for reducing errors when you build your own `manifest.yaml` file\.