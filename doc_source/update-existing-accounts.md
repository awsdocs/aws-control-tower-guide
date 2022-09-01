# Update existing OUs and accounts<a name="update-existing-accounts"></a>

When you perform a landing zone update, you must update your enrolled accounts to apply new guardrails to those accounts\.
+ You can perform an update to all accounts under an OU using the **Re\-Register** option\.
+ If you have more than one registered OU in your landing zone, re\-register all of your OUs to update all of your accounts\.
+ To update a single account, you can update them in the AWS Control Tower console, or you can select the **Update provisioned product** option in AWS Service Catalog\.

## What happens during re\-registration<a name="effects-of-re-registering"></a>

**When you re\-register an OU:**
+ The **State** field indicates whether the account currently is enrolled with AWS Control Tower \(**Enrolled**\), whether the account has never been enrolled \(**Not enrolled**\), or whether enrollment failed previously \(**Enrollment failed**\)\.
+ When you re\-register the OU, the `AWSControlTowerExecution` role is added to all accounts with status **Not enrolled** or **Enrollment failed**\.
+ AWS Control Tower creates a single sign\-on \(AWS SSO\) login for those new enrolled accounts\.
+ **Enrolled** accounts are re\-enrolled into AWS Control Tower\.
+ Drift on any preventive guardrails applied to the OU is fixed\.
+ All accounts are updated to reflect the latest landing zone changes\.

For more information, see [Enroll an existing AWS account](enroll-account.md)\.

**Tip**  
When you re\-register an OU, or when you're updating your landing zone version and multiple member accounts, you may see a failure message mentioning the **StackSet\-AWSControlTowerExecutionRole**\. This StackSet in the management account can fail because the **AWSControlTowerExecution** IAM role already exists in all enrolled member accounts\. This error message is expected behavior, and it can be disregarded\.