# Account customizations<a name="aft-account-customization-options"></a>

AFT can deploy standard or customized configurations in provisioned accounts\. In the AFT management account, AFT provides one pipeline for each account\. With this pipeline, you can implement your customizations in all accounts, in a set of accounts, or in individual accounts\. You can run Python scripts, bash scripts, and Terraform configurations, or you can interact with the AWS CLI as part of your account customizations stage\.

## Overview<a name="aft-customizations-overview"></a>

After your customizations are specified in your chosen `git` repositories, either the one where you store your global customizations or where you store your account customizations, the account customizations stage is completed automatically by the AFT pipeline\. To customize accounts retroactively, see [Re\-invoke customizations](#aft-re-invoke-customizations)\.

**Global customizations \(optional\)**

You can choose to apply certain customizations to all accounts that are provisioned by AFT\. For example, if you need to create a particular IAM role, or to deploy a custom control in every account, the global customizations stage in AFT pipeline allows you to do so, automatically\.

**Account customizations \(optional\)**

To customize an individual account, or a set of accounts, differently than other AFT provisioned accounts, you can leverage the account customizations portion of the AFT pipeline to implement account\-specific configurations\. For example, only a certain account may require access to an internet gateway\. 

## Customization prerequisites<a name="aft-account-customization-prerequisites"></a>

Before you begin to customize accounts, be sure these prerequisites are in place\.
+ A fully deployed AFT\. For information about how to deploy, see [Configure and launch your AWS Control Tower Account Factory for Terraform](aft-getting-started.md#aft-configure-and-launch)\.
+ Pre\-populated `git` repositories for global customizations and account customizations in your environment\. See *Step 3: Populate each repository* in [Post\-deployment steps](aft-post-deployment.md) for more information\.

## Apply global customizations<a name="aft-global-customizations"></a>

To apply global customizations, you must push a specific folder structure to your chosen repository\.
+ If your custom configurations are in the form of Python programs or scripts, place those under **api\_helpers/python** folder in your repository\.
+ If your custom configurations are in the form of Bash scripts, place those under **api\_helpers** folder in your repository\.
+ If your custom configurations are in the form of Terraform, place those under the **terraform** folder in your repository\.
+ Refer to the global customizations README file for more details on creating custom configurations\.

**Note**  
Global customizations are applied automatically, after the AFT account provisioning framework stage in the AFT pipeline\.

## Apply account customizations<a name="aft-account-customizations"></a>

****

 You can apply account customizations by pushing a specific folder structure to your chosen repository\. Account customizations are applied automatically in the AFT pipeline and after the global customizations stage\. You can also create multiple folders that contain different account customizations in your account customizations repository\. For each account customization that you require, use the following steps\. 

**To apply account customizations**

1.  ** Step 1: Create a folder for an account customization ** 

    In your chosen repository, copy the `ACCOUNT_TEMPLATE` folder that AFT provides to a new folder\. The name of your new folder should match the `account_customizations_name` that you provide in your account request\. 

1.  ** Add the configurations to your specific account customizations folder ** 

    You can add configurations to your account customizations folder based on the format of your configurations\. 
   +  If your custom configurations are in the form of Python programs or scripts, place them under the ***\[account\_customizations\_name\]*/api\_helpers/python** folder that's in your repository\. 
   +  If your custom configurations are in the form of Bash scripts, place them under the ***\[account\_customizations\_name\]*/api\_helpers** folder that's in your repository\. 
   +  If your custom configurations are in the form of Terraform, place them under the ***\[account\_customizations\_name\]*/terraform** folder that's in your repository\. 

    For more information about creating custom configurations, refer to the account customizations README file\. 

1.  ** Refer to the specific `account_customizations_name` parameter in the account request file ** 

    The AFT account request file includes the input parameter `account_customizations_name`\. Enter the name of your account customization as the value for this parameter\. 

**Note**  
 You can submit multiple account requests for accounts in your environement\. When you want to apply different or similar account customizations, specifiy the account customizations using the `account_customizations_name` input parameter in your account requests\. For more information, see [Submit multiple account requests](https://docs.aws.amazon.com/controltower/latest/userguide/aft-multiple-account-requests.html)\. 

## Re\-invoke customizations<a name="aft-re-invoke-customizations"></a>

AFT provides a way to re\-invoke customizations in the AFT pipeline\. This method is useful when you’ve added a new customization step, or when you are making changes to an existing customization\. When you re\-invoke, AFT initiates the customizations pipeline to make changes to the AFT provisioned account\. An event\-source\-based re\-invoke allows you to apply customizations to individual accounts, to all accounts, to accounts according to their OU, or to accounts selected according to tags\.

Follow these three steps to re\-invoke customizations for AFT\-provisioned accounts\.

**Step 1: Push changes to global or account customizations `git` repositories**

You can update your global and account customizations as needed and push changes back to your `git` repositories\. At this point, nothing happens, The customizations pipeline must be invoked by an event source, as explained in the next two steps\.

**Step 2: Start an AWS Step Function run for re\-invoking customizations**

AFT provides an AWS Step Function called `aft-invoke-customizations` in the AFT management account\. The purpose of that function is to re\-invoke the customization pipeline for AFT\-provisioned accounts\.

Here is an example of an event schema \(JSON format\) you can create to pass input to the `aft-invoke-customizations` AWS Step Function\.

```
{
  "include": [
    {
      "type": "all"
    },
    {
      "type": "ous",
      "target_value": [ "ou1","ou2"]
    },
    {
      "type": "tags",
      "target_value": [ {"key1": "value1"}, {"key2": "value2"}]
    },
    {
      "type": "accounts",
      "target_value": [ "acc1_ID","acc2_ID"]
    }
  ],

  "exclude": [
    {
      "type": "ous",
      "target_value": [ "ou1","ou2"]
    },
    {
      "type": "tags",
      "target_value": [ {"key1": "value1"}, {"key2": "value2"}]
    },
    {
      "type": "accounts",
      "target_value": [ "acc1_ID","acc2_ID"]
    }
  ]
}
```

 The example event schema shows that you can choose accounts to include or exclude from the re\-invoke process\. You can filter by organizational unit \(OU\), account tags, and account ID\. If you don’t apply any filters and include the statement `"type":"all"`, the customization for all AFT\-provisioned accounts is re\-invoked\. 

**Note**  
 If your version of AWS Control Tower is 1\.6\.5 or later, you can target nested OUs with the syntax `OU Name (ou-id-1234`\)\. For more information, see the following topic on [GitHub](https://github.com/aws-ia/terraform-aws-control_tower_account_factory/issues/280)\. 

 After you fill out the event parameters, Step Functions runs and invokes the corresponding customizations\. AFT can invoke a maximum of 5 customizations at a time\. Step Functions waits and loops until all accounts matching the event criteria are complete\. 

**Step 3: Monitor the AWS Step Function output and watch AWS CodePipeline running**
+ The resulting Step Function output contains account IDs that match the Step Function input event source\.
+ Navigate to AWS CodePipeline under **Developer Tools** and view the corresponding customization pipelines for the account ID\.

## Troubleshooting with AFT account customization request tracing<a name="aft-customization-request"></a>

 Account customization workflows that are based on AWS Lambda emit logs containing target account and customization request IDs\. AFT allows you to trace and troubleshoot customization requests with Amazon CloudWatch Logs by providing you with CloudWatch Logs Insights queries that you can use to filter CloudWatch Logs related to your customization request by your target account or customization request ID\. For more information, see [Analyzing log data with Amazon CloudWatch Logs](https://docs.aws.amazon.com/AmazonCloudWatch/latest/logs/AnalyzingLogData.html) in the *Amazon CloudWatch Logs User Guide*\. 

**To use CloudWatch Logs Insights for AFT**

1. Open the CloudWatch console at [https://console\.aws\.amazon\.com/cloudwatch/](https://console.aws.amazon.com/cloudwatch/)\.

1.  From the navigation pane, choose **Logs**, and then choose **Logs insights**\. 

1.  Choose **Queries**\. 

1.  Under **Sample queries**, choose **Account Factory for Terraform**, and then select one of the following queries: 
   +  **Customization Logs by Account ID** 
**Note**  
 Make sure to replace *"YOUR\-ACCOUNT\-ID"* with your target account ID\. 

     ```
     fields @timestamp, log_message.account_id as target_account_id, log_message.customization_request_id as customization_request_id, log_message.detail as detail, @logStream
     | sort @timestamp desc
     | filter log_message.account_id == "YOUR-ACCOUNT-ID" and @message like /customization_request_id/
     ```
   +  **Customization Logs by Customization Request ID** 
**Note**  
 Make sure to replace *"YOUR\-CUSTOMIZATION\-REQUEST\-ID"* with your customization request ID\. You can find your customization request ID in the output of the AFT account provisioning framework AWS Step Functions state machine\. For more information about the AFT account provisioning framework, see [AFT account provisioning pipeline](https://docs.aws.amazon.com/controltower/latest/userguide/aft-provisioning-framework.html) 

     ```
     fields @timestamp, log_message.account_id as target_account_id, log_message.customization_request_id as customization_request_id, log_message.detail as detail, @logStream
     | sort @timestamp desc
     | filter log_message.customization_request_id == "YOUR-CUSTOMIZATION-REQUEST-ID"
     ```

1.  After you select a query, make sure to select a time interval, and then choose **Run query**\. 