# Control API examples<a name="control-api-examples-short"></a>

Each control in AWS Control Tower has a unique identifier for use with the control APIs\. The identifier for each control is shown in the **API controlIdentifier** field, on the **Control details** page in the AWS Control Tower console\. This identifier is distinct from the **ControlID** field, which is a classification system for controls\.

**Note**  
When you invoke `EnableControl` on an account or OU, the `operationIdentifier` value is returned by means of `ListEnabledControls` or `GetEnabledControl` even if the enable operation fails\. In the AWS Control Tower console, you can determine whether the `EnableControl` operation was successful, by verifying that the control is enabled on the account or OU\. Programatically, you can track the status of the `EnableControl` operation with the `GetControlOperation` API command, by passing it the value of `operationIdentifier` as shown in an example that follows\.

**Example input for EnableControl:**

This example shows how to specify the control you wish to enable, and activate that control for the target OU that you identify\.

```
{
        controlIdentifier: "arn:aws:controltower:us-west-2::control/AWS-GR_AUTOSCALING_LAUNCH_CONFIG_PUBLIC_IP_DISABLED",
        targetIdentifier: "arn:aws:organizations::123456789123:ou/o-kg8aXXXXXX/ou-prlj-a5kXXXXX"
        }
```

**Example output for EnableControl:**

As an example of how to use this output parameter, you can pass the **operationIdentifier** parameter as an input to the **GetControlOperation** API, to track the status of your **EnableControl** task\.

```
{
    "operationIdentifier":"e2bXXXXX-6cab-XXXX-bde7-XX0c6fXXXXXX" 
}
```

**Example CLI command:**

```
aws controltower enable-control \
--control-identifier arn:aws:controltower:us-west-2::control/AWS-GR_AUDIT_BUCKET_POLICY_CHANGES_PROHIBITED \
--target-identifier arn:aws:organizations::123456789123:ou/o-qnilXXXXXX/ou-vwxu-qqlXXXXX \
--region us-west-2
```

**Example input for DisableControl:**

```
{
    controlIdentifier: "arn:aws:controltower:us-west-2::control/AWS-GR_AUTOSCALING_LAUNCH_CONFIG_PUBLIC_IP_DISABLED",
    targetIdentifier: "arn:aws:organizations::123456789123:ou/o-kg8aXXXXXX/ou-prlj-a5kXXXXX"
}
```

**Example output for DisableControl:**

```
{ 
    "operationIdentifier":"e2bXXXXX-8xai-XXXX-bde7-XX0c6fXXXXXX" 
}
```

**Example input for GetControlOperation:**

 When you give an **operationIdentifier** as input, you receive a status message as output\.

```
{
    operationIdentifier: "e2bXXXXX-6cab-XXXX-bde7-XX0c6fXXXXXX"
}
```

**Example output for GetControlOperation:**

```
{ 
    "ControlOperationStatus":{
        "OperationType": "ENABLE_CONTROL", 
        "StartTime": "2022-02-02T20:52:08.034Z", 
        "Status": "IN_PROGRESS" 
    } 
}
```

**Example output for GetControlOperation:**

```
{
    "ControlOperationStatus": {
        "EndTime": "2022-04-28T19:36:31Z",
        "OperationType": "DISABLE_CONTROL",
        "StartTime": "2022-04-28T19:35:00Z",
        "Status": "SUCCEEDED"
    }
}
```

```
{
    "ControlOperationStatus": {
        "EndTime": "2022-04-28T19:36:31Z",
        "OperationType": "DISABLE_CONTROL",
        "StartTime": "2022-04-28T19:35:00Z",
        "Status": "FAILED",
        "StatusMessage": "AWS Control Tower cannot add the SCP because the IAM user or role does not have permission to perform the requested operation in AWS Organizations. To continue, update your access permissions for AWS Organizations. For more information, see Access Management in the IAM User Guide."
    }
}
```

**Example input for ListEnabledControls:**

This example shows how to specify the target OU as input, so you can receive a list of controls as output\.

```
{
    targetIdentifier: "arn:aws:organizations::123456789123:ou/o-kg8aXXXXXX/ou-prlj-a5kXXXXX",
    nextToken: "bde7-XX0c6fXXXXXX",
    maxResults: 2
}
```

**Example output for ListEnabledControls:**

```
{
    "enabledControls": [
        {
            "controlIdentifier": "arn:aws:controltower:us-west-2::control/AWS-GR_AUTOSCALING_LAUNCH_CONFIG_PUBLIC_IP_DISABLED"
        },
        {
            "controlIdentifier": "arn:aws:controltower:us-west-2::control/AWS-GR_RESTRICT_ROOT_USER"
        }
    ],
    "nextToken": "e2bXXXXX-6cab-XXXX"
}
```