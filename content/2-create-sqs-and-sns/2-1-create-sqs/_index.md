---
title : "Create queue"
date :  2025-02-11
weight : 1
chapter : false
pre : " <b> 2.1. </b> "
---
1. Open [Amazon SQS console](https://us-east-1.console.aws.amazon.com/sqs/v2/home?region=us-east-1#/homepage).
    - Click **Queues** on the left menu.
    - Click **Create queue**.
      ![CreateSQS](/images/temp/1/9.png?width=90pc)

2. At **Create queue** page.
    - Choose **Standard** Type.
    - Enter ``checkout-queue`` Name.
      ![CreateSQS](/images/temp/1/10.png?width=90pc)
    - Leave as default, scroll down and click **Create queue** button.
      ![CreateSQS](/images/temp/1/11.png?width=90pc)

3. At **checkout-queue** page.
    - Click **Send and receive messages** button.
      ![CreateSQS](/images/temp/1/12.png?width=90pc)

4. At **Send and receive messages** page.
    - Enter ``The first message`` at **Message body**.
    - Click **Send message** to send message to queue.
      ![CreateSQS](/images/temp/1/13.png?width=90pc)
    - Click **Poll for messages** to receive all messages sent to queue.
    - Click to the message that is just shown.
      ![CreateSQS](/images/temp/1/14.png?width=90pc)
    - At the **Message: ...** popup, check **Body** message and click **Done** button.
      ![CreateSQS](/images/temp/1/15.png?width=90pc)
    - Click on the checkbox of the message that is just shown.
    - Click **Delete** button.
      ![CreateSQS](/images/temp/1/16.png?width=90pc)
    - At the **Delete Messages** popup, click **Delete** button.
      ![CreateSQS](/images/temp/1/17.png?width=90pc)
