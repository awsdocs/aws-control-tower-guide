# Create a customized account from a blueprint<a name="create-afc-customized-account"></a>

After you have created custom blueprints, you can start creating custom accounts in AWS Control Tower account factory\. 

**Follow these steps to deploy a custom blueprint when you're creating a new AWS account:**

1. Go to AWS Control Tower in the AWS Management Console\. 

1. Select **Account factory** and **Create account**\.

1. Enter account details such as account name and email address\.

1. Configure IAM Identity Center details with email address and user name\. 

1. Select a registered OU where your account will be added\.

1. Expand the **Account factory customization** section\.

1. Enter the blueprint hub account ID that contains your Service Catalog products and choose **Validate**\.

1. Select the dropdown menu that contains all blueprints from your Service Catalog Product List \(all custom and partner blueprints\)\. Choose a blueprint and corresponding version to deploy\. 

1. If your blueprint contains parameters, these fields are displayed for you to populate\. Default values are pre\-populated\. 

1. Finally, select where you'll deploy your blueprint, either **Home Region** or **All governed Regions**\. Global resources such as Route 53 or IAM, may need to be deployed to a single Region only\. Regional resources, such as Amazon EC2 instances or Amazon S3 buckets, could be deployed to all governed Regions

1. After all fields are completed, select **Create account**\.

You can view the progress of your account provisioning on the **Organization** page\. When your account provisioning is complete, the resources specified by your blueprint are already deployed within it\. To view the details of the account and blueprint, go to the **Account details** page\.