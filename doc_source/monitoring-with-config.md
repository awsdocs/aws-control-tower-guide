# Monitoring resource changes with AWS Config<a name="monitoring-with-config"></a>

AWS Control Tower enables AWS Config on all enrolled accounts, so that it can monitor compliance with detective guardrails, record resource changes, and deliver resource change logs to the log archive account\.

**If your landing zone version is earlier than 3\.0**: For your enrolled accounts, AWS Config logs all changes to resources, for all Regions in which the account operates\. Each change is modeled as a configuration item \(CI\), which contains information such as the resource identifier, the Region, the date that each change was recorded, and whether the change relates to a known resource or a newly discovered one\.

**If your landing zone version is 3\.0 or later**: AWS Control Tower limits recording for global resources, such as IAM users, groups, roles, and customer managed polices, to your home Region only\. Copies of global resource changes are not stored in every Region\. This limitation of resource recording conforms with AWS Config [best practices](http://aws.amazon.com/blogs/mt/aws-config-best-practices/)\. A [full list of global resources](https://docs.aws.amazon.com/config/latest/developerguide/select-resources.html) is available in AWS Config documentation\.
+ To learn more about AWS Config, see [ How AWS Config works](https://docs.aws.amazon.com/config/latest/developerguide/how-does-config-work.html)\. 
+ For a list of resources that AWS Config can support, see [Supported resource types](https://docs.aws.amazon.com/config/latest/developerguide/resource-config-reference.html)\.
+ To learn about how to customize resource tracking in the AWS Control Tower environment, see the blog post entitled [Customize AWS Config resource tracking in AWS Control Tower](http://aws.amazon.com/blogs/mt/customize-aws-config-resource-tracking-in-aws-control-tower-environment)\.

AWS Control Tower sets up an AWS Config delivery channel in all enrolled accounts\. Through this delivery channel, it logs all changes recorded by AWS Config in the log archive account, where they are stored to a folder in an Amazon Simple Storage Service bucket\.

## Managing AWS Config costs in AWS Control Tower<a name="config-costs"></a>

This section describes how AWS Config records and bills you for changes to resources in your AWS Control Tower accounts\. This information may help you understand how to manage the costs associated with AWS Config, when you're utilizing AWS Control Tower\. AWS Control Tower adds no additional cost\. 

**Note**  
 **If your landing zone version is 3\.0 or later**: AWS Control Tower limits AWS Config recording for global resources, such as IAM users, groups, roles, and customer\-managed polices, to your home Region only\. Therefore, some of the information in this section may not apply to your landing zone\.

AWS Config is designed to record each change to each resource, in each Region where an account operates, as a configuration item \(CI\)\. AWS Config bills you for each configuration item that it generates\.

**How AWS Config operates**

AWS Config records resources in each Region, separately\. Some global resources, such as IAM roles, are recorded once per Region\. For example, if you create a new IAM role in an enrolled account that is operating in five Regions, AWS Config generates five CIs, one for each Region\. Other global resources, such as Route 53 hosted zones, are recorded only once across all Regions\. For example, if you create a new Route 53 hosted zone in an enrolled account, AWS Config generates one CI, regardless of how many Regions are selected for that account\. For a list that helps you distinguish these types of resources, see [The same resource is recorded multiple times](#duplicate-configuration-items)\.

**Note**  
When AWS Control Tower works with AWS Config, a Region may be governed by AWS Control Tower, or ungoverned, and AWS Config still records the changes if the account operates in that Region\.

**AWS Config detects two types of relationships in resources**

AWS Config makes a distinction between *direct* and *indirect* relationships among resources\. If a resource is returned in another resource's **Describe** API call, those resources are recorded as a direct relationship\. When you change a resource in a direct relationship with another resource, AWS Config does not make a CI for both resources\.

For example, if you create an Amazon EC2 instance, and the API requires you to create a network interface, AWS Config considers the Amazon EC2 instance to have a direct relationship with the network interface\. As a result, AWS Config generates only one CI\.

AWS Config records separate changes for resource relationships that are *indirect* relationships\. For example, AWS Config generates two CIs if you create a security group and add an associated Amazon EC2 instance that's part of the security group\.

For more information about direct and indirect relationships, see [What is a direct and an indirect relationship with respect to a resource?](https://docs.aws.amazon.com/config/latest/developerguide/faq.html#faq-0)

You can find [a list of resource relationships](https://docs.aws.amazon.com/config/latest/developerguide/resource-config-reference.html) in the AWS Config documentation\. 

## View the AWS Config recorder data on enrolled accounts<a name="querying-config"></a>

AWS Config is integrated with CloudWatch so that you can view AWS Config CIs in a dashboard\. For more information, see the blog post entitled [AWS Config supports Amazon CloudWatch metrics](http://aws.amazon.com/about-aws/whats-new/2022/05/aws-config-supports-amazon-cloudwatch-metrics)\.

Programmatically, to view AWS Config data, you can work with the AWS CLI, or you can utilize other AWS tools\. 

### Query the AWS Config recorder data on a specific resource<a name="querying-resources-using-the-cli"></a>

You can use the AWS CLI to retrieve a list of the most recent changes for a resource\.

**Resource history command:**
+ `aws configservice get-resource-config-history --resource-type RESOURCE-TYPE --resource-id RESOURCE-ID --region REGION`

To learn more, see [the API documentation for `get-config-history`](https://docs.aws.amazon.com/cli/latest/reference/configservice/get-resource-config-history.html)\.

### Visualize AWS Config data with Amazon QuickSight<a name="visualize-config-data-with-quicksight"></a>

You can visualize and query resources recorded by AWS Config across your entire organization\. For more information, see [Visualizing AWS Config data using Amazon Athena and Amazon QuickSight](http://aws.amazon.com/blogs/mt/visualizing-aws-config-data-using-amazon-athena-and-amazon-quicksight/)\.

## Troubleshooting AWS Config in AWS Control Tower<a name="troubleshooting-config"></a>

This section gives information about some problems you may encounter when using AWS Config with AWS Control Tower\. 

### High AWS Config costs<a name="high-config-costs"></a>

If your workflow includes processes that create, update, or delete resources frequently, or if it handles resources in large numbers, that workflow may generate large numbers of CIs\. If you run these processes in a non\-production account, consider unenrolling the account\. You may need to de\-activate the AWS Config recorder for that account manually\.

**Note**  
After you unenroll the account, AWS Control Tower cannot enforce detective guardrails or log account events, such as AWS Config activities, for resources in that account\.

For more information, see [Unmanage an enrolled account](https://docs.aws.amazon.com/controltower/latest/userguide/unmanage-account.html)\. To learn how to deactivate the AWS Config recorder, see [Managing the configuration recorder](https://docs.aws.amazon.com/config/latest/developerguide/stop-start-recorder.html)\.

### The same resource is recorded multiple times<a name="duplicate-configuration-items"></a>

Check whether the resource is a [global resource](https://docs.aws.amazon.com/config/latest/developerguide/select-resources.html)\. For AWS Control Tower landing zones prior to version 3\.0, AWS Config may record certain global resources once for each Region in which AWS Config is operating\. For example, if AWS Config is enabled on eight Regions, each role is recorded eight times\.

**The following resources are recorded once for each Region in which AWS Config is operating:**
+ `AWS::IAM::Group` 
+ `AWS::IAM::Policy` 
+ `AWS::IAM::Role` 
+  `AWS::IAM::User`

**Other global resources are recorded only once\. Here are some examples of resources that are recorded once:**
+ `AWS::Route53::HostedZone`
+ `AWS::Route53::HealthCheck`
+ `AWS::ECR::PublicRepository`
+ `AWS::GlobalAccelerator::Listener`
+ `AWS::GlobalAccelerator::EndpointGroup`
+ `AWS::GlobalAccelerator::Accelerator`

### AWS Config did not record a resource<a name="resource-not-recorded"></a>

Certain resources have dependency relationships with other resources\. These relationships may be *direct* or *indirect*\. AWS Config no longer records resource changes for certain indirect relationships\. You can find a list of deprecated indirect relationships in [the AWS Config FAQ](https://docs.aws.amazon.com/config/latest/developerguide/faq.html#faq-2)\.