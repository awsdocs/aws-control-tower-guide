# View control details<a name="control-details"></a>

To view details about an individual control, select the name of the control from the table on the **Controls** page\.

In each **Control details** page of the console, you can find the following details for each control:
+ **Name** – The name of the control\.
+ **Description** – A description of the control\.
+ **Guidance** – The guidance is either mandatory, strongly recommended, or elective\.
+ **Category** – The control category can be **Audit logs**, **Data residency**, **Data security**, **Monitoring**, **Network**, **Operations**, **IAM**, or **AWS Control Tower Setup**\.
+ **Release date** –The date the control became available\.
+ **Behavior** – A control's behavior is set to either preventive or detective\.
+ **Compliance Status** – A control's compliance status can be **Clear**, **Compliant**, **Enforced**, **Unknown**, or **In violation**\. For more information, see [AWS Control Tower compliance status for controls, OUs, and accounts](compliance-statuses.md)\.
+ **State** – The state of enablement for a control on the current OU\. A control's state can be **Enabled**, **Enabling**, **Failed to enable**, **Failed to disable**, or **Disabling**\. If a control is enabled on an OU higher on the hierarchy tree of a nested OU, the state will show **Inherited**\.
+  The status of the Region deny control is shown as a separate entry\. 

The control is implemented by one or more artifacts\. These artifacts can include a baseline AWS CloudFormation template, a service control policy \(SCP\) to prevent account\-level configuration changes or activity that may create configuration drift, and AWS Config Rules to detect account\-level policy violations\.

To view a control's artifact, select **Service control policy \(SCP\)** or **AWS Config rule** on the **Control details** page\.