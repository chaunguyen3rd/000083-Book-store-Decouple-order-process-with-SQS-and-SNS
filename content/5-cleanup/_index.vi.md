---
title : "Dọn dẹp"
date :  2025-02-11
weight : 5
chapter : false
pre : " <b> 5. </b> "
---

{{% notice note %}}
Việc dọn dẹp sẽ mất một chút thời gian để hoàn thành
{{% /notice %}}

1. Dọn dẹp Route 53
    - Mở [AWS Route 53 console](https://us-east-1.console.aws.amazon.com/route53/v2/home?region=us-east-1).
    - Nhấp vào **Hosted zones** trên menu bên trái.
    - Chọn **Hosted zones** của bạn và dọn dẹp tất cả các bản ghi nếu có thể.

2. Làm trống S3 bucket.
    - Mở [AWS S3 console](https://s3.console.aws.amazon.com/s3/buckets?region=us-east-1).
    - Chọn **fcj-book-shop-by-myself**.
    - Nhấp vào **Empty**.
    - Nhập **permanently delete**.
    - Nhấp vào **Empty**.
    - Làm tương tự cho các bucket bắt đầu với **aws-sam-cli-managed-default-** và **book-image-resize-shop-by-myself**.

3. Xóa CloudFormation stacks.
    - Thực hiện lệnh dưới đây để xóa ứng dụng AWS SAM.

      ```bash
      sam delete --stack-name fcj-book-store
      sam delete --stack-name aws-sam-cli-managed-default
      ```

    - Nếu bạn gặp vấn đề khi xóa bằng lệnh. Mở [AWS Cloudformation console](https://us-east-1.console.aws.amazon.com/cloudformation/home?region=us-east-1#/getting-started). Sau đó, xóa tất cả các stack liên quan đến workshop này.

4. Dọn dẹp SQS.
    - Mở [Amazon SQS console](https://us-east-1.console.aws.amazon.com/sqs/v2/home?region=us-east-1#/homepage).
    - Chọn **checkout-queue**.
    - Nhấp vào **Delete**.
    - Nhập `confirm`.
    - Nhấp vào **Delete**.

5. Dọn dẹp SNS.
    - Mở [Amazon SNS console](https://us-east-1.console.aws.amazon.com/sns/v3/home?region=us-east-1#/dashboard).
    - Chọn **order-notice** tại **Topics**.
    - Nhấp vào **Delete**.
    - Nhập `delete me`.
    - Nhấp vào **Delete**.
