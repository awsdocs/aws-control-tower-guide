# Define a custom configuration<a name="cfct-custom-configuration"></a>

You'll define your custom AWS Control Tower configuration with the manifest file, the accompanying set of templates, and other JSON files\. You'll package these files into a folder structure and place them in the Amazon S3 bucket as a `.zip` file, as shown in the following code example\.

**Custom configuration folder structure**

```
- manifest.yaml
- policies/                                                [optional]
   - service control policies files (*.json)
- templates/                                               [optional]
   - template files for AWS CloudFormation Resources (*.template)
```

The previous example depicts the structure of a custom configuration folder\. The folder structure stays the same whether you choose Amazon S3 or an AWS CodeCommit repository as your source storage location\. If you choose Amazon S3 as source storage, compress all the folders and files into a `custom-control-tower-configuration.zip` file, and upload only the `.zip` file to the designated Amazon S3 bucket\.

**Note**  
If you are using AWS CodeCommit, place the files in the repository without zipping the files\.