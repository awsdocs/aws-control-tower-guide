# Account customizations<a name="aft-account-customization-options"></a>

AFT can deploy standard or customized configurations in provisioned accounts\. In the AFT management account, AFT provides one pipeline for each account\. With this pipeline, you can implement your customizations in all accounts, in a set of accounts, or in individual accounts\. You can run Python scripts, bash scripts, and Terraform configurations, or you can interact with the AWS CLI as part of your account customizations stage\.

## Overview<a name="aft-customizations-overview"></a>

After your customizations are specified in your chosen `git` repositories, either the one where you store your global customizations or where you store your account customizations, the account customizations stage is completed automatically by the AFT pipeline\. To customize accounts retroactively, see [Re\-invoke customizations](#aft-re-invoke-customizations)\.

**Global customizations \(optional\)**

You can choose to apply certain customizations to all accounts that are provisioned by AFT\. For example, if you need to create a particular IAM role, or to deploy a custom guardrail in every account, the global customizations stage in AFT pipeline allows you to do so, automatically\.

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

 To apply account customizations, you must push a specific folder structure to your chosen repository\.

**Note**  
Account customizations are applied automatically, after the global customizations stage in the AFT pipeline\.

**Step 1: Create a folder for specific account customization**

First, locate the sample `ACCOUNT_TEMPLATE` folder \(provided by AFT\) in your chosen repository\. You can copy that sample to a new folder with the name of your specific account customization, depending upon your organizational requirements\. For example, **prod\-customizations** or **finance\-customizations**\.

**Step 2: Add configurations for your specific account customizations folder**
+ If your custom configurations are in the form of Python programs or scripts, place those under ***\[account\_customizations\_name\]*/api\_helpers/python** folder in your repository\.
+ If your custom configurations are in the form of Bash scripts, place those under ***\[account\_customizations\_name\]*/api\_helpers** folder in your repository\.
+ If your custom configurations are in the form of Terraform, place those under ***\[account\_customizations\_name\]*/terraform** folder in your repository\.
+ Refer to the account customizations README file for more details on creating custom configurations\.

**Step 3: Refer to the specific **account\_customizations\_name** parameter in the account request input file**

The AFT account request file provides an input parameter where you can specify the account customizations you wish to apply, after each account has been provisioned by means of the AFT pipeline\. Enter the name of your specific account customizations folder as the value of the **account\_customizations\_name** parameter, in your account request input file, as shown in [Post\-deployment steps](aft-post-deployment.md)\.

To apply similar account customizations to multiple accounts in your environment, you may refer to the **account\_customizations\_name** input parameter in multiple account requests\.

**Multiple specific customizations**

You can create multiple folders in your account customizations repository, which can set up a variety of specific account customizations\. For each specific account customization implementation that you require, follow the steps given\.

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
      "target_value": [ "acc1","acc2"]
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
      "target_value": [ "acc1","acc2"]
    }
  ]
}
```

As shown in this example event schema, you can choose accounts to include or exclude from the re\-invoke process\. You can filter by account ID, organizational unit \(OU\), and account tags\. If you don’t apply any filters and include the statement `"type":"all"`, the customization for all AFT\-provisioned accounts is re\-invoked\.

After the event parameters have been filled out, the AWS Step Function runs\. It invokes the corresponding customizations\.

**Note**  
AFT can invoke a maximum of 5 customizations at a time\. The step function waits and loops until all accounts matching the event criteria are complete\.

**Step 3: Monitor the AWS Step Function output and watch AWS CodePipeline running**
+ The resulting Step Function output contains account IDs that match the Step Function input event source\.
+ Navigate to AWS CodePipeline under **Developer Tools** and view the corresponding customization pipelines for the account ID\.