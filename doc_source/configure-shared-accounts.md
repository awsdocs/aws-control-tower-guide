# Step 2c\. Configure your shared accounts, logging, and encryption<a name="configure-shared-accounts"></a>

In this section of the setup process, the panel shows the default selections for the names of your shared AWS Control Tower accounts\. These accounts are an essential part of your landing zone\. **Do not move or delete these shared accounts**\. You can choose customized names for the **audit** and **log archive** accounts during setup\. Alternatively, you have a one\-time option to specify existing AWS accounts as your shared accounts\.

You must provide unique email addresses for your log archive and audit accounts, and you can verify the email address that you previously provided for your management account\. Choose the **Edit** button to change the editable default values\.

**About the shared accounts**
+ **The management account** – The AWS Control Tower management account is part of the Root level\. The management account allows for AWS Control Tower billing\. The account also has administrator permissions for your landing zone\. You cannot create separate accounts for billing and for administrator permissions in AWS Control Tower\.

  The email address shown for the management account is not editable during this phase of setup\. It is shown as a confirmation, so you can check that you're editing the correct management account, in case you have multiple accounts\.
+  **The two shared accounts** – You can choose customized names for these two accounts, or bring your own accounts, and you must supply a unique email address for each account, either new or existing\. If you choose to have AWS Control Tower create new shared accounts for you, the email addresses must not already have associated AWS accounts\.

**To configure the shared accounts, fill in the requested information\.**

1. At the console, enter a name for the account initially called the **log archive** account\. Many customers decide to keep the default name for this account\.

1. Provide a unique email address for this account\.

1. Enter a name for the account initially called the **audit** account\. Many customers choose to call it the **Security** account\.

1. Provide a unique email address for this account\.