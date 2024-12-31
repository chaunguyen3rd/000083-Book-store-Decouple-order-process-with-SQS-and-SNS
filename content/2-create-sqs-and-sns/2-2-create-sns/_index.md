---
title : "Create SNS topic"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 2.2. </b> "
---
1. Navigate [Amazon SNS console](https://ap-southeast-1.console.aws.amazon.com/sns/v3/home?region=ap-southeast-1#/dashboard)

![CreateSNS](/images/2-create-sqs-and-sns/2-2-create-sns-1.png?featherlight=false&width=90pc)

2. Click **Topics** on the left menu
- Click **Create topic**

![CreateSNS](/images/2-create-sqs-and-sns/2-2-create-sns-2.png?featherlight=false&width=90pc)

3. Select **Stardard** for the topic type
- Ente topic name: `order-notic`

![CreateSNS](/images/2-create-sqs-and-sns/2-2-create-sns-3.png?featherlight=false&width=90pc)

4. Scroll down to bottom, click **Create topic**

![CreateSNS](/images/2-create-sqs-and-sns/2-2-create-sns-4.png?featherlight=false&width=90pc)

5. Click on the topic you just created

![CreateSNS](/images/2-create-sqs-and-sns/2-2-create-sns-5.png?featherlight=false&width=90pc)

6. Click **Create subscription**

![CreateSNS](/images/2-create-sqs-and-sns/2-2-create-sns-6.png?featherlight=false&width=90pc)

7. Select **Protocol** is **Email**

![CreateSNS](/images/2-create-sqs-and-sns/2-2-create-sns-7.png?featherlight=false&width=90pc)

8. Enter the email with which you registered an account on Cognito and are admin
- Click **Create subcription**

![CreateSNS](/images/2-create-sqs-and-sns/2-2-create-sns-8.png?featherlight=false&width=90pc)

9. Then, status of subcription is pending confirmation

![CreateSNS](/images/2-create-sqs-and-sns/2-2-create-sns-9.png?featherlight=false&width=90pc)

10. To confirm your email, open the registered email
- Search mail sent from **no-reply@sns.amazonaws.com**, click to confrimation link

![CreateSNS](/images/2-create-sqs-and-sns/2-2-create-sns-10.png?featherlight=false&width=90pc)

11. Back to SNS topic dashboard, subscription confirmed

![CreateSNS](/images/2-create-sqs-and-sns/2-2-create-sns-11.png?featherlight=false&width=90pc)
