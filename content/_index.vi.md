---
title : "Serverless - Xử lý đơn hàng với SQS-SNS"
date :  "`r Sys.Date()`" 
weight : 1 
chapter : false
---
# Serverless - Xử lý đơn hàng với SQS-SNS

#### Tổng quan

Trong bài số 6 của series này, chúng ta sẽ xử lý các đơn đặt hàng của người dùng sử dụng Amazon SQS và SNS. Khi người dùng đặt hàng, đơn hàng sẽ được đẩy vào hàng đợi và SNS sẽ thông báo cho admin về đơn hàng mới đó. Admin có quyền xem tất cả các đơn đặt hàng và xử lý hoặc xoá chúng.

Kiến trúc của ứng dụng web sẽ như sau:
![ServerlessExample](https://chaunguyen3rd.github.io/000083-Book-store-Decouple-order-process-with-SQS-and-SNS/images/serverless-diagram.png?featherlight=false&width=50pc)

- Khi người dùng đặt hàng (ấn **Checkout**), API POST /books/order đưa thông tin của đơn hàng vào hàng đợi mà bạn tạo, sau đó pulish một tin nhắn qua Amazon SNS topic
- API GET /books/order được gọi khi admin sử dụng tính năng quản lý đơn hàng để lấy toàn bộ đơn hàng chưa xử lý trong hàng đợi và đã xử lý trong bảng của DynamoDB
- Khi Admin xử lý đơn hàng (ấn **Handle**),  API POST /books/order/handle sẽ đẩy đơn hàng vào DynamoDB và xoá nó trong hàng đợi
- Khi Admin xoá đơn hàng (ấn **Delete**), API DELETE /books/order sẽ xoá đơn hàng khỏi hàng đợi

#### Amazon Simple Queue Service
Amazon Simple Queue Service cung cấp một hàng đợi an toàn, bền vững và có sẵn cho phép bạn tích hợp và tách rời các hệ thống và thành phần phần mềm phân tán. Nó có thể mở rộng quy mô độc lập với ứng dụng của chúng ta. Nó cung cấp một API dịch vụ web chung mà bạn có thể truy cập bằng bất kỳ ngôn ngữ lập trình nào mà AWS SDK hỗ trợ. Amazon SQS hỗ trợ cả hàng đợi tiêu chuẩn và FIFO. 

Một hệ thống message phân tán gồm 3 phần chính: các thành phần của hệ thống phân tán, hàng đợi của bạn (được phân phối trên máy chủ Amazon SQS) và các tin nhắn trong hàng đợi. Ví dụ: hệ thống của bạn có một số nhà sản xuất (thành phần gửi thông điệp đến hàng đợi) và người tiêu dùng (thành phần nhận thông báo từ hàng đợi). Hàng đợi (chứa các thông báo) lưu trữ các thông báo trên máy chủ Amazon SQS.

![ServerlessExample](https://chaunguyen3rd.github.io/000083-Book-store-Decouple-order-process-with-SQS-and-SNS/images/sqs-basic.png?featherlight=false&width=55pc)


#### Amazon Simple Notification Service
Amazon Simple Notification Service (SNS) một dịch vụ được quản lý để cung cấp dịch vụ gửi tin nhắn từ publishers đến subscribers (còn được gọi là producers và consumers). Publishers giao tiếp không đồng bộ với subscribers bằng cách gửi tin nhắn đến một topic, đây là điểm truy cập và kênh giao tiếp logic. Khách hàng có thể đăng ký SNS topic và nhận các tin nhắn đã xuất bản bằng cách sử dụng loại điểm cuối được hỗ trợ, chẳng hạn như Amazon Kinesis Data Firehose, Amazon SQS, AWS Lambda, HTTP, email, thông báo đẩy trên thiết bị di động và tin nhắn văn bản di động (SMS).

![ServerlessExample](https://chaunguyen3rd.github.io/000083-Book-store-Decouple-order-process-with-SQS-and-SNS/images/sns-basic.png?featherlight=false&width=50pc)

#### Nội dung

 1. [Chuẩn bị](1-preparation/)
 2. [Tạo hàng đợi và SNS topic](2-create-sqs-and-sns/)
 3. [Tạo API và Lambda function](3-create-api-lambda-function/)
 4. [Kiểm tra hoạt động](4-test-operation/)
 5. [Dọn dẹp tài nguyên](5-cleanup)
