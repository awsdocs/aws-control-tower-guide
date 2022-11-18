# Govern Resource Configurations with AWS Config<a name="config"></a>

AWS Config provides a detailed view of the resources associated with your AWS account, including how they are configured, how they are related to one another, and how the configurations and their relationships have changed over time\. For more information, see *[AWS Config Developer Guide](https://docs.aws.amazon.com/config/latest/developerguide/)*\.

AWS Config resources provisioned by AWS Control Tower are tagged automatically with `aws-control-tower` and a value of `managed-by-control-tower`\.

For more information about how AWS Config monitors and records resources in AWS Control Tower, and how it bills you for them, see [Monitoring resource changes with AWS Config](monitoring-with-config.md)\.

AWS Control Tower uses AWS Config Rules to implement detective controls\. For more information, see [The AWS Control Tower control library](controls.md)\. 