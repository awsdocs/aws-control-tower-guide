# Walkthrough: Automate Account Provisioning in AWS Control Tower by Service Catalog APIs<a name="automated-provisioning-walkthrough"></a>

AWS Control Tower is integrated with several other AWS services, such as AWS Service Catalog\. You can use the APIs to create and provision your member accounts in AWS Control Tower\.

The video shows you how to provision accounts in an automated, batch fashion, by calling the AWS Service Catalog APIs\. For provisioning, you'll call the [https://docs.aws.amazon.com/servicecatalog/latest/dg/API_ProvisionProduct.html](https://docs.aws.amazon.com/servicecatalog/latest/dg/API_ProvisionProduct.html) API from the AWS command line interface \(CLI\), and you'll specify a JSON file that contains the parameters for each account you'd like to set up\. The video illustrates installing and using the [AWS Cloud9](https://docs.aws.amazon.com/cloud9/latest/user-guide/welcome.html) development environment to perform this work\. The CLI commands would be the same if you use AWS Cloudshell instead of AWS Cloud9\.

**Note**  
You also can adapt this approach for automating account updates, by calling the [https://docs.aws.amazon.com/servicecatalog/latest/dg/API_UpdateProvisionedProduct.html](https://docs.aws.amazon.com/servicecatalog/latest/dg/API_UpdateProvisionedProduct.html) API of AWS Service Catalog for each account\. You can write a script to update the accounts, one by one\.

As a completely different automation method, if you are familiar with Terraform, you can [provision accounts with AWS Control Tower Account Factory for Terraform \(AFT\)](taf-account-provisioning.md)\.

**Sample automation administration role**

Here is a sample template you can use to help configure your automation administration role in the management account\. You would configure this role in your management account so it can perform the automation with Administrator access in the target accounts\.

```
AWSTemplateFormatVersion: 2010-09-09
Description: Configure the SampleAutoAdminRole

Resources:
  AdministrationRole:
    Type: AWS::IAM::Role
    Properties:
      RoleName: SampleAutoAdminRole
      AssumeRolePolicyDocument:
        Version: 2012-10-17
        Statement:
          - Effect: Allow
            Principal:
              Service: cloudformation.amazonaws.com
            Action:
              - sts:AssumeRole
      Path: /
      Policies:
        - PolicyName: AssumeSampleAutoAdminRole
          PolicyDocument:
            Version: 2012-10-17
            Statement:
              - Effect: Allow
                Action:
                  - sts:AssumeRole
                Resource:
                  - "arn:aws:iam::*:role/SampleAutomationExecutionRole"
```

**Sample automation execution role**

Here is a sample template you can use to help you set up your automation execution role\. You would configure this role in the target accounts\.

```
AWSTemplateFormatVersion: "2010-09-09"
Description: "Create automation execution role for creating Sample Additional Role."

Parameters:
  AdminAccountId:
    Type: "String"
    Description: "Account ID for the administrator account (typically management, security or shared services)."
  AdminRoleName:
    Type: "String"
    Description: "Role name for automation administrator access."
    Default: "SampleAutomationAdministrationRole"
  ExecutionRoleName:
    Type: "String"
    Description: "Role name for automation execution."
    Default: "SampleAutomationExecutionRole"
  SessionDurationInSecs:
    Type: "Number"
    Description: "Maximum session duration in seconds."
    Default: 14400

Resources:
  # This needs to run after AdminRoleName exists.
  ExecutionRole:
    Type: "AWS::IAM::Role"
    Properties:
      RoleName: !Ref ExecutionRoleName
      MaxSessionDuration: !Ref SessionDurationInSecs
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          - Effect: "Allow"
            Principal:
              AWS:
                - !Sub "arn:aws:iam::${AdminAccountId}:role/${AdminRoleName}"
            Action:
              - "sts:AssumeRole"
      Path: "/"
      ManagedPolicyArns:
        - "arn:aws:iam::aws:policy/AdministratorAccess"
```

After configuring these roles, you call the AWS Service Catalog APIs to perform the automated tasks\. The CLI commands are given in the video\.

## Sample provisioning input for Service Catalog API<a name="sample-sc-api-input"></a>

Here is a sample of the input you can give to the Service Catalog `ProvisionProduct` API if you're using the API to provision AWS Control Tower accounts:

```
{
  pathId: "lpv2-7n2o3nudljh4e",
  productId: "prod-y422ydgjge2rs",
  provisionedProductName: "Example product 1",
  provisioningArtifactId: "pa-2mmz36cfpj2p4",
  provisioningParameters: [
    {
      key: "AccountEmail",
      value: "abc@amazon.com"
    },
    {
      key: "AccountName",
      value: "ABC"
    },
    {
      key: "ManagedOrganizationalUnit",
      value: "Custom (ou-xfe5-a8hb8ml8)"
    },
    {
      key: "SSOUserEmail",
      value: "abc@amazon.com"
    },
    {
      key: "SSOUserFirstName",
      value: "John"
    },
    {
      key: "SSOUserLastName",
      value: "Smith"
    }
  ],
  provisionToken: "c3c795a1-9824-4fb2-a4c2-4b1841be4068"
}
```

For more information, see the [API reference for Service Catalog](https://docs.aws.amazon.com/servicecatalog/latest/dg/API_ProvisionProduct.html)\.

**Note**  
Notice that the format of the input string for the value of `ManagedOrganizationalUnit` has changed from `OU_NAME` to `OU_NAME (OU_ID)`\. The video that follows does not mention this change\.

## Video Walkthrough<a name="automated-provisioning-video"></a>

This video \(6:58\) describes how to automate account deployments in AWS Control Tower\. For better viewing, select the icon at the lower right corner of the video to enlarge it to full screen\. Captioning is available\.