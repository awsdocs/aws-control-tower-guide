# Manually add the required IAM role to an existing AWS account and enroll it<a name="enroll-manually"></a>

If you’ve already set up your AWS Control Tower landing zone, you can begin enrolling your organization’s accounts into an OU that is registered with AWS Control Tower\. If you haven't set up your landing zone, follow the steps as described in the AWS Control Tower User Guide at [Getting Started, Step 2](https://docs.aws.amazon.com/controltower/latest/userguide/getting-started-with-control-tower.html#step-two)\. After the landing zone is ready, complete the following steps to bring existing accounts into governance by AWS Control Tower, manually\.

**Be sure to review the [Prerequisites for Enrollment](enroll-account.md#enrollment-prerequisites) noted previously in this chapter\.**

Before enrolling an account with AWS Control Tower, you must give AWS Control Tower permission to manage that account\. To do so, you’ll add a role that has full access to the account, as shown in the steps that follow\. These steps must be performed for each account that you enroll\.

**For each account:**

**Step 1: Sign in with administrator access to the management account of the organization that currently contains the account you wish to enroll\.**

For example, if you created this account from AWS Organizations and you use a cross\-account IAM role to sign in, then you may follow these steps:

1. Sign into your organization’s management account\.

1. Go to **AWS Organizations**\.

1. Under **Accounts**, select the account you want to enroll and copy its account ID\.

1. Open the account dropdown menu on the top navigation bar and choose **Switch Role**\.

1. On the **Switch role** form, fill in the following fields:
   + Under **Account**, enter the account ID you copied\.
   + Under **Role**, enter the name of the IAM role that enables cross\-account access to this account\. The name of this role was defined when the account was created\. If you did not specify a role name when you created the account, enter the default role name, `OrganizationAccountAccessRole`\.

1. Choose **Switch Role**\.

1. You should now be signed into the AWS management console as the child account\.

1. When you’re finished, stay in the child account for the next part of the procedure\.

1. Make note of the management account ID, because you will need to enter it in the next step\.

**Step 2: Give AWS Control Tower permission to manage the account\.**

1. Go to **IAM**\.

1. Go to **Roles**\.

1. Choose **Create role**\.

1. When asked to select which service the role is for, select **EC2** and choose **Next:Permissions**\. You will change this to “AWS Control Tower” later\.

1. When asked to attach policies, choose **AdministratorAccess**\.

1. Choose **Next:Tags**\.

1. You may see an optional screen titled **Add tags**\. Skip this screen for now by choosing **Next:Review**

1. On the **Review** screen, in the **Role name** field, enter `AWSControlTowerExecution`\.

1. Enter a brief description in the **Description** box, such as *Allows full account access for enrollment\.*

1. Choose **Create role**\.

1. Navigate to the role you just created\. Choose **Roles** on the left\. Select `AWSControlTowerExecution`\.

1. Under **Trust relationships**, choose **Edit trust relationship**\.

1. Copy the code example shown here and paste it into the Policy Document\. Replace the string *`Management Account ID`* with the actual management account ID of your management account\. Here is the policy to paste:

   ```
     {
           "Version": "2012-10-17",
           "Statement": [
            {
           "Effect": "Allow",
           "Principal": {
           "AWS": "arn:aws:iam::Management Account ID:root"
                },
               "Action": "sts:AssumeRole",
               "Condition": {}
             }
             ]
            }
   ```

**Step 3: Enroll the account by moving it into a registered OU, and verify enrollment\.**

After you’ve set up the necessary permissions by creating the role, follow these steps to enroll the account and verify enrollment\.

1. **Sign in again as Admin and go to AWS Control Tower\.**

1. 

**Enroll the account\.**
   + From the Account Factory page in AWS Control Tower, choose **Enroll account**\. Fill in the required fields\. Use the email address associated with the account you just updated\.
     + Specify the current email address of the existing account you'd like to enroll in AWS Control Tower\.
     + Specify the first and last name of the account owner\.
     + Specify the organizational unit \(OU\) in which you'd like to enroll the account\.
   + Choose **Enroll account**\.

1. 

**Verify enrollment\.**
   + From AWS Control Tower, choose **Accounts**\.
   + Look for the account you have recently enrolled\. Its initial state will show a status of **Enrolling**\.
   + When the state changes to **Enrolled**, the move was successful\.

To continue this process, sign into each account in your organization that you want to enroll in AWS Control Tower\. Repeat the prerequisite steps and the enrollment steps for each account\.