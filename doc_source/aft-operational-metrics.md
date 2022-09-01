# Operational metrics<a name="aft-operational-metrics"></a>

By default, *Account Factory for Terraform \(AFT\)* sends anonymous operational metrics to AWS\. We use this data to understand how customers are using AFT so we can improve the quality and features of the solution\. You can opt out of data collection by changing a parameter during AFT deployment\. When collection is enabled, the following data is sent to AWS:
+ **Solution:** The AFT\-specific identifier
+ **Version:** The version of AFT
+ **Universally Unique Identifier \(UUID\):** Randomly generated, unique identifier for each AFT deployment
+ **Timestamp:** Data\-collection timestamp
+ **Data:** AFT configuration and actions taken by the customer

AWS owns the data collected\. Data collection is subject to the [AWS Privacy Policy](https://aws.amazon.com/privacy/)\.

**Note**  
Versions of AFT prior to 1\.6\.0 do not report usage metrics to AWS\.

To opt out of reporting metrics:
+ Set the input value of `aft_metrics_reporting` to `false` in your Terraform input configuration file, as shown in the example that follows, and redeploy AFT\. This value is set to `true` by default, if you do not set it explicitly\.

If you copy the example, remember to substitute your actual ID and Region values for the items given in strings with `x`\.

```
    module "control_tower_account_factory" {
    source = "aws-ia/control_tower_account_factory/aws"
    
    # Required Vars
    ct_management_account_id    = "xxxxxxxxxxx"
    log_archive_account_id      = "xxxxxxxxxxx"
    audit_account_id            = "xxxxxxxxxxx"
    aft_management_account_id   = "xxxxxxxxxxx"
    ct_home_region              = "xx-xxxx-x"
    tf_backend_secondary_region = "xx-xxxx-x"
    
    # Optional Vars
    aft_metrics_reporting = false    # to opt out, set this value to false 
    }
```