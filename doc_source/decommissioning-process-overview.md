# Overview of the decommissioning process<a name="decommissioning-process-overview"></a>

When you request decommissioning of your landing zone, AWS Control Tower does the following actions\.
+ Disables each detective guardrail enabled in the landing zone\. AWS Control Tower deletes the AWS CloudFormation resources supporting the guardrail\.
+ Disables each preventive guardrail by removing service control policies \(SCPs\) from AWS Organizations\. If a policy is empty \(which it should be after removing all SCPs managed by AWS Control Tower\), AWS Control Tower detaches and deletes the policy entirely\.
+ Deletes all blueprints deployed as CloudFormation StackSets\.
+ Deletes all blueprints deployed as CloudFormation Stacks across all Regions\.
+ For each provisioned account, AWS Control Tower does the following actions during the decommissioning process\.
  + Deletes records of each account factory account\. 
  + Revokes the AWS Control Tower permissions to the account by removing the IAM role that AWS Control Tower created \(unless additional policies have been added to it\) and recreates the standard `OrganizationsFullAccessRole` IAM role\.
  + Removes records of the account from AWS Service Catalog\.
  + Removes the account factory product and portfolio from AWS Service Catalog\.
+ Deletes the blueprints for the core \(logging and audit\) accounts\. 
+ Revokes the AWS Control Tower permissions from the core accounts by removing the IAM role that AWS Control Tower created \(unless additional policies have been added to it\) and recreates the `OrganizationsFullAccessRole` IAM role\.
+ Deletes records related to the core accounts\.
+ Deletes records related to customer\-created OUs\.
+ Deletes internal records that identify the home Region\.

**Note**  
After decommissioning, you may wish to remove the Account Factory VPC blueprint \(`BP_ACCOUNT_FACTORY_VPC`\) to clean up the routes and NAT gateways, if your VPC was not empty\. 