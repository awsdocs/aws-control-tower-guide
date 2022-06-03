# Collection of operational metrics<a name="cfct-metrics"></a>

*Customizations for AWS Control Tower* \(CfCT\) includes an option to send anonymous operational metrics to AWS\. We use this data to understand how customers use this capability, along with related services and products\. When data collection is enabled, the following information is sent to AWS: 
+ **Solution ID:** The AWS solution identifier
+ **Unique ID \(UUID\):** Randomly generated, unique identifier for each deployment
+ **Timestamp:** Data\-collection timestamp
+ **State Machine Execution Count:** Incrementally counts the number of times this state machine runs
+ **Manifest Version:** The manifest version used in the configuration

Note that AWS owns the data gathered through this survey\. Data collection is subject to the [AWS Privacy Policy](https://aws.amazon.com/privacy/)\. To opt out of this feature, complete one of the following tasks:
+ Modify the AWS CloudFormation template mapping section as follows:

  ```
  AnonymousData:
       SendAnonymousData:
          Data: Yes
  ```

  to

  ```
  AnonymousData:
       SendAnonymousData:
          Data: No
  ```

OR
+ After CfCT is deployed, find the `/org/primary/metrics_flag` SSM parameter key in the Parameter Store console and set the value to `No`\.