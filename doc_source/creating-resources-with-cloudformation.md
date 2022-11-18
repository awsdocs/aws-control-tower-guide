# Creating AWS Control Tower resources with AWS CloudFormation<a name="creating-resources-with-cloudformation"></a>

AWS Control Tower is integrated with AWS CloudFormation, a service that helps you to model and set up your AWS resources so that you can spend less time creating and managing your resources and infrastructure\. You create a template that describes all the AWS resources that you want, such as `AWS::ControlTower::EnabledControl` \(such as controls\)\. AWS CloudFormation provisions and configures those resources for you\. 

When you use AWS CloudFormation, you can reuse your template to set up your AWS Control Tower resources consistently and repeatedly\. Describe your resources once, and then provision the same resources over and over in multiple AWS accounts and Regions\. 

## AWS Control Tower and AWS CloudFormation templates<a name="working-with-templates"></a>

To provision and configure resources for AWS Control Tower and related services, you must understand [AWS CloudFormation templates](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/template-guide.html)\. Templates are formatted text files in JSON or YAML\. These templates describe the resources that you want to provision in your AWS CloudFormation stacks\. If you're unfamiliar with JSON or YAML, you can use AWS CloudFormation Designer to help you get started with AWS CloudFormation templates\. For more information, see [What is AWS CloudFormation Designer?](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/working-with-templates-cfn-designer.html) in the *AWS CloudFormation User Guide*\.

AWS Control Tower supports creating `AWS::ControlTower::EnabledControl` \(control resources\)  in AWS CloudFormation\. For more information, including examples of JSON and YAML templates for `AWS::ControlTower::EnabledControl`, see the [AWS Control Tower resource type reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/aws-resource-SERVICE-RESOURCETYPE.html) in the *AWS CloudFormation User Guide*\.

**Note**  
The limit for `EnableControl`and `DisableControl` updates in AWS Control Tower is 10 concurrent operations\.

To view some AWS Control Tower examples for the CLI and the console, see [Enable controls with AWS CloudFormation](enable-controls.md)\.

## Learn more about AWS CloudFormation<a name="learn-more-cloudformation"></a>

To learn more about AWS CloudFormation, see the following resources:
+ [AWS CloudFormation](http://aws.amazon.com/cloudformation/)
+ [AWS CloudFormation User Guide](https://docs.aws.amazon.com/AWSCloudFormation/latest/UserGuide/Welcome.html)
+ [AWS CloudFormation API Reference](https://docs.aws.amazon.com/AWSCloudFormation/latest/APIReference/Welcome.html)
+ [AWS CloudFormation Command Line Interface User Guide](https://docs.aws.amazon.com/cloudformation-cli/latest/userguide/what-is-cloudformation-cli.html)