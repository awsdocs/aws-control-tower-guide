# Required roles<a name="aft-required-roles"></a>

In general, roles and policies are part of identity and access management \(IAM\) in AWS\. Refer to the [https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html](https://docs.aws.amazon.com/IAM/latest/UserGuide/introduction.html) for more information\.

AFT creates multiple IAM roles and policies in the AFT management and AWS Control Tower management accounts to support the operations of the AFT pipeline\. These roles are created based on the least privilege access model, which restricts permission to the minimally required sets of actions and resources for each role and policy\. These roles and policies are assigned an AWS tag `key:value` pair, as ` managed_by:AFT` for identification\. 

Besides these roles, AFT creates two essential roles: the `AWSAFTAdmin` role and the `AWSAFTExecution` role, which are explained in the following sections\.

**The AWSAFTAdmin role, explained**

When you deploy AFT, the `AWSAFTAdmin` role is created in the AFT management account\. This role allows the AFT pipeline to assume the `AWSAFTExecution` role in AWS Control Tower and AFT provisioned accounts, thereby to perform actions related to account provisioning and customizations\.

Here is the inline policy \(JSON artifact\) attached to the `AWSAFTAdmin` role: 

```
{
    "Version": "2012-10-17",
    "Statement": [
        {
            "Effect": "Allow",
            "Action": "sts:AssumeRole",
            "Resource": "arn:aws:iam::*:role/AWSAFTExecution"
        }
    ]
}
```

The following artifact shows the trust relationship for the `AWSAFTAdmin` role\. The placeholder number `012345678901` is replaced by the AFT management account ID number\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::012345678901:root"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```

**The AWSAFTExecution role, explained**

When you deploy AFT, the `AWSAFTExecution` role is created in the AFT management and AWS Control Tower management accounts\. Later, the AFT pipeline creates the `AWSAFTExecution` role in each AFT provisioned account during the AFT account provisioning stage\.

 AFT uses the `AWSControlTowerExecution` role initially, to create the `AWSAFTExecution` role in specified accounts\. The `AWSAFTExecution` role allows the AFT pipeline to run the steps performed during the AFT framework's provisioning and provisioning customizations stages, for AFT provisioned accounts\.

Here is the IAM policy \(JSON artifact\) attached to the `AWSAFTExecution` role:

**AdministratorAccess** – AWS managed policy 

The following artifact shows the trust relationship for the `AWSAFTExecution role`\. The placeholder number `012345678901` is replaced by the AFT management account ID number\.

```
{
  "Version": "2012-10-17",
  "Statement": [
    {
      "Effect": "Allow",
      "Principal": {
        "AWS": "arn:aws:iam::012345678901:role/AWSAFTAdmin"
      },
      "Action": "sts:AssumeRole"
    }
  ]
}
```