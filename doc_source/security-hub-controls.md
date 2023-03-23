# Security Hub standard<a name="security-hub-controls"></a>

AWS Control Tower is integrated with AWS Security Hub to provide detective controls that help you monitor your AWS environment\. The integration is accomplished with a Security Hub standard, called the **Service\-Managed Standard: AWS Control Tower**\.


|  | 
| --- |
|  The AWS Security Hub integration with AWS Control Tower is in preview\. Controls owned by Security Hub are not aggregated in the compliance status of accounts and OUs in AWS Control Tower\. As a precaution, we recommend that you review the Security Hub findings and the AWS Control Tower noncompliant resources before you remediate your accounts and OUs\. To learn more, see [Viewing control status for the Service\-Managed Standard: AWS Control Tower](https://docs.aws.amazon.com/securityhub/latest/userguide/service-managed-standard-aws-control-tower.html#aws-control-tower-standard-control-status) in the Security Hub documentation\. No additional charges apply while you use these new capabilities during the preview\. However, when you set up AWS Control Tower, you incur costs for the AWS services that establish your landing zone and implement mandatory controls\. For more information, see [AWS Control Tower pricing\.](http://aws.amazon.com/controltower/pricing/)   | 

The **Service\-Managed Standard: AWS Control Tower** supports a subset of controls in the **AWS Foundational Security Best Practices \(FSBP\)** standard\. To learn more about this standard and to view the available controls, see [Service\-Managed Standard: AWS Control Tower](https://docs.aws.amazon.com/securityhub/latest/userguide/service-managed-standard-aws-control-tower.html#aws-control-tower-standard-controls)\. For more general information about Security Hub standards, see [Security standards and controls in Security Hub](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-standards.html), in the *AWS Security Hub User Guide*\.

**Note**  
AWS Control Tower does not detect drift for controls that are part of the AWS Security Hub **Service\-Managed Standard: AWS Control Tower**\.

This standard is available only for AWS Control Tower customers who have created the standard in the AWS Control Tower console\. AWS Control Tower creates the standard for you when you enable the first Security Hub control in the AWS Control Tower console\. At this time, if you haven’t already enabled Security Hub, AWS Control Tower also enables Security Hub for you\.

After you create this standard, you can view the Security Hub detective controls alongside other AWS Control Tower controls, in the AWS Control Tower console\. No controls are enabled automatically for this standard\. The Security Hub controls are active at the OU level only, not for all of AWS Control Tower OUs \(if not enabled for all\), and not for individual accounts\. AWS Control Tower does not report compliance for Security Hub controls\.

Based on control status, Security Hub calculates a security score for the **Service\-Managed Standard: AWS Control Tower**\. This security score and the control findings are available only in Security Hub\. These items aren't available in AWS Control Tower\.

**Note**  
When you create **Service\-Managed Standard: AWS Control Tower** and enable controls for it, Security Hub may take up to 18 hours to generate findings for controls that use the same underlying AWS Config service\-linked rule as controls from other enabled Security Hub standards\. For more information, see [Schedule for running security checks](https://docs.aws.amazon.com/securityhub/latest/userguide/securityhub-standards-schedule.html) in the AWS Security Hub User Guide\.

 You can delete this standard in the AWS Control Tower console by deactivating all controls in the standard\. This deletes the standard for all managed accounts and governed Regions in AWS Control Tower\. Deleting the standard does not deactivate Security Hub for your account\.