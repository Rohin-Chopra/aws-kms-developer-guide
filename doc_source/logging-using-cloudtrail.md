# Logging AWS KMS API calls with AWS CloudTrail<a name="logging-using-cloudtrail"></a>

AWS KMS is integrated with [AWS CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/), a service that records all calls to AWS KMS by users, roles, and other AWS services\. CloudTrail captures all API calls to AWS KMS as events, including calls from the AWS KMS console, AWS KMS APIs, the AWS Command Line Interface \(AWS CLI\), and AWS Tools for PowerShell\.

CloudTrail logs all AWS KMS operations, including read\-only operations, such as [ListAliases](https://docs.aws.amazon.com/kms/latest/APIReference/API_ListAliases.html) and [GetKeyRotationStatus](https://docs.aws.amazon.com/kms/latest/APIReference/API_GetKeyRotationStatus.html), operations that manage KMS keys, such as [CreateKey](https://docs.aws.amazon.com/kms/latest/APIReference/API_CreateKey.html) and [PutKeyPolicy](https://docs.aws.amazon.com/kms/latest/APIReference/API_PutKeyPolicy.html), and [cryptographic operations](concepts.md#cryptographic-operations), such as [GenerateDataKey](https://docs.aws.amazon.com/kms/latest/APIReference/API_GenerateDataKey.html) and [Decrypt](https://docs.aws.amazon.com/kms/latest/APIReference/API_Decrypt.html)\.

CloudTrail logs successful operations and attempted calls that failed, such as when the caller is denied access to a resource\. Operations on [KMS keys in other accounts](key-policy-modifying-external-accounts.md) are logged in both the caller account and the KMS key owner account\. However, cross\-account AWS KMS requests that are rejected because access is denied are logged only in the caller's account\.

For security reasons, some fields are omitted from AWS KMS log entries, such as the `Plaintext` parameter of an [Encrypt](https://docs.aws.amazon.com/kms/latest/APIReference/API_Encrypt.html) request, and the response to [GetKeyPolicy](https://docs.aws.amazon.com/kms/latest/APIReference/API_GetKeyPolicy.html) or any cryptographic operation\.

Although, by default, all AWS KMS actions are logged as CloudTrail events, you can exclude AWS KMS actions from a CloudTrail trail\. For details, see [Excluding AWS KMS events from a trail](#filtering-kms-events)\.

**Topics**
+ [Logging events in CloudTrail](#kms-info-in-cloudtrail)
+ [Excluding AWS KMS events from a trail](#filtering-kms-events)
+ [Examples of AWS KMS log entries](understanding-kms-entries.md)

## Logging events in CloudTrail<a name="kms-info-in-cloudtrail"></a>

CloudTrail is enabled on your AWS account when you create the account\. When activity occurs in AWS KMS, that activity is recorded in a CloudTrail event along with other AWS service events in **Event history**\. You can view, search, and download recent events in your AWS account\. For more information, see [Viewing Events with CloudTrail Event History](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/view-cloudtrail-events.html)\. 

For an ongoing record of events in your AWS account, including events for AWS KMS, create a trail\. A trail enables CloudTrail to deliver log files to an Amazon S3 bucket\. By default, when you create a trail in the console, the trail applies to all regions\. The trail logs events from all regions in the AWS partition and delivers the log files to the Amazon S3 bucket that you specify\. Additionally, you can configure other AWS services to further analyze and act upon the event data collected in CloudTrail logs\. For more information, see:
+ [Overview for Creating a Trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-and-update-a-trail.html)
+ [CloudTrail Supported Services and Integrations](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-aws-service-specific-topics.html#cloudtrail-aws-service-specific-topics-integrations)
+ [Configuring Amazon SNS Notifications for CloudTrail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/getting_notifications_top_level.html)
+ [Receiving CloudTrail Log Files from Multiple Regions](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/receive-cloudtrail-log-files-from-multiple-regions.html) and [Receiving CloudTrail Log Files from Multiple Accounts](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-receive-logs-from-multiple-accounts.html)

To learn more about CloudTrail, see the [AWS CloudTrail User Guide](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/)\. To learn about other ways to monitor the use of your KMS keys, see [Monitoring AWS KMS keys](monitoring-overview.md)\.

Every event or log entry contains information about who generated the request\. The identity information helps you determine the following: 
+ If the request was made with root or IAM user credentials\.
+ If the request was made with temporary security credentials for a role or federated user\.
+ If the request was made by another AWS service\.

For more information, see the [CloudTrail userIdentity Element](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-event-reference-user-identity.html)\.

## Excluding AWS KMS events from a trail<a name="filtering-kms-events"></a>

Most AWS KMS users rely on the events in a CloudTrail trail to provide a record of the use and management of their AWS KMS resources\. The trail can be an valuable source of data for auditing critical events, such as creating, disabling, and deleting AWS KMS keys, changing key policy, and the use of your KMS keys by AWS services on your behalf\. In some cases, the metadata in a CloudTrail log entry, such as the [encryption context](concepts.md#encrypt_context) in an encryption operation, can help you to avoid or resolve errors\.

However, because AWS KMS can generate a large number of events, AWS CloudTrail lets you exclude AWS KMS events from a trail\. This per\-trail setting excludes all AWS KMS events; you cannot exclude particular AWS KMS events\.

**Warning**  
Excluding AWS KMS events from a CloudTrail Log can obscure actions that use your KMS keys\. Be cautious when giving principals the `cloudtrail:PutEventSelectors` permission that is required to perform this operation\.

To exclude AWS KMS events from a trail: 
+ In the CloudTrail console, use the **Log Key Management Service events** setting when you [create a trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-create-a-trail-using-the-console-first-time.html) or [update a trail](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-update-a-trail-console.html)\. For instructions, see [Logging Management Events with the AWS Management Console](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/logging-management-and-data-events-with-cloudtrail.html) in the AWS CloudTrail User Guide\.
+ In the CloudTrail API, use the [PutEventSelectors](https://docs.aws.amazon.com/awscloudtrail/latest/APIReference/API_PutEventSelectors.html) operation\. Add the `ExcludeManagementEventSources` attribute to your event selectors with a value of `kms.amazonaws.com`\. For an example, see [Example: A trail that does not log AWS Key Management Service events](https://docs.aws.amazon.com/awscloudtrail/latest/userguide/cloudtrail-additional-cli-commands.html#configuring-event-selector-example-kms) in the AWS CloudTrail User Guide\.

You can disable this exclusion at any time by changing the console setting or the event selectors for a trail\. The trail will then start recording AWS KMS events\. However, it cannot recover AWS KMS events that occurred while the exclusion was effective\.

When you exclude AWS KMS events by using the console or API, the resulting CloudTrail `PutEventSelectors` API operation is also logged in your CloudTrail Logs\. If AWS KMS events don't appear in your CloudTrail Logs, look for a `PutEventSelectors` event with the `ExcludeManagementEventSources` attribute set to `kms.amazonaws.com`\.