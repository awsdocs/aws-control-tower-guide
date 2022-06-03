# Nested OU<a name="cfct-nested-ou"></a>

CfCT supports listing one or more nested OUs under the `organizational_units` keyword in manifest V2 version \(2021\-03\-15\)\. 

A complete path \(excluding Root\) for the nested OU is required, using a colon as the separator between OUs\. For deployment method `scp`, AWS Control Tower deploys the SCPs to the last OU in the nested OU path\. For deployment method `stack_set`, AWS Control Tower deploys the stack sets to all the accounts under the last OU in the nested OU path\.

For example, consider the path `OUName1:OUName2:OUName3`\. The last OU in the path is `OUName3`\. CfCT deploys the SCPs to `OUName3` and stack sets to all of the accounts directly under `OUName3`, only\.

```
---
region: your-home-region
version: 2021-03-15

resources:

  …truncated…

    deployment_targets:
      organizational_units:
        - OuName1:OUName2:OUName3
```

**Note**  
The nested OU feature is supported only in the V2 version of the manifest file \(2021\-03\-15\)\.