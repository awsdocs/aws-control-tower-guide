# Walkthrough: Customize Your AWS Control Tower Landing Zone<a name="customize-landing-zone"></a>

You can add customizations to your AWS Control Tower landing zone using an AWS CloudFormation template and service control policies \(SCPs\)\. You can deploy the custom template and policies to individual accounts and organizational units \(OUs\) within your organization\.

This solution integrates with AWS Control Tower [lifecycle events](https://docs.aws.amazon.com/controltower/latest/userguide/lifecycle-events.html) to ensure that resource deployments stay in sync with the landing zone\. For example, when a new account is created using the AWS Control Tower account factory, the solution ensures that all resources attached to the account's OUs are deployed automatically\.

The deployment documentation for this AWS Control Tower solution architecture is available through the [AWS Solutions web page](http://aws.amazon.com/solutions/customizations-for-aws-control-tower/)\.