# Guardrail Reference<a name="guardrails-reference"></a>

The following sections include a reference for each of the guardrails available in AWS Control Tower\. Each guardrail reference includes the details, artifacts, additional information, and considerations to keep in mind when enabling a specific guardrail on a OU in your landing zone\.

**Topics**
+ [Mandatory Guardrails](mandatory-guardrails.md)
+ [Strongly Recommended Guardrails](strongly-recommended-guardrails.md)
+ [Elective Guardrails](elective-guardrails.md)

**Guardrail overview**

Two mandatory guardrails are detective, the others are preventive\.
+ Disallow Public Read Access to Log Archive
+ Disallow Public Write Access to Log Archive

**Note**  
The four mandatory guardrails with `"Sid": "GRCLOUDTRAILENABLED"` are identical by design\. The sample code is correct\.

Two strongly recommended guardrails are preventive, the others are detective\. By default, these guardrails are not enabled\.
+ Disallow Creation of Access Keys for the Root User
+ Disallow Actions as a Root User

Two elective guardrails are preventive, the others are detective\. By default, these guardrails are not enabled\.
+ Disallow Cross\-Region Replication for Amazon S3 Buckets
+ Disallow Delete Actions on Amazon S3 Buckets Without MFA