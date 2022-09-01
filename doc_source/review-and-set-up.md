# Step 3\. Review and set up the landing zone<a name="review-and-set-up"></a>

The next section in the setup shows you the permissions that AWS Control Tower requires for your landing zone\. Choose a checkbox to expand each topic\. You'll be asked to agree to these permissions, which may affect multiple accounts, and to agree to the overall **Terms of Service**\.

**To finalize**

1. At the console, review the **Service permissions**, and when you're ready, choose **I understand the permissions AWS Control Tower will use to administer AWS resources and enforce rules on my behalf**\.

1. To finalize your selections and initialize launch, choose **Set up landing zone**\.

This series of steps starts the process of setting up your landing zone, which can take about thirty minutes to complete\. During setup, AWS Control Tower creates your Root level, the Security OU, and the shared accounts\. Other AWS resources are created, modified, or deleted\.

**Confirm SNS subscriptions**  
The email address you provided for the audit account will receive **AWS Notification – Subscription Confirmation** emails from every AWS Region supported by AWS Control Tower\. To receive compliance emails in your audit account, you must choose the **Confirm subscription** link within each email from each AWS Region supported by AWS Control Tower\. 