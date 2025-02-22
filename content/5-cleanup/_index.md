---
title : "Clean up"
date :  2025-02-11
weight : 5
chapter : false
pre : " <b> 5. </b> "
---

{{% notice note %}}
It will take a bit time to finish the cleanup
{{% /notice %}}

1. Cleanup the Route 53
    - Open [AWS Route 53 console](https://us-east-1.console.aws.amazon.com/route53/v2/home?region=us-east-1).
    - Click **Hosted zones** on the left menu.
    - Choose your **Hosted zones** and cleanup all the records if possible.

2. Empty S3 bucket.
    - Open [AWS S3 console](https://s3.console.aws.amazon.com/s3/buckets?region=us-east-1).
    - Select **fcj-book-shop-by-myself**.
    - Click **Empty**.
    - Enter **permanently delete**.
    - Click **Empty**.
    - Do the same for bucket starting with **aws-sam-cli-managed-default-** and **book-image-resize-shop-by-myself**.

3. Delete CloudFormation stacks.
    - Execute the below command to delete the AWS SAM application.

      ```bash
      sam delete --stack-name fcj-book-store
      sam delete --stack-name aws-sam-cli-managed-default
      ```

    - If you have issues when deleting with command. Open [AWS Cloudformation console](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/getting-started). Then, delete all stacks related to this workshop.

4. Cleanup SQS.
    - Open [Amazon SQS console](https://us-east-1.console.aws.amazon.com/sqs/v2/home?region=us-east-1#/homepage).
    - Choose **checkout-queue**.
    - Click **Delete**.
    - Enter `confirm`.
    - Click **Delete**.

5. Cleanup SNS.
    - Open [Amazon SNS console](https://us-east-1.console.aws.amazon.com/sns/v3/home?region=us-east-1#/dashboard).
    - Choose **order-notice** at **Topics**.
    - Click **Delete**.
    - Enter `delete me`.
    - Click **Delete**.
