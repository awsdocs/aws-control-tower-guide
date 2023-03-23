# Enroll and customize accounts<a name="enroll-and-customize"></a>

To enroll and customize accounts in the AWS Control Tower console\. 

1. Navigate to the AWS Control Tower console and select **Organization** from the left navigation\.

1. You will see a list of your available accounts\. Identify the account you would like to enroll with a custom blueprint\. The **State** column for that account should reflect the account in a **Not enrolled** status\.

1. Select the radio button to the left of the account and choose the **Actions** dropdown menu, in the top right of the screen\. Here you will select the **Enroll** option\.

1. Complete the **Access configuration** section with the account's IAM Identity Center information\.

1. Select the registered OU where your account will become a member\.

1. Complete the **Account factory customization** section using the same steps as 7\-12 of the **Create account** procedure\. For more information, see [Provision Account Factory accounts with AWS Service Catalog](https://docs.aws.amazon.com/controltower/latest/userguide/provision-as-end-user.html)\. 

You can view the status of your account progress on the **Organization** page\. When your account enrollment is complete, the resources specified by the blueprint are already deployed within it\.