---
title : "Create queue"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 2.1. </b> "
---
1. Open [Amazon SQS console](https://ap-southeast-1.console.aws.amazon.com/sqs/v2/home?region=ap-southeast-1#/homepage)

![CreateSQS](/images/2-create-sqs-and-sns/2-1-create-sqs-1.png?featherlight=false&width=90pc)

2. Click **Create queue**

![CreateSQS](/images/2-create-sqs-and-sns/2-1-create-sqs-2.png?featherlight=false&width=90pc)

3. Select **Stardard** for queue type
- Enter queue name: `checkout-queue`

![CreateSQS](/images/2-create-sqs-and-sns/2-1-create-sqs-3.png?featherlight=false&width=90pc)

4. Scroll down, click **Create queue**

![CreateSQS](/images/2-create-sqs-and-sns/2-1-create-sqs-4.png?featherlight=false&width=90pc)

5. Click **Send and receive messages**

![CreateSQS](/images/2-create-sqs-and-sns/2-1-create-sqs-5.png?featherlight=false&width=90pc)

6. Enter message content, such as: `The first order`
- Click **Send** to send message to queue

![CreateSQS](/images/2-create-sqs-and-sns/2-1-create-sqs-6.png?featherlight=false&width=90pc)

7. Click **Poll message** to receive all message sent to queue 

![CreateSQS](/images/2-create-sqs-and-sns/2-1-create-sqs-7.png?featherlight=false&width=90pc)

8. Click to the message is showing

![CreateSQS](/images/2-create-sqs-and-sns/2-1-create-sqs-8.png?featherlight=false&width=90pc)

9. The message content is displayed
- Ấn **Done**

![CreateSQS](/images/2-create-sqs-and-sns/2-1-create-sqs-9.png?featherlight=false&width=90pc)

10. Check to this message
- Click **Delete** to delete message

![CreateSQS](/images/2-create-sqs-and-sns/2-1-create-sqs-10.png?featherlight=false&width=90pc)

11. Click **Delete**

![CreateSQS](/images/2-create-sqs-and-sns/2-1-create-sqs-11.png?featherlight=false&width=90pc)
