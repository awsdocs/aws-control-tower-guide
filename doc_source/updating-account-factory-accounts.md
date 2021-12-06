# Updating and moving account factory accounts with AWS Service Catalog<a name="updating-account-factory-accounts"></a>

The following procedure guides you through how to update your Account Factory account or move it to a new OU, through AWS Service Catalog, by updating the provisioned product\.

**To update an Account Factory account or change its OU**

1. Sign in to the AWS Management Console and open the AWS Service Catalog console at **https://console\.aws\.amazon\.com/servicecatalog/**
**Note**  
You must be signed in as a user with the permissions to provision new products in AWS Service Catalog; for example, an AWS SSO user in either the **AWSAccountFactory** or **AWSServiceCatalogAdmins** groups\.

1. From the navigation pane, choose **Provisioned products list**\. 

1. For each account listed, perform the following steps to update all your member accounts:

   1. From the drop\-down menu for the account, choose **Provisioned product details**\.

   1. Make a note of the following parameters:
      + **SSOUserEmail** \(Available in provisioned product details\)
      + **AccountEmail** \(Available in provisioned product details\)
      + **SSOUserFirstName** \(Available in SSO\)
      + **SSOUSerLastName** \(Available in SSO\)
      + **AccountName** \(Available in SSO\)

   1. From **Actions**, choose **Update**\.

   1. Choose the button next to the **Version** of the product you want to update, and choose **Next**\.

   1. Provide the parameter values that were mentioned previously\.
      + If you want to keep the existing OU, for **ManagedOrganizationalUnit**, choose the OU that the account was already in\.
      + If you want to migrate the account to a new OU, for **ManagedOrganizationalUnit**, choose the new OU for the account\.

       A central cloud administrator can find this information in the AWS Control Tower console, under **Accounts**\.

   1. Choose **Next**\.

   1. Review your changes, and then choose **Update**\. This process can take a few minutes per account\.