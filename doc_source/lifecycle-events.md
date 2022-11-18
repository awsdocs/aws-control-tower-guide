# Lifecycle Events in AWS Control Tower<a name="lifecycle-events"></a>

Some events logged by AWS Control Tower are *lifecycle events*\. A lifecycle event's purpose is to mark the *completion* of certain AWS Control Tower actions that change the state of resources\. Lifecycle events apply to resources that AWS Control Tower creates or manages, such as organizational units \(OUs\), accounts, and controls\.

**Characteristics of AWS Control Tower lifecycle events**
+ For each lifecycle event, the event log shows whether the originating Control Tower action completed successfully, or failed\.
+ AWS CloudTrail automatically records each lifecycle event as a *non\-API AWS service event*\. For more information, see [ the AWS CloudTrail User Guide\.](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/non-api-aws-service-events.html)
+ Each lifecycle event also is delivered to the Amazon EventBridge and Amazon CloudWatch Events services\. 

**Lifecycle events in AWS Control Tower offer two primary benefits:**
+ Because a lifecycle event registers the completion of an AWS Control Tower action, you can create an Amazon EventBridge rule or Amazon CloudWatch Events rule that can trigger the next steps in your automation workflow, based on the state of the lifecycle event\.
+ The logs provide additional detail to assist administrators and auditors in reviewing certain types of activity in your organizations\. 

**How lifecycle events work**

 AWS Control Tower relies upon multiple services to implement its actions\. Therefore, each lifecycle event is recorded only after a series of actions is complete\. For example, when you enable a control on an OU, AWS Control Tower launches a series of sub\-steps that implement the request\. The final result of the entire series of sub\-steps is recorded in the log as the state of the lifecycle event\.
+ If every underlying sub\-step has completed successfully, the lifecycle event state is recorded as **Succeeded**\.
+ If any of the underlying sub\-steps did not complete successfully, the lifecycle event state is recorded as **Failed**\.

Each lifecycle event includes a logged timestamp that shows when the AWS Control Tower action was initiated, and another timestamp showing when the lifecycle event is completed, marking success or failure\.

**Viewing lifecycle events in Control Tower**

