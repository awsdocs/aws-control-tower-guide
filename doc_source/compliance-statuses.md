# AWS Control Tower compliance status for guardrails, OUs, and accounts<a name="compliance-statuses"></a>

Compliance is reported in the AWS Control Tower dashboard for guardrails, accounts, and OUs\. This section lists the possible categories of compliance and non\-compliance in AWS Control Tower, assuming that guardrail controls are enabled for an account or an OU\.
+ **For a guardrail: **A compliance status of **Enforced**, **Clear**, or **In violation** is possible, as long as the guardrail is enabled on your OUs and the member accounts in the OUs\. You can view this guardrail status on individual guardrail detail pages\.
+ **For an account or OU: **A compliance status of **Compliant**, **Noncompliant**, or **Unknown** is possible\. The compliance status refers to the status of the resources associated with a single account, or the status of all accounts in an OU that has multiple guardrails enabled on it\. The account or OU compliance status can be found on the account or OU detail pages\.

****The following list gives more information about compliance status as reported specifically for guardrails\.****
+  **`Enforced`** – Maximum level of protection\. Operations that would break this compliance rule are simply not allowed\. 
  + **Reported for:** Preventive guardrails \(SCPs\)
  + **Applies to:** Any preventive guardrail that's enabled on any of multiple accounts that are members of an OU\. Guardrails are enabled at the OU level\.
+  **`Clear`** – Compliance rules are properly in place\. No violations have been detected\. 
  + **Reported for:** Detective guardrails \(AWS Config Rules\)
  + **What is checked:** Any detective guardrail that's enabled on any of multiple accounts that are members of an OU\. Guardrails are enabled at the OU level\.
+  **`In violation`** – Denotes that resources are actively breaching a guardrail control\.
  + **Reported for:** Detective guardrails \(AWS Config Rules\)
  + **What is checked:** Any detective guardrail that's enabled on any of multiple accounts that are members of an OU\. Guardrails are enabled at the OU level\.

****The following list gives more information about compliance status as reported specifically for OUs and their member accounts\.****
+  **`Compliant`** – Compliance rules are properly in place\. No violations have been detected for any resources\. Guardrails are applied at the OU level, for all enrolled accounts in the OU, and their resources\.
  + **Reported for:** Detective guardrails \(AWS Config Rules\)
  + 

**What it checks:**
    + Any individual detective guardrail that's applied to the member accounts in an OU
    + Multiple detective guardrails that are applied to the member accounts in an OU
+  **`Noncompliant`** – Compliance rules are in place\. However, non\-compliant resources have been detected in one or more member accounts in the OU\.
  + **Reported for:** Detective guardrails \(AWS Config Rules\)
  + 

**What it checks:**
    + Any individual detective guardrail that's applied to the member accounts in an OU
    + Multiple detective guardrails that are applied to the member accounts in an OU

**The following status can be reported for any account, guardrail, or OU\.**

 **`Unknown`** – A compliance rule is broken or compliance cannot be guaranteed\.
+ 

**Reported for:**
  + Detective Guardrails \(AWS Config Rules\)
  + Preventive guardrails \(SCPs\)
+ 

**What it checks:**
  + Any detective guardrail that's enabled on any accounts that are members of an OU\. Guardrails are enabled at the OU level\.
  + Any preventive guardrail that's enabled on any accounts that are members of an OU\. Guardrails are enabled at the OU level\.
  + Basically anything with a compliance status \(account, guardrail, resource, or OU\)\.