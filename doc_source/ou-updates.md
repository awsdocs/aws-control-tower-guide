# Update organizations<a name="ou-updates"></a>

The quickest way to update an organizational unit \(OU\) or to update multiple accounts within an OU is to **Re\-register** the OU\.

## What happens during re\-registration<a name="effects-of-re-registering"></a>

**When you re\-register an OU:**
+ The **State** field indicates whether the account currently is enrolled with AWS Control Tower \(**Enrolled**\), whether the account has never been enrolled \(**Not enrolled**\), or whether enrollment failed previously \(**Enrollment failed**\)\.
+ When you re\-register the OU, the `AWSControlTowerExecution` role is added to all accounts with status **Not enrolled** or **Enrollment failed**\.
+ AWS Control Tower creates a single sign\-on \(IAM Identity Center\) login for those new enrolled accounts\.
+ **Enrolled** accounts are re\-enrolled into AWS Control Tower\.
+ Drift on any preventive controls applied to the OU is fixed, because the SCPs are returned to their default definitions\.
+ All accounts are updated to reflect the latest landing zone changes\.

For more information, see [Enroll an existing AWS account](enroll-account.md)\.

**Tip**  
When you re\-register an OU, or when you're updating your landing zone version and multiple member accounts, you may see a failure message mentioning the **StackSet\-AWSControlTowerExecutionRole**\. This StackSet in the management account can fail because the **AWSControlTowerExecution** IAM role already exists in all enrolled member accounts\. This error message is expected behavior, and it can be disregarded\.