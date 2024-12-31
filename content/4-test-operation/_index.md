---
title : "Test web operation"
date :  "`r Sys.Date()`" 
weight : 4
chapter : false
pre : " <b> 4. </b> "
---
You can download the image files here to add data to check the operation of the services

{{%attachments title="Images" pattern=".*\.(jpg|png)$"/%}}

1. Open **config.js** file in source code folder of front-end
- Uncomment the line 4, then change value with email that you registered account as well as registered to receive notify

![UpdateSource](/images/4-test-operation/4-test-operation-1.png?featherlight=false&width=90pc)

2. Open **Login.js** file in **source/component/Authen/** folder
- Uncomment the lines 39 and 41

![UpdateSource](/images/4-test-operation/4-test-operation-2.png?featherlight=false&width=90pc)

3. Run the below command to build and upload to S3 bucket
```
yarn build
aws s3 cp build s3://fcj-book-store --recursive
```

4. Navigate [Amazon S3 console](https://s3.console.aws.amazon.com/s3/buckets?region=ap-southeast-1)
- Click to **fcj-book-store** bucket

![AccessWeb](/images/4-test-operation/4-test-operation-3.png?featherlight=false&width=90pc)

5. Click **Properties** tab

![AccessWeb](/images/4-test-operation/4-test-operation-4.png?featherlight=false&width=90pc)

- Scroll down to bottom, click to website endpoint

![AccessWeb](/images/4-test-operation/4-test-operation-5.png?featherlight=false&width=90pc)

6. Click **Login**

![Login](/images/4-test-operation/4-test-operation-6.png?featherlight=false&width=90pc)

6. Nhập thông tin tài khoản bạn đã đăng ký: email và mật khẩu
- Click **Submit**

![Login](/images/4-test-operation/4-test-operation-7.png?featherlight=false&width=90pc)

7. Click **Create new book**

![AddData](/images/4-test-operation/4-test-operation-8.png?featherlight=false&width=90pc)

8. Enter the book information
- Enter ID: `1`
- Enter book name: `Java`
- Enter author: `Jame Patternson`
- Enter catagory `IT`
- Enter price: `10.98`
- Enter desciption: `A beginner's guide to learning the basic of Java`
- Click **Choose File** và chọn tệp ảnh **Java.jpg** mà bạn vừa tải về
- Click **Create**

![AddData](/images/4-test-operation/4-test-operation-9.png?featherlight=false&width=90pc)

9. Ấn **OK**

![AddData](/images/4-test-operation/4-test-operation-29.png?featherlight=false&width=90pc)

10. Ấn **Create new book**
- Enter ID: `2`
- Enter book name: `Let's Go`
- Enter author: `Alex Edwards`
- Enter catagory: `IT`
- Enter price: `15.8`
- Enter desciption: `A step-by-step guide to creating fast, secure web with Go`
- Click **Choose File** và chọn tệp ảnh **LetGoBook.png** mà bạn vừa tải về
- Click **Create**

![AddData](/images/4-test-operation/4-test-operation-10.png?featherlight=false&width=90pc)

11. Click **OK**

![AddData](/images/4-test-operation/4-test-operation-29.png?featherlight=false&width=90pc)

12. Click **Add to cart** of each product

![CheckOut](/images/4-test-operation/4-test-operation-11.png?featherlight=false&width=90pc)

13. Click on the cart icon in the upper right corner

![CreateRecord](/images/4-test-operation/4-test-operation-12.png?featherlight=false&width=90pc)

14. Click **Checkout**

![CreateRecord](/images/4-test-operation/4-test-operation-13.png?featherlight=false&width=90pc)

- Click **OK**

![CreateRecord](/images/4-test-operation/4-test-operation-14.png?featherlight=false&width=90pc)

15. Back to Amazon SQS console
- Click **Send and receive messages**

![CreateRecord](/images/4-test-operation/4-test-operation-16.png?featherlight=false&width=90pc)

16. Click **Poll messages**

![CreateRecord](/images/4-test-operation/4-test-operation-17.png?featherlight=false&width=90pc)

17. Click to the showing message

![CreateRecord](/images/4-test-operation/4-test-operation-18.png?featherlight=false&width=90pc)

18. View the content of the order, Click **Done**

![CreateRecord](/images/4-test-operation/4-test-operation-19.png?featherlight=false&width=90pc)

19. Open email that you have subscribed to receive notification

![CreateRecord](/images/4-test-operation/4-test-operation-20.png?featherlight=false&width=90pc)

20. Back to the application tab
- Click **Orders**, orders are displayed

![CreateRecord](/images/4-test-operation/4-test-operation-21.png?featherlight=false&width=90pc)

21. Repeat steps 12 to 14 to add as some orders as you like
22. Click **Orders**
- Click **Handle**

![CreateRecord](/images/4-test-operation/4-test-operation-22.png?featherlight=false&width=90pc)

23. Click **OK**

![CreateRecord](/images/4-test-operation/4-test-operation-23.png?featherlight=false&width=90pc)

24. The order has been processed, the status is changed to **Processed** and delete and processing buttons aren't displayed
- Click **Delete**

25. Open [Amazon DynamoDB console](https://ap-southeast-1.console.aws.amazon.com/dynamodbv2/home?region=ap-southeast-1#dashboard), Click **Explore items** on the left menu
- Select **Orders**, processed order data has been entered into the table

![OrdersTableData](/images/4-test-operation/4-test-operation-30.png?featherlight=false&width=90pc)

24. Back to application tab
- Click **Delete**

![CreateRecord](/images/4-test-operation/4-test-operation-24.png?featherlight=false&width=90pc)

- Click **OK**

![CreateRecord](/images/4-test-operation/4-test-operation-25.png?featherlight=false&width=90pc)

- Deleted orders are no longer displayed

![CreateRecord](/images/4-test-operation/4-test-operation-26.png?featherlight=false&width=90pc)

We have completed the workshop, already know how to work with Amazon SQS and Amazon SNS. The next workshop we will use CodePipeline to deploy the application.