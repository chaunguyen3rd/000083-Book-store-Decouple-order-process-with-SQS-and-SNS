---
title : "Tạo hàng đợi"
date :  2025-02-11
weight : 1
chapter : false
pre : " <b> 2.1. </b> "
---
1. Mở [Amazon SQS console](https://us-east-1.console.aws.amazon.com/sqs/v2/home?region=us-east-1#/homepage).
    - Nhấp vào **Queues** trên menu bên trái.
    - Nhấp vào **Create queue**.
      ![CreateSQS](/images/temp/1/9.png?width=90pc)

2. Tại trang **Create queue**.
    - Chọn loại **Standard**.
    - Nhập tên ``checkout-queue``.
      ![CreateSQS](/images/temp/1/10.png?width=90pc)
    - Để mặc định, cuộn xuống và nhấp vào nút **Create queue**.
      ![CreateSQS](/images/temp/1/11.png?width=90pc)

3. Tại trang **checkout-queue**.
    - Nhấp vào nút **Send and receive messages**.
      ![CreateSQS](/images/temp/1/12.png?width=90pc)

4. Tại trang **Send and receive messages**.
    - Nhập ``The first message`` tại **Message body**.
    - Nhấp vào **Send message** để gửi tin nhắn đến hàng đợi.
      ![CreateSQS](/images/temp/1/13.png?width=90pc)
    - Nhấp vào **Poll for messages** để nhận tất cả tin nhắn gửi đến hàng đợi.
    - Nhấp vào tin nhắn vừa được hiển thị.
      ![CreateSQS](/images/temp/1/14.png?width=90pc)
    - Tại cửa sổ **Message: ...**, kiểm tra tin nhắn **Body** và nhấp vào nút **Done**.
      ![CreateSQS](/images/temp/1/15.png?width=90pc)
    - Nhấp vào hộp kiểm của tin nhắn vừa được hiển thị.
    - Nhấp vào nút **Delete**.
      ![CreateSQS](/images/temp/1/16.png?width=90pc)
    - Tại cửa sổ **Delete Messages**, nhấp vào nút **Delete**.
      ![CreateSQS](/images/temp/1/17.png?width=90pc)
