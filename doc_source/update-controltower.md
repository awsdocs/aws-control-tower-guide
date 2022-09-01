# Update Your Landing Zone<a name="update-controltower"></a>

The easiest way to update your AWS Control Tower landing zone is through the **Landing zone settings** page, which you can reach by choosing **Landing zone settings** in the left navigation of the AWS Control Tower dashboard\.

The **Landing zone settings** page shows you the current version of your landing zone, and it lists any updated versions that may be available\. You can choose the **Update** button if you need to update your version\. 

**Note**  
Alternatively, you can update your landing zone manually\. The update takes approximately the same amount of time, whether you use the **Update** button or the manual process\. To perform a manual update of your landing zone only, see steps 1 and 2 that follow\.

## Manual updates<a name="manual-update"></a>

The following procedure walks you through the steps of a full update for AWS Control Tower manually\. To update an individual account, see [Update the account in the console](updating-account-factory-accounts.md#update-account-in-console)\.

**To update your landing zone manually, with any number of accounts per OU**

1. Open a web browser, and navigate to the AWS Control Tower console at [https://console\.aws\.amazon\.com/controltower/home/update](https://console.aws.amazon.com/controltower/home/update)\.

1. Review the information in the wizard and choose **Update**\. This updates the backend of the landing zone as well as your shared accounts\. This process can take a little more than half an hour\.

1. Update your member accounts \(this procedure must be followed for an OU that contains over 300 accounts\)\. 

1. From the left navigation pane, choose **Organization**\.

1. To update each account, follow the steps given in [Update the account in the console](updating-account-factory-accounts.md#update-account-in-console)\.

**Optionally Re\-register OU to update accounts**  
For registered AWS Control Tower OUs with fewer than 300 accounts, you can go to the **OU page** in the dashboard and select **Re\-register OU** to update the accounts in that OU\.