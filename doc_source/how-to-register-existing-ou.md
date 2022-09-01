# Register an existing OU<a name="how-to-register-existing-ou"></a>

In the AWS Control Tower console, on the **Organization** page, you can view all of of your organization's OUs and accounts in a hierarchy, including OUs that are registered with AWS Control Tower, and those that are not registered\.

In general, unregistered OUs were created in AWS Organizations, and they are not governed by any other landing zone\. You can register existing OUs that contain up to 300 accounts\. If an OU contains more than 300 accounts, you cannot register it in AWS Control Tower\.

**To register an existing OU**

1. Sign in to the AWS Control Tower console at [https://console\.aws\.amazon\.com/controltower](https://console.aws.amazon.com/controltower)\. 

1. In the left\-pane navigation menu, choose **Organization**\.

1. On the **Organization** page, select the radio button next to the OU you want to register, then select **Register organizational unit** from the **Actions** dropdown menu at the upper right, or alternatively, select the name of the OU so you can view the **OU details** page for that OU\.

1. On the **OU details** page, at the upper right you can select **Register OU** from the **Actions** dropdown menu\.

The registration process takes a minimum of 10 minutes to extend governance to the OU, and up to 2 additional minutes for each additional account\.

**Results of registering an existing OU**

After you register an existing OU, the `AWSControlTowerExecution` role allows AWS Control Tower to extend governance to its individual accounts\. Guardrails are enforced, and information about account activities is reported to your audit and logging accounts\.

Other results include the following:
+ `AWSControlTowerExecution` allows auditing by the AWS Control Tower audit account\.
+ `AWSControlTowerExecution` helps you configure your organization’s logging, so that all the logs for every account are sent to the logging account\.
+ `AWSControlTowerExecution` ensures that your selected AWS Control Tower guardrails apply automatically to every individual account in your OUs, as well as to every new account you create in AWS Control Tower\.

For a registered OU, you can provide compliance and security reports based on the auditing and logging features embodied by AWS Control Tower guardrails\. Your security and compliance teams can verify that all requirements are met, and that no organizational drift has occurred\. For more information about drift, see [Detect and resolve drift in AWS Control Tower](drift.md)\.

**Note**  
One unusual situation can occur when AWS Control Tower displays OUs and their accounts\. If you have created an account in a registered OU and then you subsequently move that enrolled account into another OU that’s not registered, particularly if you use AWS Organizations to move the account, you can see a result “1 of 0” accounts in your OU details page\. Furthermore, you may have created another unenrolled account in that unregistered OU\. If there’s an unregistered account, the console may read “1 of 1” for the OU\. It will seem that the single \(newly created\) account is enrolled, but in fact it is not\. You must enroll the new account\.