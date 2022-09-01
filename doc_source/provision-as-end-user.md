# Provision Account Factory accounts with AWS Service Catalog<a name="provision-as-end-user"></a>

The following procedure describes how to create and provision accounts as an AWS SSO end user, through AWS Service Catalog\. This procedure also is referred to as *advanced account provisioning*, or *manual account provisioning*\. Optionally, you may be able to provision accounts programmatically, with the AWS CLI or with AWS Control Tower Account Factory for Terraform \(AFT\)\.

**To provision accounts individually in Account Factory, as an end user**

1. Sign in from your user portal URL\.

1. From **Your applications**, choose **AWS Account**\.

1. From the list of accounts, choose the account ID for your management account\. This ID may also have a label, for example, **\(Management\)**\. 

1. From **AWSServiceCatalogEndUserAccess**, choose **Management console**\. This opens the AWS Management Console for this user in this account\.

1. Ensure that you've selected the correct AWS Region for provisioning accounts, which should be your AWS Control Tower home region\.

1. Search for and choose **Service Catalog** to open the AWS Service Catalog console\.

1. From the navigation pane, choose **Products**\.

1. Select **AWS Control Tower Account Factory**, then choose the **Launch product** button\. This selection starts the wizard to provision a new account\.

1. Fill in the information, and keep the following in mind:
   + The **SSOUserEmail** can be a new email address, or the email address associated with an existing AWS SSO user\. Whichever you choose, this user will have administrative access to the account you're provisioning\.
   + The **AccountEmail** must be an email address that isn't already associated with an AWS account\. If you used a new email address in **SSOUserEmail**, you can use that email address here\.

1. Do not define **TagOptions** and do not enable **Notifications**, otherwise the account can fail to be provisioned\. When you're finished, choose **Launch product**\.

1. Review your account settings, and then choose **Launch**\. Do not create a resource plan, otherwise the account will fail to be provisioned\.
1. Your account is now being provisioned\. It can take a few minutes to complete\. You can refresh the page to update the displayed status information\.
**Note**  
Only one account can be provisioned at a time\.
