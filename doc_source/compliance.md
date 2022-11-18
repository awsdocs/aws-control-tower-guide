# Controls and compliance<a name="compliance"></a>

Within AWS Control Tower, compliance refers to the state of a resource, when it is evaluated with respect to a deployed detective control, or a drift detection rule\. Compliance in AWS Control Tower is related to drift — usually, a non\-compliant resource is in a state of drift\. AWS Control Tower controls embody rules of compliance\. They help you identify compliant and non\-compliant resources by helping identify drift\.

When AWS Control Tower evaluates the compliance of resources, it reports the compliance results at the OU, account, and control levels\. This section describes compliance status in detail, for controls, OUs, and accounts\.

Compliance reporting is intended to let cloud administrators know when the resources associated with the accounts in their organization are compliant with established policies\. When the resources are in compliance, builders can provision new AWS accounts quickly in a few clicks\.

When we talk about compliance in AWS Control Tower, we do not intend the same meaning as compliance with governmental regulations, such as data privacy or health information standards\. However, AWS Control Tower can assist your organization to comply with many governmental regulations\.

For more information about how AWS Control Tower helps you maintain compliance with governmental regulations and industry standards, see [Compliance Validation](https://docs.aws.amazon.com/controltower/latest/userguide/compliance-program-info.html)\.

**Examples of compliance rules \(controls\) in AWS Control Tower:**
+ [Detect Whether Public Write Access to Amazon S3 Buckets is Allowed](strongly-recommended-controls.md#s3-disallow-public-write)
+ [Detect Whether Unrestricted Incoming TCP Traffic is Allowed](strongly-recommended-controls.md#rdp-disallow-internet)

**Examples of governmental compliance regulations:**
+ The U\.S\. Health Insurance Portability and Accountability Act of 1996 \(HIPAA\)
+ The European Union’s General Data Protection Regulation of 2016 \(GDPR\)

For ongoing governance, administrators can enable pre\-configured controls—clearly defined rules for security, operations, and compliance\. These controls can:
+ prevent deployment of resources that don’t conform to policies \(by means of preventive controls, implemented with SCPs\)
+ continuously monitor deployed resources for nonconformance \(by means of detective controls, implemented with AWS Config Rules\)