---
title : "Create SNS topic"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 2.2. </b> "
---
1. Open [Amazon SNS console](https://us-east-1.console.aws.amazon.com/sns/v3/home?region=us-east-1#/dashboard)
    - Click **Topics** on the left menu.
    - Click **Create topic** button.
      ![CreateSNS](https://chaunguyen3rd.github.io/000083-Book-store-Decouple-order-process-with-SQS-and-SNS/images/temp/1/18.png?width=90pc)

2. At **Create topic** page.
    - Click **Standard** at **Type**.
    - Enter ``order-notice`` at **Name**.
      ![CreateSNS](https://chaunguyen3rd.github.io/000083-Book-store-Decouple-order-process-with-SQS-and-SNS/images/temp/1/19.png?width=90pc)
    - Leave as default, scroll down and click **Create topic** button.
      ![CreateSNS](https://chaunguyen3rd.github.io/000083-Book-store-Decouple-order-process-with-SQS-and-SNS/images/temp/1/20.png?width=90pc)

3. At **order-notice** page.
    - Click **Subscriptions** tab.
    - Click **Create subscription** button.
      ![CreateSNS](https://chaunguyen3rd.github.io/000083-Book-store-Decouple-order-process-with-SQS-and-SNS/images/temp/1/21.png?width=90pc)

4. At **Create subscription** page.
    - Choose **Email** at **Protocol**.
    - Enter your email at **Endpoint**.
    - Click **Create subscription**.
      ![CreateSNS](https://chaunguyen3rd.github.io/000083-Book-store-Decouple-order-process-with-SQS-and-SNS/images/temp/1/22.png?width=90pc)

5. Back to **order-notice** page.
    - Click **Subscriptions** tab.
    - Check the subscription that is just created with **Pending confirmation** status.
      ![CreateSNS](https://chaunguyen3rd.github.io/000083-Book-store-Decouple-order-process-with-SQS-and-SNS/images/temp/1/23.png?width=90pc)

6. Open your email box, search mail sent from **<no-reply@sns.amazonaws.com>**.
    - Click on **Confirm subscription** link.
      ![CreateSNS](https://chaunguyen3rd.github.io/000083-Book-store-Decouple-order-process-with-SQS-and-SNS/images/temp/1/24.png?width=90pc)
    - It will direct you to a new tab.
      ![CreateSNS](https://chaunguyen3rd.github.io/000083-Book-store-Decouple-order-process-with-SQS-and-SNS/images/temp/1/25.png?width=90pc)

7. Back to **order-notice** page, check the status **Confirmed** of the subscription that you just created.
    ![CreateSNS](https://chaunguyen3rd.github.io/000083-Book-store-Decouple-order-process-with-SQS-and-SNS/images/temp/1/26.png?width=90pc)
