# Configure the Region deny control<a name="region-deny"></a>

The Region deny control is unique, because it applies to the landing zone as a whole, rather than to any specific OU\. To configure the Region deny control, go to the **Landing zone settings** page and select **Modify settings**\. 
+ This setting can be changed at a later time\.
+ When enabled, this control applies to all registered OUs\.
+ This control cannot be configured for individual OUs\.

**Note**  
Before you enable the Region deny control, be sure that you do not have existing resources in these Regions, because you will not have access to your resources after you apply the control\. While the control is enabled, you will not be able to deploy resources in the denied Regions\.

The Region deny control prohibits access to AWS services, based on your AWS Control Tower Region configuration\. It denies access to AWS Regions with status **Not Governed**\. The Region deny control also denies access to Regions in which AWS Control Tower is not available\. You cannot deny access to your home Region\. Certain global AWS services, such as IAM and AWS Organizations, are exempt from the Region deny control\. To learn more, see [Deny access to AWS based on the requested AWS Region](data-residency-controls.md#primary-region-deny-policy)\.

When you enable the control, it applies to all registered, top\-level OUs in your hierarchy, and it is inherited by OUs lower in the chain\. When you remove the control, it is removed on all registered OUs, all non\-governed Regions in AWS Control Tower remain in a **Not governed** status, and you can deploy resources in Regions outside of AWS Control Tower availability\.
+ Full control name: **Deny access to AWS based on the requested AWS Region**
+ Guardrail description: Disallows access to unlisted operations in global and regional services outside of the specified Regions\.
+ This is an elective control with preventive guidance\.

To view the template for the Region deny control SCP, see [Deny access to AWS based on the requested AWS Region](data-residency-controls.md#primary-region-deny-policy) in the *AWS Control Tower Guardrail reference*\. The AWS Control Tower SCP is similar to [the SCP for AWS Organizations](https://docs.aws.amazon.com/organizations/latest/userguide/orgs_manage_policies_scps_examples_general.html#example-scp-deny-region), but not identical\.

You can determine Regional service endpoints on the [Regional services page](http://aws.amazon.com/about-aws/global-infrastructure/regional-product-services)\.