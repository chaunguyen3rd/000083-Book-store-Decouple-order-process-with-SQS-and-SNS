---
title : "Tạo bảng OrdersTable DynamoDB"
date :  2025-02-11
weight : 1
chapter : false
pre : " <b> 3.1 </b> "
---
Trong bước này, chúng ta sẽ tạo một bảng DynamoDB mới bằng cách sử dụng mẫu SAM.

#### Tạo bảng FcjOrdersTable DynamoDB

1. Mở **template.yaml** trong mã nguồn bạn đã tải xuống trước đó.
    - Thêm các đoạn mã sau đây để tạo bảng **FcjOrdersTable**.

      ```yaml
      orderTable:
        Type: String
        Default: OrdersTable
      ```

      ![CreateOrderTable](/images/temp/1/27.png?width=90pc)

      ```yaml
      FcjOrdersTable:
        Type: AWS::DynamoDB::Table
        Properties:
          TableName: !Ref orderTable
          BillingMode: PAY_PER_REQUEST
          AttributeDefinitions:
            - AttributeName: id
              AttributeType: S
            - AttributeName: book_id
              AttributeType: S
          KeySchema:
            - AttributeName: id
              KeyType: HASH
            - AttributeName: book_id
              KeyType: RANGE
      ```

      ![CreateOrderTable](/images/temp/1/28.png?width=90pc)

2. Chạy các lệnh dưới đây.

    ```bash
    sam build
    sam validate
    sam deploy
    ```

    ![CreateOrderTable](/images/temp/1/29.png?width=90pc)

3. Mở [AWS DynamoDB](https://us-east-1.console.aws.amazon.com/dynamodbv2/home?region=us-east-1#tables) console để kiểm tra.
    ![CreateOrderTable](/images/temp/1/30.png?width=90pc)
