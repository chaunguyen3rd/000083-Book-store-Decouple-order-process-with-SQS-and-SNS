---
title : "Clean up"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b> 5. </b> "
---
1. Empty S3 bucket
- Open [AWS S3 console](https://s3.console.aws.amazon.com/s3/buckets?region=ap-southeast-1)
- Select **fcj-book-store**
- Click **Empty**
- Enter **permanently delete**
- Click **Empty**
- Do the same for bucket starting with **aws-sam-cli-managed-default-**
2. Delete CloudFormation stacks
- Execute the below command to delete the AWS SAM application
```
sam delete --stack-name fcj-book-store
sam delete --stack-name aws-sam-cli-managed-default
```
3. Delete queue
- Open [Amazon SQS console](https://ap-southeast-1.console.aws.amazon.com/sqs/v2/home?region=ap-southeast-1#/homepage)
- Select created queue
- Click **Delete**
- Enter **delete**
- Click **Delete**
4. Delete SNS topic and subcription
- Open [Amazon SNS console](https://ap-southeast-1.console.aws.amazon.com/sns/v3/home?region=ap-southeast-1#/topics)
- Select created topic
- Click **Delete**
- Enter **delete me**
- Click **Delete**
- Click **Subcriptions** tab
- Select created subcription
- Click **Delete**
- Click **Delete** again