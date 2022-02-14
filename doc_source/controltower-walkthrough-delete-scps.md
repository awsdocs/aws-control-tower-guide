# Delete SCPs<a name="controltower-walkthrough-delete-scps"></a>

AWS Control Tower uses service control policies \(SCPs\) for its guardrails\. This procedure walks through how to delete the SCPs specifically related to AWS Control Tower\.

**To delete AWS Organizations SCPs**

1. Open the Organizations console at [https://console\.aws\.amazon\.com/organizations/](https://console.aws.amazon.com/organizations/)\.

1. Open the **Policies** tab, and find the Service Control Policies \(SCPs\) that have the prefix **aws\-guardrails\-** and do the following for each SCP:

   1. Detach the SCP from the associated OU\.

   1. Delete the SCP\.