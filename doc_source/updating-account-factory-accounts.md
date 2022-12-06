# Update and move account factory accounts with AWS Control Tower or with AWS Service Catalog<a name="updating-account-factory-accounts"></a>

The easiest way to update an enrolled account is through the AWS Control Tower console\. Individual account updates are useful for resolving drift, such as [Moved Member Account](governance-drift.md#drift-account-moved)\. Account updates also are required as part of a full landing zone update\.

If you move an account from one OU to another, remember that the controls applied by the new OU may be different than the controls in the former OU\. Be sure that the controls in the new OU meet your policy requirements for the account\.

## <a name="when-accounts-are-moved"></a>

**Control behavior when accounts are moved between  OUs**

When you move an account between OUs, the controls for the destination OU are applied to the  account\. However, the controls that applied to the account from the former OU are not  removed\. The exact behavior of the controls is specific to the implementation of the  controls that are active on the former OU and the destination OU\.
+  *For controls implemented with AWS Config rules:* The controls from the previous OU  are not removed\. These controls must be removed manually\.
+ *For controls implemented with SCPs:* The controls from the previous OU are not  removed\. These controls must be removed manually\. 
+ *For controls implemented with AWS CloudFormation hooks:* This behavior  depends on the status of controls in the new OU\.
  + *If the destination OU has no hook\-based controls active:* The old  controls remain active for the moved account, unless you remove them  manually\.
  + *If the destination OU has hook controls active:* The old controls are  removed and the controls in the destination OU are applied to the  account\.

## Update the account in the console<a name="update-account-in-console"></a>

**To update an account in the AWS Control Tower console**

1. When signed in to AWS Control Tower, navigate to the **Organization** page\.

1. In the list of OUs and accounts, select the name of the account you wish to update\. Accounts that are available for updating show a status of **Update available**\.

1. Next you'll see the **Account details** page for your selected account\.

1. In the upper right, choose **Update account**\.

## Update the provisioned product<a name="update-provisioned-product"></a>

The following procedure guides you through how to update your Account Factory account or move it to a new OU, by updating the account's provisioned product in AWS Service Catalog\.

**To update an Account Factory account or change its OU through AWS Service Catalog**

1. Sign in to the AWS Management Console and open the AWS Service Catalog console at **https://console\.aws\.amazon\.com/servicecatalog/**
**Note**  
You must be signed in as a user with the permissions to provision new products in AWS Service Catalog; for example, an IAM Identity Center user in either the **AWSAccountFactory** or **AWSServiceCatalogAdmins** groups\.

1. From the navigation pane, choose **Provisioned products**\. 

1. For each account listed, perform the following steps to update all your member accounts:

   1. From the drop\-down menu for the account, choose **Provisioned product details**\.

   1. Make a note of the following parameters:
      + **SSOUserEmail** \(Available in provisioned product details\)
      + **AccountEmail** \(Available in provisioned product details\)
      + **SSOUserFirstName** \(Available in IAM Identity Center\)
      + **SSOUSerLastName** \(Available in IAM Identity Center\)
      + **AccountName** \(Available in IAM Identity Center\)

   1. From **Actions**, choose **Update**\.

   1. Choose the button next to the **Version** of the product you want to update, and choose **Next**\.

   1. Provide the parameter values that were mentioned previously\.
      + If you want to keep the existing OU, for **ManagedOrganizationalUnit**, choose the OU that the account was already in\.
      + If you want to migrate the account to a new OU, for **ManagedOrganizationalUnit**, choose the new OU for the account\.

       A central cloud administrator can find this information in the AWS Control Tower console, on the **Organization** page in AWS Control Tower\.

   1. Choose **Next**\.

   1. Review your changes, and then choose **Update**\. This process can take a few minutes per account\.