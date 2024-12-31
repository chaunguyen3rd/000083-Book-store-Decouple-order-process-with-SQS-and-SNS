---
title : "Tạo hàng đợi"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 2.1. </b> "
---
1. Mở bảng điều khiển của [Amazon SQS](https://ap-southeast-1.console.aws.amazon.com/sqs/v2/home?region=ap-southeast-1#/homepage)

![CreateSQS](/images/2-create-sqs-and-sns/2-1-create-sqs-1.png?featherlight=false&width=90pc)

2. Ấn **Create queue**

![CreateSQS](/images/2-create-sqs-and-sns/2-1-create-sqs-2.png?featherlight=false&width=90pc)

3. Chọn **Stardard** cho kiểu của hàng đợi
- Nhập tên cho hàng đợi: `checkout-queue`

![CreateSQS](/images/2-create-sqs-and-sns/2-1-create-sqs-3.png?featherlight=false&width=90pc)

4. Kéo xuống cuối, ấn **Create queue**

![CreateSQS](/images/2-create-sqs-and-sns/2-1-create-sqs-4.png?featherlight=false&width=90pc)

5. Ấn **Send and receive messages**

![CreateSQS](/images/2-create-sqs-and-sns/2-1-create-sqs-5.png?featherlight=false&width=90pc)

6. Nhập nội dung message, ví dụ: `The first order`
- Ấn **Send** để gửi message đến hàng đợi

![CreateSQS](/images/2-create-sqs-and-sns/2-1-create-sqs-6.png?featherlight=false&width=90pc)

7. Ấn **Poll message** để nhận toàn bộ message gửi đến hàng đợi

![CreateSQS](/images/2-create-sqs-and-sns/2-1-create-sqs-7.png?featherlight=false&width=90pc)

8. Ấn vào message đang hiện thị

![CreateSQS](/images/2-create-sqs-and-sns/2-1-create-sqs-8.png?featherlight=false&width=90pc)

9. Nội dung message được hiện thị
- Ấn **Done**

![CreateSQS](/images/2-create-sqs-and-sns/2-1-create-sqs-9.png?featherlight=false&width=90pc)

10. Tích chọn message
- Ấn **Delete** để xoá message

![CreateSQS](/images/2-create-sqs-and-sns/2-1-create-sqs-10.png?featherlight=false&width=90pc)

11. Ấn **Delete**

![CreateSQS](/images/2-create-sqs-and-sns/2-1-create-sqs-11.png?featherlight=false&width=90pc)
