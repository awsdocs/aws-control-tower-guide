# AWS Control Tower compliance status for controls, OUs, and accounts<a name="compliance-statuses"></a>

Compliance is reported in the AWS Control Tower dashboard for controls, accounts, and OUs\. This section lists the possible categories of compliance and non\-compliance in AWS Control Tower, assuming that control controls are enabled for an account or an OU\.
+ **For a control: **A compliance status of **Enforced**, **Clear**, or **In violation** is possible, as long as the control is enabled on your OUs and the member accounts in the OUs\. You can view this control status on individual control detail pages\.
+ **For an account or OU: **A compliance status of **Compliant**, **Noncompliant**, or **Unknown** is possible\. The compliance status refers to the status of the resources associated with a single account, or the status of all accounts in an OU that has multiple controls enabled on it\. The account or OU compliance status can be found on the account or OU detail pages\.

**Note**  
The **State** of a control, as viewed in the AWS Control Tower console, reflects only the enabled or de\-activated state of the control for a specific OU\. This field does not reflect any information about the framework compliance status or the drift status of the landing zone environment\. The control **State** and **Status** information is available in the console only\. It is not available from the public API\. To view the control status, navigate to the **Control details** page in the AWS Control Tower console\.

****The following list gives more information about compliance status as reported specifically for controls\.****
+  **`Enforced`** – Maximum level of protection\. Operations that would break this compliance rule are simply not allowed\. 
  + **Reported for:** Preventive controls \(SCPs\)
  + **Applies to:** Any preventive control that's enabled on any of multiple accounts that are members of an OU\. Controls are enabled at the OU level\.
+  **`Clear`** – Compliance rules are properly in place\. No violations have been detected\. 
  + **Reported for:** Detective controls \(AWS Config Rules\)
  + **What is checked:** Any detective control that's enabled on any of multiple accounts that are members of an OU\. Controls are enabled at the OU level\.
+  **`In violation`** – Denotes that resources are actively breaching a control\.
  + **Reported for:** Detective controls \(AWS Config Rules\)
  + **What is checked:** Any detective control that's enabled on any of multiple accounts that are members of an OU\. Controls are enabled at the OU level\.

****The following list gives more information about compliance status as reported specifically for OUs and their member accounts\.****
+  **`Compliant`** – Compliance rules are properly in place\. No violations have been detected for any resources\. Controls are applied at the OU level, for all enrolled accounts in the OU, and their resources\.
  + **Reported for:** Detective controls \(AWS Config Rules\)
  + 

**What it checks:**
    + Any individual detective control that's applied to the member accounts in an OU
    + Multiple detective controls that are applied to the member accounts in an OU
+  **`Noncompliant`** – Compliance rules are in place\. However, non\-compliant resources have been detected in one or more member accounts in the OU\.
  + **Reported for:** Detective controls \(AWS Config Rules\)
  + 

**What it checks:**
    + Any individual detective control that's applied to the member accounts in an OU
    + Multiple detective controls that are applied to the member accounts in an OU

**The following status can be reported for any account, control, or OU\.**

 **`Unknown`** – A compliance rule is broken or compliance cannot be guaranteed\.
+ 

**Reported for:**
  + Detective controls \(AWS Config Rules\)
  + Preventive controls \(SCPs\)
+ 

**What it checks:**
  + Any detective control that's enabled on any accounts that are members of an OU\. Controls are enabled at the OU level\.
  + Any preventive control that's enabled on any accounts that are members of an OU\. Controls are enabled at the OU level\.
  + Basically anything with a compliance status \(account, control, resource, or OU\)\.