# Govern Resource Configurations with AWS Config<a name="config"></a>

AWS Config provides a detailed view of the resources associated with your AWS account, including how they are configured, how they are related to one another, and how the configurations and their relationships have changed over time\. For more information, see *[AWS Config Developer Guide](https://docs.aws.amazon.com/config/latest/developerguide/)*\.

AWS Config resources provisioned by AWS Control Tower are tagged automatically with `aws-control-tower` and a value of `managed-by-control-tower`\.

AWS Control Tower uses AWS Config Rules with some guardrails\. For more information, see [Guardrails in AWS Control Tower](guardrails.md)\. 