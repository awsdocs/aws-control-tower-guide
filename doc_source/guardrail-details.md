# View guardrail details<a name="guardrail-details"></a>

To view details about an individual guardrail, select the name of the guardrail from the table on the **Guardrails** page\.

In the guardrail details page of the console, you can find the following details for each guardrail:
+ **Name** – The name of the guardrail\.
+ **Description** – A description of the guardrail\.
+ **Guidance** – The guidance is either mandatory, strongly recommended, or elective\.
+ **Behavior** – A guardrail's behavior is set to either preventive or detective\.
+ **Compliance Status** – A guardrail's compliance status can be clear, compliant, enforced, unknown, or in violation\. For more information, see [AWS Control Tower guardrail compliance status](compliance.md#compliance-statuses)\.
+  The status of the Region deny guardrail is shown as a separate entry\. 

The guardrail is implemented by one or more artifacts\. These artifacts can include a baseline AWS CloudFormation template, a service control policy \(SCP\) to prevent account\-level configuration changes or activity that may create configuration drift, and AWS Config Rules to detect account\-level policy violations\.

To view a guardrail's artifact, select **Service control policy \(SCP\)** or **AWS Config rule** on the **Guardrail details** page\.