# A Solution to Receive Notifications when IAM Configuration Changes
This reference solution allows an AWS administrator to know when any modifications are done in the IAM service. Whenever there is a change in IAM users/roles/policies/groups, the administrator is notified via e-mail. It is an implementation of the [sample solution](https://aws.amazon.com/blogs/security/how-to-receive-alerts-when-your-iam-configuration-changes/) proposed by AWS.

<br/>

> Note that this process is a reactive approach because it gets triggered after the user has already modified IAM configuration.

<br/>

![Architecture diagram](https://github.com/oyenamit/aws-monitor-iam-changes/blob/master/docs/architecture.png)


## Solution Components
The CloudFormation template contains the following:
1. A CloudTrail that logs IAM API calls to CloudWatch Logs.
2. A CloudWatch Log Group.
3. An IAM role that allows CloudTrail to log events to CloudWatch Logs.
4. A Metric Filter for IAM APIs (APIs that create/modify/delete IAM resources).
5. A CloudWatch Alarm that gets triggered by the metric filter.
6. An SNS notification topic that sends e-mail when the CloudWatch Alarm switches to ALARM state.

## Input Parameters
The Cloudformation template requires the following input:
1. *TrailDestS3BucketName*: The name of S3 bucket where CloudTrail will save logs. The bucket must have its bucket policy set to allow CloudTrail to write these logs.
2. *NotificationEmail*: E-mail ID where notifications should be sent.

## Instructions
1. Before testing the solution, ensure that the SNS e-mail subscription has been confirmed.
2. Login to AWS with user who has access to IAM service.
3. Make changes to IAM configuration (for example, create a new role)
4. The solution will detect this change and send an e-mail alert

## Cleanup
To remove all created resources:
1. Delete the AWS CloudFormation stack.
2. Revert any changes done to IAM configuration as part of testing 

<br/><br/>


> **Note 1**: This solution should be deployed in us-east-1 (N. Virginia) region. This is because IAM is a global service and its API calls are only available to CloudTrail in that region.

<br/>

> **Note 2**: When a user changes IAM configuration, it can take several minutes before the API call is logged to CloudWatch via CloudTrail. As per [AWS documentation](https://aws.amazon.com/cloudtrail/faqs/), CloudTrail delivers an event within 15 minutes of the API call.

<br/>

> **Note 3**: When this solution is deployed, the e-mail provided as input parameter is attached with a new SNS topic. The user needs to confirm the subscription before any notification e-mails can be sent to it.
