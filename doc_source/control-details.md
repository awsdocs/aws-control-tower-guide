# View control details<a name="control-details"></a>

To view details about an individual control in the console, select the name of the control from the table on the **Controls** page\.

To view more details about an individual control in the *AWS Control Tower User Guide*, see [Tables of control metadata](control-metadata-tables.md)\. For each control, the **API controlIdentifer** for each Region is available, along with the framework and objective\. Certain additional information is available only in the console, as described in the next sections\.

In each **Control details** page of the console, you can find the following details for each control:
+ **Name** – The name of the control\.
+ **Control objective** – The pre\-defined objective that this control helps you enforce\. See the [List of control objectives](list-of-control-objectives.md)\.
+ **Service** – The AWS service to which this control applies\.
+ **Control owner** – The AWS service that owns and maintains this control\.
+ **Behavior** – A control's behavior is set to preventive, detective, or proactive\.
+ **Implementation** – The underlying implementation method for this control, such as SCP, AWS Config managed rule, or AWS CloudFormation hook\.
+ **Resource** – The AWS resource that is monitored or affected by this control\.
+ **Framework** – The industry\-standard compliance framework that this control helps to enforce, for example, **NIST 800\-53 Rev 5**\.
+ **Control ID** – A unique identifier assigned to each control\. This identifier is part of a classification system for the controls\.
+ **API controlIdentifier** – This identifier is needed when calling the AWS Control Tower APIs\.
+ **Guidance** – The guidance is either mandatory, strongly recommended, or elective\.
+ **Severity** – The relative risk associated with any violation of this control\.
+ **Release date** –The date the control became available\.

 The status of the Region deny control is shown as a separate entry\. 

Other information may appear on the **Control details** page, including these:
+ **Description** – A brief description of the control and its function\.
+  **Remediation message** – Suggestions for what to change if your AWS CloudFormation hook control returns a FAIL status\.
+ **Remediation samples ** – Examples showing configurations that can return a PASS or FAIL result for your AWS CloudFormation hook control\.
+ **Usage considerations** – Additional information about how to apply this control or about the resources it can affect\.
+ The **Gherkin** artifact – The Gherkin is a readable specification for the AWS CloudFormation hook controls, showing requirements for tests that cause PASS, FAIL, or SKIP results to be returned\.

**To view a control artifact**

Each control is implemented by one or more artifacts\. These artifacts can include a baseline AWS CloudFormation template, a service control policy \(SCP\) to prevent account\-level configuration changes or activity that may create configuration drift, and AWS Config Rules to detect account\-level policy violations\.

To view a control's artifact, select the **Artifact** tab to view the **Service control policy \(SCP\)**, **AWS Config rule**, or **AWS CloudFormation policy template** on the **Control details** page\.