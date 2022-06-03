# Enable guardrails<a name="enable-guardrails"></a>

Most guardrails are enabled automatically according to an OU's configuration, and some guardrails can be enabled manually on your OUs\. The following procedure describes the steps for enabling guardrails on an OU\.

**Important**  
When you enable optional guardrails, AWS Control Tower creates and manages AWS resources in your accounts\. Do not modify or delete resources created by AWS Control Tower\. Doing so could result in the guardrails entering an unknown state\.

**To enable guardrails in an OU**

1. Using a web browser, navigate to the AWS Control Tower console at [https://console\.aws\.amazon\.com/controltower](https://console.aws.amazon.com/controltower)\.

1. From the left navigation, choose **Guardrails**\.

1. Choose a guardrail that you want to enable; for example, **Guardrail: Detect Whether Encryption is Enabled for Amazon EBS Volumes Attached to Amazon EC2 Instances**\. This choice opens the guardrail's details page\.

1. From **Organizational units enabled**, choose **Enable guardrail on OU**\.

1. A new page is displayed that lists the names of your OUs\. Identify the OU on which you want to enable this guardrail\.

1. Choose **Enable guardrail on OU**\.

1. Your guardrail is now enabled\. It may take several minutes for the change to complete\. When it does, you'll see that this guardrail is applied to the OU you selected\.

**Note**  
You can enable preventive and detective guardrails concurrently\.

## Concurrent guardrail deployment<a name="concurrent-optional-guardrails"></a>

When applying optional guardrails, you can deploy more than one guardrail at a time, up to the StackSets limitation of 5000 concurrent operations\.

**Functionality available**
+ Apply and remove different detective guardrails on the same OU, concurrently\. For example, you can enable **Detect Whether MFA for the Root User is Enabled** and **Detect Whether Public Write Access to Amazon S3 Buckets is Allowed**\. 
+ Apply and remove different detective guardrails on different OUs, concurrently\. 
+ Apply and remove the same detective guardrail on multiple OUs, concurrently\.
+ Apply and remove different preventive guardrails on the same OU, concurrently\.
+ Apply and remove different preventive guardrails on different OUs, concurrently\. 
+ Apply and remove the same preventive guardrail on multiple OUs, concurrently\. 
+ You can apply and remove preventive and detective guardrails, concurrently\.

You can apply and remove multiple optional guardrails without waiting for individual guardrail operations to complete\. The only restricted times are when AWS Control Tower is in the process of landing zone setup, or while extending governance to a new organization\.

When you apply preventive guardrails to nested OUs, the preventive guardrails affect all accounts and OUs nested under the target OU, even if those accounts and OUs are not registered with AWS Control Tower\. Preventive guardrails are implemented using Service Control Policies \(SCPs\), which are part of AWS Organizations\. Detective guardrails are implemented using AWS Config rules\. Guardrails remain in effect as you create new accounts or make changes to your existing accounts, and AWS Control Tower provides a summary report of how each account conforms to your enabled policies\. For a full list of available guardrails, see the [Guardrail reference](guardrails-reference.md)\.