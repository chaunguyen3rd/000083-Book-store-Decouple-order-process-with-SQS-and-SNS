---
title : "Test web operation"
date :  2025-02-11
weight : 4
chapter : false
pre : " <b> 4. </b> "
---

In this step, we will test Web operation.

You can download the image files here to add data to check the operation of the services.
    {{%attachments title="Images" pattern=".*\.(jpg|png)$"/%}}

1. Enter the following links in a new tab in your web browser: ``http://www.DOMAIN``, replace all DOMAIN with your domain name. All those links redirect to the new path, replace http with https.
    ![TestOperation](/images/temp/1/8.png?width=90pc)

2. Click **Create new book**.
    - Enter ID: ``1``.
    - Enter book name: ``Java``.
    - Enter author: ``Jame Patterson``.
    - Enter category: ``IT``.
    - Enter price: ``10.98``.
    - Enter description: ``A beginner's guide to learning the basic of Java``.
    - Click **Choose File** button và choose **LetGoBook.png`` image that you just downloaded.
    - Click **Create** button.
      ![TestOperation](/images/temp/1/69.png?width=90pc)
    - Click **OK** button when popup opens.
      ![TestOperation](/images/temp/1/70.png?width=90pc)

3. Create the new book as the previous step.
    - Click **Create new book**.
    - Enter ID: ``2``.
    - Enter book name: ``Let's Go``.
    - Enter author: ``Alex Edwards``.
    - Enter category: ``IT``.
    - Enter price: ``15.8``.
    - Enter description: ``A step-by-step guide to creating fast, secure web with Go``.
    - Click **Choose File** button và choose **LetGoBook.png`` image that you just downloaded.
    - Click **Create** button.
      ![TestOperation](/images/temp/1/71.png?width=90pc)
    - Click **OK** button when popup opens.
      ![TestOperation](/images/temp/1/70.png?width=90pc)

4. Back to Homepage.
    - Click **Home**.
    - Click **Add to cart** button to add both 2 books to the cart.
    - Then, click on **Cart** icon in the upper right corner.
      ![TestOperation](/images/temp/1/72.png?width=90pc)

5. At **Cart Items** page.
    - Click **Checkout** button.
      ![TestOperation](/images/temp/1/73.png?width=90pc)
    - Then, click **OK** button.
      ![TestOperation](/images/temp/1/74.png?width=90pc)

6. Open [Amazon SQS console](https://us-east-1.console.aws.amazon.com/sqs/v2/home?region=us-east-1#/homepage).
    - Click **Queues** on the left menu.
    - Click **checkout-queue** queue.
      ![TestOperation](/images/temp/1/75.png?width=90pc)
    - At **checkout-queue** page, click **Send and receive messages** button.
      ![TestOperation](/images/temp/1/76.png?width=90pc)
    - At **Send and receive messages** page, click **Poll for messages** button.
      ![TestOperation](/images/temp/1/77.png?width=90pc)
    - Then, click on the showing message.
      ![TestOperation](/images/temp/1/78.png?width=90pc)
    - Check the **Message: ...** popup and click **Done** button.
      ![TestOperation](/images/temp/1/79.png?width=90pc)

7. Open the email that you have subscribed to receive the notification.
    ![TestOperation](/images/temp/1/80.png?width=90pc)

8. Back to the application tab.
    - Click **Orders** and check the books you added to the cart.
      ![TestOperation](/images/temp/1/81.png?width=90pc)

9. Next, repeat the **5th** step to add some more orders as you want.

10. Open the application tab.
    - Click **Orders** and check the books you added to the cart.
      ![TestOperation](/images/temp/1/82.png?width=90pc)
    - Click **Handle** button and then click **OK** button on the popup.
      ![TestOperation](/images/temp/1/83.png?width=90pc)

11. Open [AWS DynamoDB](https://us-east-1.console.aws.amazon.com/dynamodbv2/home?region=us-east-1#tables).
    - Click **Tables** on the left menu.
    - Choose **OrdersTable** table.
      ![TestOperation](/images/temp/1/86.png?width=90pc)
    - At **OrdersTable** page, click **Explore table items** button.
      ![TestOperation](/images/temp/1/87.png?width=90pc)
    - You could see the two books in the order that you clicked the **Handle** button before.
      ![TestOperation](/images/temp/1/88.png?width=90pc)

12. Back to the application tab.
    - Click **Orders** and check the books you added to the cart.
    - Next, click **Delete** button and then click **OK** button on the popup.
      ![TestOperation](/images/temp/1/84.png?width=90pc)
    - The deleted item are no longer displayed.
      ![TestOperation](/images/temp/1/85.png?width=90pc)
