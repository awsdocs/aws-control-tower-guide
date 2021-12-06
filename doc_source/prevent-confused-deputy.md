# Prevent cross\-service impersonation<a name="prevent-confused-deputy"></a>

 In AWS, cross\-service impersonation can result in the *confused deputy problem*\. When one service calls another service, cross\-service impersonation occurs if one service manipulates another service to use its permissions to act on a customer's resources in a way that's not otherwise permitted\. To prevent this attack, AWS provides tools to help you protect your data, so that only those services with legitimate permission can gain access to resources in your account\.

We recommend using the `aws:SourceArn` and `aws:SourceAccount` conditions in your policies, to limit the permissions that AWS Control Tower gives to another service for access to your resources\.
+ Use `aws:SourceArn` if you want only one resource to be associated with cross\-service access\.
+ Use `aws:SourceAccount` if you want to allow any resource in that account to be associated with cross\-service use\.
+ If the `aws:SourceArn` value does not contain the account ID, such as the ARN for an Amazon S3 bucket, you must use both conditions to limit permissions\.
+ If you use both conditions, and if the `aws:SourceArn` value contains the account ID, the `aws:SourceAccount` value and the account in the `aws:SourceArn` value must show the same account ID when used in the same policy statement

For more information and examples, see [Optional conditions for your role trust relationships](roles-how.md#conditions-for-role-trust)\.