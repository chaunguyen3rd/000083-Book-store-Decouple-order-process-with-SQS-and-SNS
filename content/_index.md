---
title : "Serverless - Processing orders with SQS and SNS"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
---
# Serverless - Processing orders with SQS and SNS

#### Overview

In the workshop 6 of this series, we will process user orders using Amazon SQS and SNS. When user places an order, the order will be send to the queue and SNS will notify the admin about about the new order. Admin has the right to view all orders and process or delete them.

The architecture of the web application:
![SeverlessExample](/images/serverless-diagram.png?featherlight=false&width=50pc)

- When user places an order (click **Checkout**), the POST API /books/order puts the order's information into a queue you create, then pulish a message via Amazon SNS topic
- GET API /books/order is called when admin uses order management to get all pending and processed orders in DynamoDB table
- When the Admin processes the order (click **Handle**), the POST API /books/order/handle will write the order to DynamoDB and remove it from the queue
- When Admin deletes an order (press **Delete**), DELETE API /books/order will remove the order from the queue

#### Amazon Simple Queue Service
Amazon Simple Queue Service (Amazon SQS) offers a secure, durable, and available hosted queue that lets you integrate and decouple distributed software systems and components. It can scale independently from our application. It provides a generic web services API that you can access using any programming language that the AWS SDK supports.

There are three main parts in a distributed messaging system: the components of your distributed system, your queue (distributed on Amazon SQS servers), and the messages in the queue. Example: your system has several producers (components that send messages to the queue) and consumers (components that receive messages from the queue). The queue (which holds messages) stores the messages on Amazon SQS servers.

![SeverlessExample](/images/sqs-basic.png?featherlight=false&width=55pc)


#### Amazon Simple Notification Service
Amazon Simple Notification Service (Amazon SNS) is a managed service that provides message delivery from publishers to subscribers (also known as producers and consumers). Publishers communicate asynchronously with subscribers by sending messages to a topic, which is a logical access point and communication channel. Clients can subscribe to the SNS topic and receive published messages using a supported endpoint type, such as Amazon Kinesis Data Firehose, Amazon SQS, AWS Lambda, HTTP, email, mobile push notifications, and mobile text messages (SMS).

![SeverlessExample](/images/sns-basic.png?featherlight=false&width=50pc)

#### Content

 1. [Preparation](1-preparation/)
 2. [Create queue and SNS topic](2-create-sqs-and-sns/)
 3. [Create API and Lambda function](3-create-api-lambda-function/)
 4. [Test web operation](4-test-operation/)
 5. [Cleanup](5-cleanup)
