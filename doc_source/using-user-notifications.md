# Using AWS User Notifications with AWS Control Tower<a name="using-user-notifications"></a>

You can use [AWS User Notifications](https://docs.aws.amazon.com/notifications/latest/userguide/what-is.html) to set up delivery channels to be notified about AWS Control Tower events\. You receive a notification when an event matches a rule that you specify\. You can receive notifications for events through multiple channels, including email, [AWS Chatbot](https://docs.aws.amazon.com/chatbot/latest/adminguide/what-is.html) chat notifications, or [AWS Console Mobile App](https://docs.aws.amazon.com/consolemobileapp/latest/userguide/what-is-consolemobileapp.html) push notifications\. You can also see notifications in the Console Notifications Center\.

AWS User Notifications supports aggregation, which can reduce the number of notifications you receive during specific events\. Notifications also are visible in the Console Notifications Center\.

The advantages of subscribing to notifications through AWS User Notifications instead of EventBridge include:
+ A friendlier user interface \(UI\)\.
+ Integration with the AWS console, in the bell/notifications area on the global navigation bar\.
+ Native support for email notifications, there's no need to set up Amazon SNS\.
+ Most notably, support for mobile push notifications, exclusive to AWS User Notifications\.

For example, one type of notification you may wish to receive is in case of Security Hub critical and high severity findings\. A code snippet in JSON to set up that notification subscription may look something like this:

```
{
  "detail": {
    "findings": {
      "Compliance": {
        "Status": ["FAILED", "WARNING", "NOT_AVAILABLE"]
      },
      "RecordState": ["ACTIVE"],
      "Severity": {
        "Label": ["CRITICAL", "HIGH"]
      },
      "Workflow": {
        "Status": ["NEW", "NOTIFIED"]
      }
    }
  }
}
```

**Event filtering**
+ You can filter events by service and name using the filters available on the AWS User Notifications console\.
+ You can filter events by specific properties if you create your own EventBridge filter from JSON code\.

**Example AWS Control Tower event**

Here is a generalized example event for AWS Control Tower\.
+ It an EventBridge event\.
+ You can subscribe to EventBridge events \(such as this one\) using AWS User Notifications\.

```
{
    "version": "0",
    "id": "<id>", // alphanumeric string
    "detail-type": "AWS Service Event via CloudTrail",
    "source": "aws.controltower",
    "account": "<account ID>", // Management account ID. 
    "time": "<date>", // Format: yyyy-MM-dd'T'hh:mm:ssZ 
    "region": "<region>", // AWS Control Tower home region.
    "resources": [],
    "detail": {
        "eventVersion": "1.05",
        "userIdentity": {
            "accountId": "121212121212",
            "invokedBy": "AWS Internal"
        },
        "eventTime": "2018-08-30T21:42:18Z", // Timestamp when call was made. Format: yyyy-MM-dd'T'hh:mm:ssZ.
        "eventSource": "controltower.amazonaws.com",
        "eventName": "<event name>", // one of the 9 event names in https://docs.aws.amazon.com/controltower/latest/userguide/lifecycle-events.html
        "awsRegion": "<region>",
        "sourceIPAddress": "AWS Internal",
        "userAgent": "AWS Internal",
        "eventID": "<id>",
        "readOnly": false,
        "eventType": "AwsServiceEvent",
        "serviceEventDetails": {
            // the contents of this object vary depending on the event subtype and event state
        }
    }
}
```