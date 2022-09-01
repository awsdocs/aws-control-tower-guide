# Resolve drift with Repair and Re\-register<a name="resolve-drift"></a>

Drift often occurs as you and your organization members use the landing zone\.

Drift detection is automatic in AWS Control Tower\. Automated scans of your SCPs help you identify resources that need changes or configuration updates that must be made to resolve the drift\. 

To repair most types of drift, choose **Repair** on the **Landing zone settings** page\. Also, you can repair some types of drift by choosing to ** Re\-register** an OU\. For more information about types of drift and how to resolve them, see [Types of Governance Drift](governance-drift.md) and [Detect and resolve drift in AWS Control Tower](drift.md)\.

One special case of repair occurs for *role drift*\. If a required role is not available, the console shows a warning page and some instructions on how to restore the role\. Your landing zone is unavailable until the role drift is repaired\. This drift repair is not the same as a full landing zone repair\. For more information, see *Don't delete required roles* in the section called [Types of drift to repair right away](drift.md#types-of-drift)\.

**Note**  
When you fully repair your landing zone, the landing zone is upgraded to the latest landing zone version\.