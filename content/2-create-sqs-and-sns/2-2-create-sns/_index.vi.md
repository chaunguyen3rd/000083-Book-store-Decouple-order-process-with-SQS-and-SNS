---
title : "Tạo SNS topic"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 2.2. </b> "
---
1. Mở bảng điều khiển của [Amazon SNS](https://ap-southeast-1.console.aws.amazon.com/sns/v3/home?region=ap-southeast-1#/dashboard)

![CreateSNS](/images/2-create-sqs-and-sns/2-2-create-sns-1.png?featherlight=false&width=90pc)

2. Ấn **Topics** ở menu phía bên trái
- Ấn **Create topic**

![CreateSNS](/images/2-create-sqs-and-sns/2-2-create-sns-2.png?featherlight=false&width=90pc)

3. Chọn **Stardard** cho kiểu của topic
- Nhập tên topic: `order-notic`

![CreateSNS](/images/2-create-sqs-and-sns/2-2-create-sns-3.png?featherlight=false&width=90pc)

4. Kéo xuống cuối trang, ấn **Create topic**

![CreateSNS](/images/2-create-sqs-and-sns/2-2-create-sns-4.png?featherlight=false&width=90pc)

5. Ấn vào topic mà bạn vừa tạo

![CreateSNS](/images/2-create-sqs-and-sns/2-2-create-sns-5.png?featherlight=false&width=90pc)

6. Ấn **Create subscription**

![CreateSNS](/images/2-create-sqs-and-sns/2-2-create-sns-6.png?featherlight=false&width=90pc)

7. Chọn **Protocol** là **Email**

![CreateSNS](/images/2-create-sqs-and-sns/2-2-create-sns-7.png?featherlight=false&width=90pc)

8. Nhập email mà bạn đã đăng ký tài khoản trên Cognito và là admin
- Ấn **Create subcription**

![CreateSNS](/images/2-create-sqs-and-sns/2-2-create-sns-8.png?featherlight=false&width=90pc)

9. Sau đó, subcription sẽ ở trạng thái đang xử lý

![CreateSNS](/images/2-create-sqs-and-sns/2-2-create-sns-9.png?featherlight=false&width=90pc)

10. Để xác nhận email của bạn, mở email đã đăng ký
- Tìm mail được gửi đến từ địa **no-reply@sns.amazonaws.com**, ấn vào link để xác nhận

![CreateSNS](/images/2-create-sqs-and-sns/2-2-create-sns-10.png?featherlight=false&width=90pc)

11. Quay lại với sns topic, subcription đã được xác nhận

![CreateSNS](/images/2-create-sqs-and-sns/2-2-create-sns-11.png?featherlight=false&width=90pc)
