# Guardrail Reference<a name="guardrails-reference"></a>

The following sections include a reference for each of the guardrails available in AWS Control Tower\. Each guardrail reference includes the details, artifacts, additional information, and considerations to keep in mind when enabling a specific guardrail on a OU in your landing zone\.

**Topics**
+ [Mandatory Guardrails](mandatory-guardrails.md)
+ [Strongly Recommended Guardrails](strongly-recommended-guardrails.md)
+ [Elective Guardrails](elective-guardrails.md)

**Two mandatory guardrails are detective, the others are preventive\.**
+ Detect Public Read Access Setting for Log Archive
+ Detect Public Write Access Setting for Log Archive

**Note**  
The four mandatory guardrails with `"Sid": "GRCLOUDTRAILENABLED"` are identical by design\. The sample code is correct\.

**Two strongly recommended guardrails are preventive, the others are detective\. By default, these guardrails are not enabled\.**
+ Disallow Creation of Access Keys for the Root User
+ Disallow Actions as a Root User

**Six elective guardrails are preventive, the others are detective\. By default, these guardrails are not enabled\.**
+ Disallow Changes to Replication Configuration for Amazon S3 Buckets
+ Disallow Delete Actions on Amazon S3 Buckets Without MFA
+ Disallow Changes to Encryption Configuration for Amazon S3 Buckets \[Previously: Enable Encryption at Rest for Log Archive\]
+ Disallow Changes to Logging Configuration for Amazon S3 Buckets \[Previously: Enable Access Logging for Log Archive\]
+ Disallow Changes to Bucket Policy for Amazon S3 Buckets \[Previously: Disallow Policy Changes to Log Archive\] 
+ Disallow Changes to Lifecycle Configuration for Amazon S3 Buckets \[Previously: Set a Retention Policy for Log Archive\]