# Data Protection in AWS Control Tower<a name="controltower-console-encryption"></a>

The AWS [shared responsibility model](http://aws.amazon.com/compliance/shared-responsibility-model/) applies to data protection in AWS Control Tower\. As described in this model, AWS is responsible for protecting the global infrastructure that runs all of the AWS Cloud\. You are responsible for maintaining control over your content that is hosted on this infrastructure\. This content includes the security configuration and management tasks for the AWS services that you use\. For more information about data privacy, see the [Data Privacy FAQ](http://aws.amazon.com/compliance/data-privacy-faq)\. For information about data protection in Europe, see the [AWS Shared Responsibility Model and GDPR](http://aws.amazon.com/blogs/security/the-aws-shared-responsibility-model-and-gdpr/) blog post on the *AWS Security Blog*\.

For data protection purposes, we recommend that you protect AWS account credentials and set up individual user accounts with AWS Identity and Access Management \(IAM\)\. That way each user is given only the permissions necessary to fulfill their job duties\. We also recommend that you secure your data in the following ways:
+ Use multi\-factor authentication \(MFA\) with each account\.
+ Use SSL/TLS to communicate with AWS resources\. We recommend TLS 1\.2 or later\.
+ Set up API and user activity logging with AWS CloudTrail\.
+ Use AWS encryption solutions, along with all default security controls within AWS services\.
+ Use advanced managed security services such as Amazon Macie, which assists in discovering and securing personal data that is stored in Amazon S3\.
+ If you require FIPS 140\-2 validated cryptographic modules when accessing AWS through a command line interface or an API, use a FIPS endpoint\. For more information about the available FIPS endpoints, see [Federal Information Processing Standard \(FIPS\) 140\-2](http://aws.amazon.com/compliance/fips/)\.

We strongly recommend that you never put confidential or sensitive information, such as your customers' email addresses, into tags or free\-form fields such as a **Name** field\. This includes when you work with AWS Control Tower or other AWS services using the console, API, AWS CLI, or AWS SDKs\. Any data that you enter into tags or free\-form fields used for names may be used for billing or diagnostic logs\. If you provide a URL to an external server, we strongly recommend that you do not include credentials information in the URL to validate your request to that server\.

**Note**  
User activity logging with AWS CloudTrail is handled automatically in AWS Control Tower when you set up your landing zone\.

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

As a best practice, you should restrict access to the appropriate subset of users\. With AWS Control Tower, you can do this by ensuring that your central cloud administrators and end users have the right IAM permissions or, in the case of AWS SSO users, that they are in the correct groups\.
+ For more information about roles and policies for IAM entities, see *[IAM User Guide](https://docs.aws.amazon.com/IAM/latest/UserGuide/)*\.
+ For more information about the AWS SSO groups that are created when you set up your landing zone, see [AWS SSO Groups for AWS Control Tower](sso.md#sso-groups)\.