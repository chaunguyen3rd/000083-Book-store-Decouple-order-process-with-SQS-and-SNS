---
title : "Chuẩn bị"
date :  2025-02-11
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---
Trước khi chúng ta đi vào nội dung chính của bài workshop này, chúng ta cần phải reset ứng dụng web.

1. Tải xuống mã nguồn dưới đây.

    {{%attachments title="Mã nguồn" pattern=".*\.(zip)$"/%}}

2. Giải nén mã nguồn, mở **template.yaml**.
    - Thay đổi giá trị **chaunguyen.site** thành tên miền của bạn.

      ```yaml
      route53HostedZoneName:
        Type: String
        Default: chaunguyen.site
      ```

      ![Chuẩn bị](/images/temp/1/6.png?width=90pc)
3. Chạy các lệnh dưới đây.
{{% notice note %}}
Đảm bảo bạn đã cài đặt AWS CLI và SAM CLI trên máy của bạn, cấu hình thông tin xác thực AWS trước khi chạy các lệnh.
{{% /notice %}}

    ```bash
    sam build
    sam validate
    sam deploy --guided
    ```

4. Nhập nội dung sau. Để mặc định.
    - Stack Name []: `fcj-book-store`
    - AWS Region []: `us-east-1`
    - Confirm changes before deploy [Y/n]: y
    - Allow SAM CLI IAM role creation [Y/n]: y
    - Disable rollback [y/N]: n
    - Save arguments to configuration file [Y/n]: y
      ![Chuẩn bị](/images/temp/1/1.png?width=90pc)

5. Thực hiện theo hướng dẫn này nếu nhà đăng ký tên miền của bạn không phải là AWS.
  {{% notice note %}}
  [Sử dụng Route 53 làm dịch vụ DNS cho một tên miền đang sử dụng](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/migrate-dns-domain-in-use.html)
  {{% /notice %}}
      - Sao chép các bản ghi loại NS từ vùng lưu trữ Route53 của bạn.
        ![Chuẩn bị](/images/temp/1/4.png?width=90pc)
      - Dán các bản ghi NS đó vào nhà đăng ký tên miền DNS của bạn.
        ![Chuẩn bị](/images/temp/1/5.png?width=90pc)

6. Mở **template.yaml** trong mã nguồn bạn đã tải xuống trước đó.
    - Bỏ chú thích khối mã này.

      ```yaml
      # FcjCertificate:
      #   Type: AWS::CertificateManager::Certificate
      #   Properties:
      #     DomainName: !Sub "*.${route53HostedZoneName}"
      #     SubjectAlternativeNames:
      #       - !Ref route53HostedZoneName
      #     ValidationMethod: DNS
      #     DomainValidationOptions:
      #       - DomainName: !Sub "*.${route53HostedZoneName}"
      #         HostedZoneId: !Ref FcjRoute53HostedZone

      # FcjCloudFrontDistribution:
      #   Type: AWS::CloudFront::Distribution
      #   Properties:
      #     DistributionConfig:
      #       Enabled: true
      #       Origins:
      #         - DomainName: !GetAtt FcjBookShop.RegionalDomainName
      #           Id: !Ref FcjBookShop
      #           CustomOriginConfig:
      #             HTTPPort: 80
      #             HTTPSPort: 443
      #             OriginProtocolPolicy: match-viewer
      #             OriginSSLProtocols:
      #               - TLSv1
      #               - TLSv1.1
      #               - TLSv1.2
      #       Aliases:
      #         - !Sub "www.${route53HostedZoneName}"
      #         - !Ref route53HostedZoneName
      #       DefaultCacheBehavior:
      #         CachePolicyId: !Ref cachePolicyId
      #         ViewerProtocolPolicy: redirect-to-https
      #         TargetOriginId: !Ref FcjBookShop
      #         AllowedMethods:
      #           - GET
      #           - HEAD
      #       DefaultRootObject: index.html
      #       ViewerCertificate:
      #         AcmCertificateArn: !Ref FcjCertificate
      #         SslSupportMethod: sni-only

      # FcjRoute53RecordSet:
      #   Type: AWS::Route53::RecordSet
      #   Properties:
      #     HostedZoneId: !Ref FcjRoute53HostedZone
      #     Name: !Sub "www.${route53HostedZoneName}."
      #     Type: A
      #     AliasTarget:
      #       DNSName: !GetAtt FcjCloudFrontDistribution.DomainName
      #       HostedZoneId: !Ref cloudFrontHostedZoneId
      ```

    ![Chuẩn bị](/images/temp/1/89.png?width=90pc)

7. Chạy các lệnh dưới đây.

    ```bash
    sam build
    sam validate
    sam deploy --guided
    ```

    ![Chuẩn bị](/images/temp/1/7.png?width=90pc)

8. Tải mã nguồn **FCJ-Serverless-Workshop** về thiết bị của bạn.
    - Mở terminal trên máy tính của bạn trong thư mục nơi bạn muốn lưu mã nguồn.
    - Sao chép lệnh dưới đây.

      ```bash
      git clone https://github.com/AWS-First-Cloud-Journey/FCJ-Serverless-Workshop.git
      cd FCJ-Serverless-Workshop
      ```

    - Mở **FCJ-Serverless-Workshop** với VSCode và chỉnh sửa.
      - Mở **src/App.js** và chỉnh sửa ``isAdmin: true`` như dưới đây.
        ![Chuẩn bị](/images/temp/1/64.png?width=90pc)

    - Quay lại thư mục gốc của **FCJ-Serverless-Workshop** và chạy các lệnh dưới đây.

      ```bash
      yarn
      yarn build
      ```

9. Chúng ta đã hoàn thành việc xây dựng front-end. Tiếp theo, thực hiện lệnh sau để tải thư mục **build** lên S3.

    ```bash
    aws s3 cp build s3://fcj-book-shop-by-myself --recursive
    ```

10. Nhập các liên kết sau vào một tab mới trong trình duyệt web của bạn: ``http://www.DOMAIN``, thay thế tất cả DOMAIN bằng tên miền của bạn. Tất cả các liên kết đó sẽ chuyển hướng đến đường dẫn mới, thay thế http bằng https.
    ![Chuẩn bị](/images/temp/1/8.png?width=90pc)

Vậy là chúng ta đã xây dựng lại ứng dụng web.
