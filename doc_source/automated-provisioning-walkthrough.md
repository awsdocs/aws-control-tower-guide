# Walkthrough: Automated Account Provisioning in AWS Control Tower<a name="automated-provisioning-walkthrough"></a>

AWS Control Tower is integrated with several other AWS services, such as AWS Service Catalog\. You can use the APIs to create and provision your member accounts in AWS Control Tower\.

The video shows you how to provision accounts in an automated, batch fashion, by calling the AWS Service Catalog APIs\. For provisioning, you'll call the [https://docs.aws.amazon.com/servicecatalog/latest/dg/API_ProvisionProduct.html](https://docs.aws.amazon.com/servicecatalog/latest/dg/API_ProvisionProduct.html) API from the AWS command line interface \(CLI\), and you'll specify a JSON file that contains the parameters for each account you'd like to set up\. The video illustrates installing and using the [AWS Cloud9](https://docs.aws.amazon.com/cloud9/latest/user-guide/welcome.html) development environment to perform this work\.

**Note**  
You also can use this approach for automating account updates, by calling the [https://docs.aws.amazon.com/servicecatalog/latest/dg/API_UpdateProvisionedProduct.html](https://docs.aws.amazon.com/servicecatalog/latest/dg/API_UpdateProvisionedProduct.html) API of AWS Service Catalog for each account\. You can write a script to update the accounts, one by one\.


Here is a sample template you can use to help configure your automation administration role in the master account\.


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

Here is a sample template you can use to help you set up your automation execution role\. 

```
AWSTemplateFormatVersion: "2010-09-09"
Description: "Create automation execution role for creating Sample Additional Role."

Parameters:
  AdminAccountId:
    Type: "String"
    Description: "Account ID for the administrator account (typically master, security or shared services)."
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

## Video Walkthrough<a name="automated-provisioning-video"></a>

This video \(7:08\) describes how to automate account deployments in AWS Control Tower\. For better viewing, select the icon at the lower right corner of the video to enlarge it to full screen\. Captioning is available\.
