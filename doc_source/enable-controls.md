# Enable controls with AWS CloudFormation<a name="enable-controls"></a>

You can enable guardrail controls with AWS CloudFormation, either through the AWS CloudFormation console, or through the AWS CLI\. This section gives an example of each type\.

## Create the stack through AWS CloudFormation<a name="enable-controls-in-cfn"></a>

You can use AWS CloudFormation to help you enable AWS Control Tower guardrails\. Here's an example template\.

```
Resources:
    TestControl:
        Type: AWS::ControlTower::EnabledControl
        Properties:
            ControlIdentifier: arn:aws:controltower:us-west-2::control/AWS-GR_RESTRICT_ROOT_USER
            TargetIdentifier: arn:aws:organizations::123456789012:ou/o-ybfpt9XXXl/ou-XXXc-nlqXXXXX
```

To create your stack through the AWS CloudFormation console, edit the template to contain the control and target of your choice, then save the template with the file name `template.yaml`\. Follow the AWS CloudFormation wizard\. When the wizard asks for a template file, enter the file you saved as `template.yaml`\. For more information, see [Creating a stack on the Amazon CloudFormation console](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/cfn-console-create-stack.html)\.

**Note**  
The limit for `EnableControl` and `DisableControl` updates in AWS Control Tower is 10 concurrent operations\.

## Create the stack through AWS CloudFormation and the AWS CLI<a name="enable-controls-in-cli"></a>

Here's an example of creating the stack with the CLI\.

```
aws cloudformation create-stack --region us-west-2 --stack-name testControlTower --template-body "$(cat << TEMPLATE
Resources:
  TestControl:
    Type: AWS::ControlTower::EnabledControl
    Properties:
      ControlIdentifier: arn:aws:controltower:us-west-2::control/AWS-GR_RESTRICT_ROOT_USER
      TargetIdentifier: arn:aws:organizations::123456789012:ou/o-ybfpt9XXXl/ou-XXXc-nlqXXXXX
TEMPLATE)"
```

You can also save the example template as a `template.yaml` file, then upload your file to an Amazon S3 bucket\. Later, you can provide the URL for the bucket with the `--template-url` flag\.

When you enter your template into the wizard or through the CLI, if the stack is created, it means that the control was enabled\.

**View the progress of your stack through the AWS CLI:**

```
aws cloudformation describe-stack-events --region us-west-2 --stack-name testControlTower
```

or

```
aws cloudformation describe-stacks --region us-west-2 --stack-name testControlTower
```

**Delete the stack through the AWS CLI:**

```
aws cloudformation delete-stack --region us-west-2 --stack-name testControlTower
```