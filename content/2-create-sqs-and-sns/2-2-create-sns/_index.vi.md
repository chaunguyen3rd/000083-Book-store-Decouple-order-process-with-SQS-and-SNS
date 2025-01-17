---
title : "Tạo chủ đề SNS"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 2.2. </b> "
---
1. Mở [Amazon SNS console](https://us-east-1.console.aws.amazon.com/sns/v3/home?region=us-east-1#/dashboard)
    - Nhấp vào **Topics** trên menu bên trái.
    - Nhấp vào nút **Create topic**.
      ![CreateSNS](/images/temp/1/18.png?width=90pc)

2. Tại trang **Create topic**.
    - Nhấp vào **Standard** tại **Type**.
    - Nhập ``order-notice`` tại **Name**.
      ![CreateSNS](/images/temp/1/19.png?width=90pc)
    - Để mặc định, cuộn xuống và nhấp vào nút **Create topic**.
      ![CreateSNS](/images/temp/1/20.png?width=90pc)

3. Tại trang **order-notice**.
    - Nhấp vào tab **Subscriptions**.
    - Nhấp vào nút **Create subscription**.
      ![CreateSNS](/images/temp/1/21.png?width=90pc)

4. Tại trang **Create subscription**.
    - Chọn **Email** tại **Protocol**.
    - Nhập email của bạn tại **Endpoint**.
    - Nhấp vào **Create subscription**.
      ![CreateSNS](/images/temp/1/22.png?width=90pc)

5. Quay lại trang **order-notice**.
    - Nhấp vào tab **Subscriptions**.
    - Kiểm tra đăng ký vừa được tạo với trạng thái **Pending confirmation**.
      ![CreateSNS](/images/temp/1/23.png?width=90pc)

6. Mở hộp thư email của bạn, tìm email gửi từ **<no-reply@sns.amazonaws.com>**.
    - Nhấp vào liên kết **Confirm subscription**.
      ![CreateSNS](/images/temp/1/24.png?width=90pc)
    - Nó sẽ chuyển hướng bạn đến một tab mới.
      ![CreateSNS](/images/temp/1/25.png?width=90pc)

7. Quay lại trang **order-notice**, kiểm tra trạng thái **Confirmed** của đăng ký bạn vừa tạo.
    ![CreateSNS](/images/temp/1/26.png?width=90pc)
