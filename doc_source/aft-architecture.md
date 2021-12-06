# AFT Architecture<a name="aft-architecture"></a>

The AFT architecture has three high\-level principles\.

1. **AFT Pipeline** – \(Mandatory\) These components are necessary to support the AFT framework\. They are part of every deployment, and supported by the AWS Control Tower team\.

1. **AFT Feature Options** – \(Optional\) These components are embedded in the offering, and built according to AWS prescriptive guidance\. These options are deployed with feature flags that you can select, and they are supported by the AWS Control Tower team\.

**Feature options include**
   + AWS CloudTrail data event logging for Amazon S3 buckets and Lambda; functions\.
   + Automated deletion of the AWS default VPC for an account, in all AWS Regions\.
   + Automatic enrollment in the AWS Enterprise Support plan\.

1. **AFT Customizations** – \(Optional\) You can add any public or private Terraform module that you choose into the AFT pipeline\. You are responsible for all module configurations related to these customizations\.

**AWS Control Tower Account Factory for Terraform – architecture diagram**

![\[Figure: AFT Architecture\]](http://docs.aws.amazon.com/controltower/latest/userguide/images/aft-architecture.png)

## Order of operations<a name="aft-operation"></a>

You will run AFT operations while signed into the AFT management account\. For a full account provisioning workflow, the order of stages, shown in the diagram from left to right, is as follows:

1. Each account is provisioned\. This stage runs in the AFT management account\.

1. If you've set up a state machine, certain customizations are run as a part of provisioning \(referred to as the \(*provisioning framework*\)\. This stage runs in the AFT management account\.

1. Global customizations are run\. This stage runs in the pipeline that's set up for each vended account\.

1. Account customizations are run, if they were referenced in the initial account provisioning request\. This stage runs on targeted accounts only\.

If the account is already provisioned, you must initiate further customizations manually in that account's pipeline