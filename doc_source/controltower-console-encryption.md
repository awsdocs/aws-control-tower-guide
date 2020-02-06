# Data Protection in AWS Control Tower<a name="controltower-console-encryption"></a>

AWS Control Tower conforms to the AWS shared responsibility model, which includes regulations and guidelines for data protection\. AWS is responsible for protecting the global infrastructure that runs all the AWS services\. AWS maintains control over data hosted on this infrastructure, including the security configuration controls for handling customer content and personal data\. AWS customers and APN partners, acting either as data controllers or data processors, are responsible for any personal data that they put in the AWS Cloud\.

For data protection purposes, we recommend that you protect AWS account credentials and set up individual user accounts with AWS Identity and Access Management \(IAM\), so that each user is given only the permissions necessary to fulfill their job duties\. We also recommend that you secure your data in the following ways:
+ Use multi\-factor authentication \(MFA\) with each account\.
+ Use SSL/TLS to communicate with AWS resources\.
+ Set up API and user activity logging with CloudTrail\. This is handled automatically in AWS Control Tower when you set up your landing zone\.
+ Use AWS encryption solutions, along with all default security controls within AWS services\.
+ Use advanced managed security services such as Amazon Macie, which assists in discovering and securing personal data that is stored in Amazon S3\.

We strongly recommend that you never put sensitive identifying information, such as your customers' account numbers, into free\-form fields such as a **Name** field\. This includes when you work with AWS Control Tower or other AWS services using the console, API, AWS CLI, or AWS SDKs\. Any data that you enter into AWS Control Tower or other services might get picked up for inclusion in diagnostic logs\. When you provide a URL to an external server, don't include credentials information in the URL to validate your request to that server\.

For more information about data protection, see the [AWS Shared Responsibility Model and GDPR](https://aws.amazon.com/blogs/security/the-aws-shared-responsibility-model-and-gdpr) blog post on the *AWS Security Blog*\. AWS Control Tower provides the following options that you can use to help secure the content that exists in your landing zone:

**Topics**
+ [Encryption at Rest](#data-protection-encryption-rest)
+ [Encryption in Transit](#data-protection-encryption-in-transit)
+ [Restrict Access to Content](#data-protection-restrict-access)

## Encryption at Rest<a name="data-protection-encryption-rest"></a>

AWS Control Tower uses Amazon S3 buckets and Amazon DynamoDB databases that are encrypted at rest by using Amazon S3\-Managed Keys \(SSE\-S3\) in support of your landing zone\. This encryption is configured by default when you set up your landing zone\. You can also establish encryption at rest for the services you use in your landing zone for the services that support it\. For more information, see the security chapter of that service's online documentation\.

## Encryption in Transit<a name="data-protection-encryption-in-transit"></a>

AWS Control Tower uses Transport Layer Security \(TLS\) and client\-side encryption for encryption in transit in support of your landing zone\. In addition, accessing AWS Control Tower requires using the console, which can only be accessed through an HTTPS endpoint\. This encryption is configured by default when you set up your landing zone\.

## Restrict Access to Content<a name="data-protection-restrict-access"></a>

As a best practice, you should restrict access to the appropriate subset of users\. With AWS Control Tower, you can do this by ensuring your central cloud administrators and end users have the right IAM permissions or, in the case of AWS SSO users, are in the correct groups\.
+ For more information about roles and policies for IAM entities, see *[IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)*\.
+ For more information about the AWS SSO groups that are created when you set up your landing zone, see [AWS SSO Groups for AWS Control Tower](sso.md#sso-groups)\.