You can view lifecycle events from the **Activities** page in your AWS Control Tower dashboard\.
+ To navigate to the **Activities** page, choose **Activities** from the left navigation pane\.
+ To get more details about a specific event, select the event and then choose the **View details** button at the upper right\.

 For more information about how to integrate AWS Control Tower lifecycle events into your workflows, see this blog post, [Using lifecycle events to track AWS Control Tower actions and trigger automated workflows](http://aws.amazon.com/blogs/mt/using-lifecycle-events-to-track-aws-control-tower-actions-and-trigger-automated-workflows/)\.

**Expected behavior of CreateManagedAccount and UpdateManagedAccount lifecycle events**

 When you create an account or enroll an account in AWS Control Tower, those two actions call the same internal API\. If there's an error during the process, it usually occurs after the account has been created but is not fully provisioned\. When you retry to create the account after the error, or when you try to update the provisioned product, AWS Control Tower sees that the account already exists\.

Because the account exists, AWS Control Tower records the `UpdateManagedAccount` lifecycle event instead of the `CreateManagedAccount` lifecycle event at the end of the retry request\. You may have expected to see another `CreateManagedAccount` event because of the error\. However, the `UpdateManagedAccount` lifecycle event is the expected and desired behavior\.

 If you plan to create or enroll accounts into AWS Control Tower using automated methods, program the Lambda function to look for **UpdateManagedAccount** lifecycle events as well as **CreateManagedAccount** lifecycle events\. 

**Lifecycle event names**

Each lifecycle event is named so that it corresponds to the originating AWS Control Tower action, which also is recorded by AWS CloudTrail\. Thus, for example, a lifecycle event originated by the AWS Control Tower `CreateManagedAccount` CloudTrail event is named `CreateManagedAccount`\.

Each name in the list that follows is a link to an example of the logged detail in `JSON` format\. The additional detail shown in these examples is taken from the Amazon CloudWatch event logs\.

Although `JSON` does not support comments, some comments have been added in the examples for explanatory purposes\. Comments are preceded by "//" and they appear in the right side of the examples\.

In these examples, some account names and organization names are obscured\. An `accountId` is always a 12\-number sequence, which has been replaced with "xxxxxxxxxxxx" in the examples\. An `organizationalUnitID` is a unique string of letters and numbers\. Its form is preserved in the examples\.
+ [`CreateManagedAccount`](#create-managed-account): The log records whether AWS Control Tower successfully completed every action to create and provision a new account using account factory\.
+ [`UpdateManagedAccount`](#update-managed-account): The log records whether AWS Control Tower successfully completed every action to update a provisioned product that's associated with an account you had previously created by using account factory\.
+ [`EnableGuardrail`](#enable-control): The log records whether AWS Control Tower successfully completed every action to enable a control on an OU that was created by AWS Control Tower\.
+ [`DisableGuardrail`](#disable-control): The log records whether AWS Control Tower successfully completed every action to disable a control on an OU that was created by AWS Control Tower\.
+ [`SetupLandingZone`](#setup-landing-zone): The log records whether AWS Control Tower successfully completed every action to set up a landing zone\.
+ [`UpdateLandingZone`](#update-landing-zone): The log records whether AWS Control Tower successfully completed every action to update your existing landing zone\.
+ [`RegisterOrganizationalUnit`](#register-organizational-unit): The log records whether AWS Control Tower successfully completed every action to enable its governance features on an OU\. 
+ [`DeregisterOrganizationalUnit`](#deregister-organizational-unit): The log records whether AWS Control Tower successfully completed every action to disable its governance features on an OU\.
+ [`PrecheckOrganizationalUnit`](#precheck-organizational-unit): The log records whether AWS Control Tower detected any resource that would would prevent the **Extend governance** operation from completing successfully\.

The following sections provide a list of AWS Control Tower lifecycle events, with examples of the details logged for each type of lifecycle event\.

## `CreateManagedAccount`<a name="create-managed-account"></a>

This lifecycle event records whether AWS Control Tower successfully created and provisioned a new account using account factory\. This event corresponds to the AWS Control Tower `CreateManagedAccount` CloudTrail event\. The lifecycle event log includes the `accountName` and `accountId` of the newly\-created account, and the `organizationalUnitName` and `organizationalUnitId` of the OU in which the account has been placed\.

```
{
    "version": "0",
    "id": "999cccaa-eaaa-0000-1111-123456789012",         
    "detail-type": "AWS Service Event via CloudTrail",
    "source": "aws.controltower",
    "account": "XXXXXXXXXXXX",                                   // Management account ID. 
    "time": "2018-08-30T21:42:18Z",                              // Format: yyyy-MM-dd'T'hh:mm:ssZ 
    "region": "us-east-1",                                       // AWS Control Tower home region.
    "resources": [ ],
    "detail": {
        "eventVersion": "1.05",
        "userIdentity": {
            "accountId": "XXXXXXXXXXXX",                       
            "invokedBy": "AWS Internal"
        },
        "eventTime": "2018-08-30T21:42:18Z",                     // Timestamp when call was made. Format: yyyy-MM-dd'T'hh:mm:ssZ.
        "eventSource": "controltower.amazonaws.com",
        "eventName": "CreateManagedAccount",                  
        "awsRegion": "us-east-1",                               
        "sourceIPAddress": "AWS Internal",
        "userAgent": "AWS Internal",
        "eventID": "0000000-0000-0000-1111-123456789012",        
        "readOnly": false,
        "eventType": "AwsServiceEvent",
        "serviceEventDetails": {
            "createManagedAccountStatus": {
                "organizationalUnit":{
                    "organizationalUnitName":"Custom",
                    "organizationalUnitId":"ou-XXXX-l3zc8b3h"

                    },
                "account":{
                    "accountName":"LifeCycle1",
                    "accountId":"XXXXXXXXXXXX"
                    },
                "state":"SUCCEEDED",
                "message":"AWS Control Tower successfully created a managed account.",
                "requestedTimestamp":"2019-11-15T11:45:18+0000",
                "completedTimestamp":"2019-11-16T12:09:32+0000"}
        }
    }
}
```

## `UpdateManagedAccount`<a name="update-managed-account"></a>

This lifecycle event records whether AWS Control Tower successfully updated the provisioned product associated with an account that was created previously by using account factory\. This event corresponds to the AWS Control Tower `UpdateManagedAccount` CloudTrail event\. The lifecycle event log includes the `accountName` and `accountId` of the associated account, and the `organizationalUnitName` and `organizationalUnitId` of the OU in which the updated account is placed\. 

```
{
    "version": "0",
    "id": "999cccaa-eaaa-0000-1111-123456789012",                
    "detail-type": "AWS Service Event via CloudTrail",
    "source": "aws.controltower",
    "account": "XXXXXXXXXXXX",                                   // AWS Control Tower organization management account.
    "time": "2018-08-30T21:42:18Z",                              // Format: yyyy-MM-dd'T'hh:mm:ssZ 
    "region": "us-east-1",                                       // AWS Control Tower home region.
    "resources": [],
    "detail": {
        "eventVersion": "1.05",
        "userIdentity": {
            "accountId": "XXXXXXXXX",                        
            "invokedBy": "AWS Internal"
        },
        "eventTime": "2018-08-30T21:42:18Z",                     // Timestamp when call was made. Format: yyyy-MM-dd'T'hh:mm:ssZ.
        "eventSource": "controltower.amazonaws.com",
        "eventName": "UpdateManagedAccount",                   
        "awsRegion": "us-east-1",                                
        "sourceIPAddress": "AWS Internal",
        "userAgent": "AWS Internal",
        "eventID": "0000000-0000-0000-1111-123456789012",        
        "readOnly": false,
        "eventType": "AwsServiceEvent",
        "serviceEventDetails": {
            "updateManagedAccountStatus": {
                "organizationalUnit":{
                    "organizationalUnitName":"Custom",
                    "organizationalUnitId":"ou-XXXX-l3zc8b3h"
                    },
                "account":{
                    "accountName":"LifeCycle1",
                    "accountId":"624281831893"
                    },
                "state":"SUCCEEDED",
                "message":"AWS Control Tower successfully updated a managed account.",
                "requestedTimestamp":"2019-11-15T11:45:18+0000",
                "completedTimestamp":"2019-11-16T12:09:32+0000"}
        }
    }
}
```

## `EnableGuardrail`<a name="enable-control"></a>

This lifecycle event records whether AWS Control Tower successfully enabled a control on an OU that is being managed by AWS Control Tower\. This event corresponds to the AWS Control Tower `EnableGuardrail` CloudTrail event\. The lifecycle event log includes the `guardrailId` and `guardrailBehavior` of the control, and the `organizationalUnitName` and `organizationalUnitId` of the OU on which the control is enabled\.

```
{
    "version": "0",
    "id": "999cccaa-eaaa-0000-1111-123456789012",         
    "detail-type": "AWS Service Event via CloudTrail",
    "source": "aws.controltower",
    "account": "XXXXXXXXXXXX",                                 
    "time": "2018-08-30T21:42:18Z",                              // End-time of action. Format: yyyy-MM-dd'T'hh:mm:ssZ 
    "region": "us-east-1",                                       // AWS Control Tower home region.
    "resources": [ ],
    "detail": {
        "eventVersion": "1.05",
        "userIdentity": {
            "accountId": "XXXXXXXXXXXX",                    
            "invokedBy": "AWS Internal"
        },
        "eventTime": "2018-08-30T21:42:18Z",                  
        "eventSource": "controltower.amazonaws.com",             
        "eventName": "EnableGuardrail",                   
        "awsRegion": "us-east-1",                              
        "sourceIPAddress": "AWS Internal",
        "userAgent": "AWS Internal",
        "eventID": "0000000-0000-0000-1111-123456789012",       
        "readOnly": false,
        "eventType": "AwsServiceEvent",
        "serviceEventDetails": {
            "enableGuardrailStatus": {
                "organizationalUnits": [
                    {
                      "organizationalUnitName": "Custom",
                      "organizationalUnitId": "ou-vwxy-18vy4yro"
                    }
                  ],
                  "guardrails": [
                    {
                      "guardrailId": "AWS-GR_RDS_INSTANCE_PUBLIC_ACCESS_CHECK",
                      "guardrailBehavior": "DETECTIVE"
                    }
                  ],
                  "state": "SUCCEEDED",
                  "message": "AWS Control Tower successfully enabled a guardrail on an organizational unit.",
                  "requestTimestamp": "2019-11-12T09:01:07+0000",
                  "completedTimestamp": "2019-11-12T09:01:54+0000"
                }
        }
    }
}
```

## `DisableGuardrail`<a name="disable-control"></a>

This lifecycle event records whether AWS Control Tower successfully disabled a control on an OU that is being managed by AWS Control Tower\. This event corresponds to the AWS Control Tower `DisableGuardrail` CloudTrail event\. The lifecycle event log includes the `guardrailId` and `guardrailBehavior` of the control, and the `organizationalUnitName` and `organizationalUnitId` of the OU on which the control is disabled\. 

```
{
    "version": "0",
    "id": "999cccaa-eaaa-0000-1111-123456789012",     
    "detail-type": "AWS Service Event via CloudTrail",
    "source": "aws.controltower",
    "account": "XXXXXXXXXXXX",                         
    "time": "2018-08-30T21:42:18Z",                   
    "region": "us-east-1",                           
    "resources": [ ],
    "detail": {
        "eventVersion": "1.05",
        "userIdentity": {
            "accountId": "XXXXXXXXXXXX",                 
            "invokedBy": "AWS Internal"
        },
        "eventTime": "2018-08-30T21:42:18Z",            
        "eventSource": "controltower.amazonaws.com",
        "eventName": "DisableGuardrail",                 
        "awsRegion": "us-east-1",                            
        "sourceIPAddress": "AWS Internal",
        "userAgent": "AWS Internal",
        "eventID": "0000000-0000-0000-1111-123456789012",     
        "readOnly": false,
        "eventType": "AwsServiceEvent",
        "serviceEventDetails": {
            "disableGuardrailStatus": {
                   "organizationalUnits": [
                    {
                      "organizationalUnitName": "Custom",
                      "organizationalUnitId": "ou-vwxy-18vy4yro"
                    }
                  ],
                  "guardrails": [
                    {
                      "guardrailId": "AWS-GR_RDS_INSTANCE_PUBLIC_ACCESS_CHECK",
                      "guardrailBehavior": "DETECTIVE"
                    }
                  ],
                  "state": "SUCCEEDED",
                  "message": "AWS Control Tower successfully disabled a guardrail on an organizational unit.",
                  "requestTimestamp": "2019-11-12T09:01:07+0000",
                  "completedTimestamp": "2019-11-12T09:01:54+0000"
                }
            }
        }
    }
```

## `SetupLandingZone`<a name="setup-landing-zone"></a>

This lifecycle event records whether AWS Control Tower successfully set up a landing zone\. This event corresponds to the AWS Control Tower `SetupLandingZone` CloudTrail event\. The lifecycle event log includes the `rootOrganizationalId`, which is ID of the organization that AWS Control Tower creates from the management account\. The log entry also includes the `organizationalUnitName` and `organizationalUnitId` for each of of the OUs, and the `accountName` and `accountId` for each account, that are created when AWS Control Tower sets up the landing zone\.

```
{
    "version": "0",
    "id": "999cccaa-eaaa-0000-1111-123456789012",                // Request ID.
    "detail-type": "AWS Service Event via CloudTrail",
    "source": "aws.controltower",
    "account": "XXXXXXXXXXXX",                                   // Management account ID.
    "time": "2018-08-30T21:42:18Z",                              // Event time from CloudTrail.
    "region": "us-east-1",                                       // Management account CloudTrail region.
    "resources": [ ],
    "detail": {
        "eventVersion": "1.05",
        "userIdentity": {
            "accountId": "XXXXXXXXXXXX",                         // Management-account ID.
            "invokedBy": "AWS Internal"
        },
        "eventTime": "2018-08-30T21:42:18Z",                     // Timestamp when call was made. Format: yyyy-MM-dd'T'hh:mm:ssZ.
        "eventSource": "controltower.amazonaws.com",
        "eventName": "SetupLandingZone",
        "awsRegion": "us-east-1",                                // AWS Control Tower home region.
        "sourceIPAddress": "AWS Internal",
        "userAgent": "AWS Internal",
        "eventID": "CloudTrail_event_ID",                        // This value is generated by CloudTrail.
        "readOnly": false,
        "eventType": "AwsServiceEvent",
        "serviceEventDetails": {
            "setupLandingZoneStatus": {
                "state": "SUCCEEDED",                             // Status of entire lifecycle operation.
                "message": "AWS Control Tower successfully set up a new landing zone.",                
                "rootOrganizationalId" : "r-1234",
                "organizationalUnits" : [                         // Use a list.
                  {
                    "organizationalUnitName": "Security",             // Security OU name.
                    "organizationalUnitId": "ou-adpf-302pk332"    // Security OU ID.
                  },
                  {
                    "organizationalUnitName": "Custom",           // Custom OU name.
                    "organizationalUnitId": "ou-adpf-302pk332"    // Custom OU ID. 
                  },
                ],
               "accounts": [                                      // All created accounts are here. Use a list of "account" objects.

                  {
                    "accountName": "Audit",  
                    "accountId": "XXXXXXXXXXXX"                        
                  },
                  {
                    "accountName": "Log archive",                 
                    "accountId": "XXXXXXXXXXXX"
                  }
              ],
              "requestedTimestamp": "2018-08-30T21:42:18Z",
              "completedTimestamp": "2018-08-30T21:42:18Z"
            }
        }
    }
}
```

## `UpdateLandingZone`<a name="update-landing-zone"></a>

This lifecycle event records whether AWS Control Tower successfully updated your existing landing zone\. This event corresponds to the AWS Control Tower `UpdateLandingZone` CloudTrail event\. The lifecycle event log includes the `rootOrganizationalId`, which is ID of the \(updated\) organization governed by AWS Control Tower\. The log entry also includes the `organizationalUnitName` and `organizationalUnitId` for each of of the OUs, and the `accountName` and `accountId` for each account, that was created previously, when AWS Control Tower originally set up the landing zone\.

```
{
    "version": "0",
    "id": "999cccaa-eaaa-0000-1111-123456789012",                // Request ID.
    "detail-type": "AWS Service Event via CloudTrail",
    "source": "aws.controltower",
    "account": "XXXXXXXXXXXX",                                   // Management account ID.
    "time": "2018-08-30T21:42:18Z",                              // Event time from CloudTrail.
    "region": "us-east-1",                                       // Management account CloudTrail region.
    "resources": [ ],
    "detail": {
        "eventVersion": "1.05",
        "userIdentity": {
            "accountId": "XXXXXXXXXXXX",                         // Management account ID.
            "invokedBy": "AWS Internal"
        },
        "eventTime": "2018-08-30T21:42:18Z",                     // Timestamp when call was made. Format: yyyy-MM-dd'T'hh:mm:ssZ.
        "eventSource": "controltower.amazonaws.com",
        "eventName": "UpdateLandingZone",
        "awsRegion": "us-east-1",                                // AWS Control Tower home region.
        "sourceIPAddress": "AWS Internal",
        "userAgent": "AWS Internal",
        "eventID": "CloudTrail_event_ID",                        // This value is generated by CloudTrail.

        "readOnly": false,
        "eventType": "AwsServiceEvent",
        "serviceEventDetails": {
            "updateLandingZoneStatus": {
                "state": "SUCCEEDED",                            // Status of entire operation.
                "message": "AWS Control Tower successfully updated a landing zone.",                

                "rootOrganizationalId" : "r-1234",
                "organizationalUnits" : [                         // Use a list.
                  {
                    "organizationalUnitName": "Security",             // Security OU name.
                    "organizationalUnitId": "ou-adpf-302pk332"    // Security OU ID.
                  },
                  {
                    "organizationalUnitName": "Custom",            // Custom OU name.
                    "organizationalUnitId": "ou-adpf-302pk332"     // Custom OU ID.
                  },
                ],
               "accounts": [                                       // All created accounts are here. Use a list of "account" objects.

                  {
                    "accountName": "Audit",  
                    "accountId": "XXXXXXXXXXXX"                        
                  },
                  {
                    "accountName": "Log archive",                 
                    "accountId": "XXXXXXXXXX"
                  }
              ],
              "requestedTimestamp": "2018-08-30T21:42:18Z",
              "completedTimestamp": "2018-08-30T21:42:18Z"
            }
        }
    }
}
```

## `RegisterOrganizationalUnit`<a name="register-organizational-unit"></a>

This lifecycle event records whether AWS Control Tower successfully enabled its governance features on an OU\. This event corresponds to the AWS Control Tower `RegisterOrganizationalUnit` CloudTrail event\. The lifecycle event log includes the `organizationalUnitName` and `organizationalUnitId` of the OU that AWS Control Tower has brought under its governance\.

```
{
    "version": "0",
    "id": "999cccaa-eaaa-0000-1111-123456789012",            
    "detail-type": "AWS Service Event via CloudTrail", 
    "source": "aws.controltower",
    "account": "123456789012",                               
    "time": "2018-08-30T21:42:18Z",                  
    "region": "us-east-1",                       
    "resources": [ ],
    "detail": {
        "eventVersion": "1.05",
        "userIdentity": {
            "accountId": "XXXXXXXXXXXX",                
            "invokedBy": "AWS Internal"
        },
        "eventTime": "2018-08-30T21:42:18Z",               
        "eventSource": "controltower.amazonaws.com",
        "eventName": "RegisterOrganizationalUnit",        
        "awsRegion": "us-east-1",                           
        "sourceIPAddress": "AWS Internal",
        "userAgent": "AWS Internal",
        "eventID": "0000000-0000-0000-1111-123456789012",    
        "readOnly": false,
        "eventType": "AwsServiceEvent",
        "serviceEventDetails": {
            "registerOrganizationalUnitStatus": {
                "state": "SUCCEEDED",                        

                "message": "AWS Control Tower successfully registered an organizational unit.",

                "organizationalUnit" :                        
                  {
                    "organizationalUnitName": "Test",            
                    "organizationalUnitId": "ou-adpf-302pk332"    
                  }
                "requestedTimestamp": "2018-08-30T21:42:18Z",
                "completedTimestamp": "2018-08-30T21:42:18Z" 
            }
        }
    }
}
```

## `DeregisterOrganizationalUnit`<a name="deregister-organizational-unit"></a>

This lifecycle event records whether AWS Control Tower successfully disabled its governance features on an OU\. This event corresponds to the AWS Control Tower `DeregisterOrganizationalUnit` CloudTrail event\. The lifecycle event log includes the `organizationalUnitName` and `organizationalUnitId` of the OU on which AWS Control Tower has disabled its governance features\.

```
{
    "version": "0",
    "id": "999cccaa-eaaa-0000-1111-123456789012",    
    "detail-type": "AWS Service Event via CloudTrail",
    "source": "aws.controltower",
    "account": "XXXXXXXXXXXX",                  
    "time": "2018-08-30T21:42:18Z", 
    "region": "us-east-1",            
    "resources": [ ],
    "detail": {
        "eventVersion": "1.05",
        "userIdentity": {
            "accountId": "XXXXXXXXXXXX",              
            "invokedBy": "AWS Internal"
        },
        "eventTime": "2018-08-30T21:42:18Z",               
        "eventSource": "controltower.amazonaws.com",
        "eventName": "DeregisterOrganizationalUnit",     
        "awsRegion": "us-east-1",                       
        "sourceIPAddress": "AWS Internal",
        "userAgent": "AWS Internal",
        "eventID": "0000000-0000-0000-1111-123456789012", 
        "readOnly": false,
        "eventType": "AwsServiceEvent",
        "serviceEventDetails": {
            "deregisterOrganizationalUnitStatus": {                 
                "state": "SUCCEEDED",               
                "message": "AWS Control Tower successfully deregistered an organizational unit, and enabled mandatory guardrails on the new organizational unit.",
                "organizationalUnit" :                        
                  {
                    "organizationalUnitName": "Test",                   // Foundational OU name.
                    "organizationalUnitId": "ou-adpf-302pk332"          // Foundational OU ID.
                  },
                "requestedTimestamp": "2018-08-30T21:42:18Z", 
                "completedTimestamp": "2018-08-30T21:42:18Z"  
            }
            }
        }
    }
```

## `PrecheckOrganizationalUnit`<a name="precheck-organizational-unit"></a>

This lifecycle event records whether AWS Control Tower successfully performed prechecks on an OU\. This event corresponds to the AWS Control Tower `PrecheckOrganizationalUnit` CloudTrail event\. The lifecycle event log contains a field for the `Id`, `Name`, and `failedPrechecks` values, for each resource on which AWS Control Tower has performed prechecks during the OU registration process\.

The event log also contains information about the nested accounts on which the prechecks were performed, including the `accountName`, `accountId`, and `failedPrechecks` fields\.

If the `failedPrechecks` value is empty, it means that all prechecks for that resource passed successfully\.
+ This event is emitted only if there is a precheck failure\.
+ This event is not emitted if you are registering an empty OU\.

Example of event:

```
{
  "eventVersion": "1.08",
  "userIdentity": {
    "accountId": "XXXXXXXXXXXX",
    "invokedBy": "AWS Internal"
  },
  "eventTime": "2021-09-20T22:45:43Z",
  "eventSource": "controltower.amazonaws.com",
  "eventName": "PrecheckOrganizationalUnit",
  "awsRegion": "us-west-2",
  "sourceIPAddress": "AWS Internal",
  "userAgent": "AWS Internal",
  "eventID": "b41a9d67-0da4-4dc5-a87a-25fa19dc5305",
  "readOnly": false,
  "eventType": "AwsServiceEvent",
  "managementEvent": true,
  "recipientAccountId": "XXXXXXXXXXXX",
  "serviceEventDetails": {
    "precheckOrganizationalUnitStatus": {
      "organizationalUnit": {
        "organizationalUnitName": "Ou-123",
        "organizationalUnitId": "ou-abcd-123456",
        "failedPrechecks": [
            "SCP_CONFLICT"
          ]
      },
      "accounts": [
        {
          "accountName": "Child Account 1",
          "accountId": "XXXXXXXXXXXX",
          "failedPrechecks": [
            "FAILED_TO_ASSUME_ROLE"
          ]
        },
        {
          "accountName": "Child Account 2",
          "accountId": "XXXXXXXXXXXX",
          "failedPrechecks": [
            "FAILED_TO_ASSUME_ROLE"
          ]
        },
        {
          "accountName": "Management Account",
          "accountId": "XXXXXXXXXXXX",
          "failedPrechecks": [
            "MISSING_PERMISSIONS_AF_PRODUCT"
          ]
        },
        {
          "accountName": "Child Account 3",
          "accountId": "XXXXXXXXXXXX",
          "failedPrechecks": []
        },
        ...
      ],
      "state": "FAILED",
      "message": "AWS Control Tower failed to register an organizational unit due to pre-check failures. Go to the OU details page to download a list of failed pre-checks for the OU and accounts within.",
      "requestedTimestamp": "2021-09-20T22:44:02+0000",
      "completedTimestamp": "2021-09-20T22:45:43+0000"
    }
  },
  "eventCategory": "Management"
}
```