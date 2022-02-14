# Pricing<a name="pricing"></a>

No additional charge exists for using AWS Control Tower\. You only pay for the AWS services enabled by AWS Control Tower, and the services you use in your landing zone\. For example, you pay for AWS Service Catalog for provisioning accounts with Account Factory, and AWS CloudTrail for events tracked in your landing zone\. For information about the pricing and fees associated with AWS Control Tower, see [AWS Control Tower pricing](http://aws.amazon.com/controltower/pricing)\.

If you are running ephemeral workloads from accounts in AWS Control Tower, you may see an increase in costs associated with AWS Config\. For details, see [AWS Config pricing](http://aws.amazon.com/config/pricing)\. Contact your AWS account representative for more specific information about managing these costs\.

## <a name="costs-for-org-trails"></a>

If you implement organization\-level AWS CloudTrail trails outside of AWS Control Tower, you can use them with AWS Control Tower\. However, if you have organization\-level trails, you will incur duplicate charges, because AWS Control Tower requires account\-level trails\. We do not recommend setting up organization\-level trails, unless you have a specific requirement, because AWS Control Tower performs their function by setting up CloudTrail trails for you in every account\. 