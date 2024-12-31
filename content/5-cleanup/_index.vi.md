---
title : "Dọn dẹp tài nguyên"
date :  "`r Sys.Date()`" 
weight : 5
chapter : false
pre : " <b> 5. </b> "
---
1. Làm rỗng S3 bucket
- Mở bảng điều khiển của [AWS S3](https://s3.console.aws.amazon.com/s3/buckets?region=ap-southeast-1)
- Chọn **fcj-book-store**
- Ấn **Empty**
- Nhập **permanently delete**
- Ấn **Empty**
- Làm tương tự với bucket bắt đầu bằng **aws-sam-cli-managed-default-** và bucket **book-image-resize-store**
2. Xoá stack của CloudFormation
- Chạy câu lệnh dưới đây để xoá ứng dụng AWS SAM
```
sam delete --stack-name fcj-book-store
sam delete --stack-name aws-sam-cli-managed-default
```
3. Xoá hàng đợi
- Mở bảng điều khiển của [Amazon SQS](https://ap-southeast-1.console.aws.amazon.com/sqs/v2/home?region=ap-southeast-1#/homepage)
- Chọn hàng đợi đã tạo
- Ấn **Delete**
- Nhập **delete**
- Ấn **Delete**
4. Xoá SNS topic
- Mở bảng điều khiển của [Amazon SNS](https://ap-southeast-1.console.aws.amazon.com/sns/v3/home?region=ap-southeast-1#/topics)
- Chọn topic đã tạo
- Ấn **Delete**
- Nhập **delete me**
- Ấn **Delete**
- Ấn vào tab **Subcriptions**
- Chọn subcription đã tạo
- Ấn **Delete**
- Ấn **Delete** lần nữa