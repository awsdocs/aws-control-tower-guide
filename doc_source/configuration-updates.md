# Configuration Update Management in AWS Control Tower<a name="configuration-updates"></a>

It is the responsibility of the members of central cloud administrators to keep your landing zone updated\. Updating your landing zone ensures that AWS Control Tower is patched and updated\. In addition, to protect your landing zone from potential compliance issues, the members of the central cloud administrator team should resolve drift issues as soon as they're detected and reported\.

**Note**  
 The AWS Control Tower console indicates when your landing zone needs to be updated\. If you don't see an option to update, your landing zone is already up to date\.

## Updating Your Landing Zone<a name="update-controltower"></a>

The easiest way to update your AWS Control Tower landing zone is through the **Settings** page\. Navigate to the **Settings** page by choosing **Settings** in the left navigation\.

The **Settings** page shows you the current version of your landing zone, and it lists any updated versions that may be available\. You can choose the **Update** button if you need to update your version\. If the **Update** button appears greyed\-out, you do not need to update\.

**Note**  
Alternatively, you can update your landing zone manually\. The update takes approximately the same amount of time, whether you use the **Update ** button or the manual process\.

The following procedure walks you through the steps of updating your landing zone manually\.

**To update your landing zone manually**

1. Open a web browser, and navigate to the AWS Control Tower console at [https://us\-west\-2\.console\.aws\.amazon\.com/controltower/home/update](https://console.aws.amazon.com/controltower/home/update)\.

1. Review the information in the wizard and choose **Update**\. This updates the backend of the landing zone as well as your shared accounts\. This process can take a little more than an hour\.

1. Update your member accounts\. From the navigation pane, choose **Accounts**\.

1. Choose **Provision new account** to open the AWS Service Catalog console and the Account Factory product\.

1. From the navigation pane, choose **Provisioned products list**\.

1. For each account listed, perform the following steps to update all your member accounts:

   1. From the menu for the account, choose **Provisioned product details**\.

   1. Make a note of the following parameters:
      + SSOUserEmail
      + AccountEmail
      + SSOUserFirstName
      + SSOUSerLastName
      + AccountName

   1. From **ACTIONS**, choose **Update**\.

   1. Choose the radio button next to the **Version** of the product you want to update, and choose **NEXT**\.

   1. Provide the parameter values that were mentioned previously\. For **ManagedOrganizationalUnit** choose the OU that the account is in\. You can find this information in the AWS Control Tower console, under **Accounts**\.

   1. Choose **NEXT**\.

   1. Review your changes, and then choose **UPDATE**\. This process can take a few minutes per account\.

## Resolve Drift<a name="resolve-drift"></a>

When you create your AWS Control Tower landing zone, the landing zone and all the OUs, accounts, and resources are compliant with all of the governance rules enforced by your guardrails, whether mandatory or elective\. As you and your organization members use the landing zone, changes in compliance status may occur\. Some changes may be accidental, and some may be made intentionally to respond to time\-sensitive operational events\. Regardless, changes can complicate your compliance story\.

Resolving drift helps to ensure your organization's compliance with governance regulations\. Drift resolution is a regular operations task for your master account administrators\.

Drift detection is automatic in AWS Control Tower\. It helps you identify resources that need changes or configuration updates that must be made to resolve the drift\.

To repair most types of drift, choose **Repair** on the **Settings** page\. The **Repair** button becomes selectable when drift has occurred\. For more information, see [Detecting and Resolving Drift in AWS Control Tower](drift.md)\.