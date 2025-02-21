---
title : "Tạo hàm order_management Lambda"
date :  2025-02-11
weight : 3
chapter : false
pre : " <b> 3.3 </b> "
---
Trong bước này, chúng ta sẽ tạo một hàm order_management Lambda mới bằng cách sử dụng mẫu SAM.

#### Chuẩn bị

1. Mở **template.yaml** trong mã nguồn bạn đã tải xuống trước đó.
    - Bình luận khối mã này.

      ```yaml
      # BookApiDeployment:
      #   Type: AWS::ApiGateway::Deployment
      #   Properties:
      #     RestApiId: !Ref BookApi
      #   DependsOn:
      #     - BookApiGet
      #     - BookApiCreate
      #     - BookApiDelete
      #     - LoginApi
      #     - RegisterApi
      #     - ConfirmApi

      BookApiStage:
        Type: AWS::ApiGateway::Stage
        Properties:
          RestApiId: !Ref BookApi
          StageName: !Ref stage
      #     DeploymentId: !Ref BookApiDeployment
      ```

      ![CreateOrderManagementFunction](/images/temp/1/33.png?width=90pc)

2. Chạy các lệnh dưới đây.

    ```bash
    sam build
    sam validate
    sam deploy
    ```

    ![CreateOrderManagementFunction](/images/temp/1/35.png?width=90pc)

#### Tạo hàm FcjOrderManagement

1. Mở **template.yaml** trong mã nguồn bạn đã tải xuống trước đó.
    - Thêm các đoạn mã sau để tạo hàm **FcjOrderManagement**.

      ```yaml
      FcjOrderManagementFunction:
        Type: AWS::Serverless::Function
        Properties:
          CodeUri: fcj-book-shop/order_management
          Handler: order_management.lambda_handler
          Runtime: python3.11
          FunctionName: order_management
          Environment:
            Variables:
              SQS_QUEUE_URL: !Ref checkoutQueueUrl
              ORDER_TABLE_NAME: !Ref orderTable
          Architectures:
            - x86_64
          Policies:
            - Statement:
                - Sid: VisualEditor0
                  Effect: Allow
                  Action:
                    - dynamodb:Scan
                    - dynamodb:Query
                  Resource:
                    - !Sub "arn:aws:dynamodb:${AWS::Region}:${AWS::AccountId}:table/${orderTable}"
                - Sid: VisualEditor1
                  Effect: Allow
                  Action:
                    - sqs:*
                  Resource:
                    - !Sub "arn:aws:sqs:${AWS::Region}:${AWS::AccountId}:${checkoutQueueName}"

      FcjOrderManagementApi:
        Type: AWS::ApiGateway::Method
        Properties:
          HttpMethod: GET
          RestApiId: !Ref BookApi
          ResourceId: !Ref FcjCheckoutOrderResource
          AuthorizationType: NONE
          Integration:
            Type: AWS_PROXY
            IntegrationHttpMethod: POST # For Lambda integrations, you must set the integration method to POST
            Uri: !Sub >-
              arn:aws:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${FcjOrderManagementFunction.Arn}/invocations
          MethodResponses:
            - StatusCode: "200"
              ResponseParameters:
                method.response.header.Access-Control-Allow-Origin: true
                method.response.header.Access-Control-Allow-Methods: true
                method.response.header.Access-Control-Allow-Headers: true

      FcjOrderManagementApiInvokePermission:
        Type: AWS::Lambda::Permission
        Properties:
          FunctionName: !Ref FcjOrderManagementFunction
          Action: lambda:InvokeFunction
          Principal: apigateway.amazonaws.com
          SourceAccount: !Ref "AWS::AccountId"
      ```

      ![CreateOrderManagementFunction](/images/temp/1/42.png?width=90pc)

2. Cấu trúc thư mục như sau.

    ```bash
    fcj-book-shop-sam-ws3
    ├── fcj-book-shop
    │   ├── checkout_order
    │   │   └── checkout_order.py
    │   ├── order_management
    │   │   └── order_management.py
    │   ├── ...
    │
    └── template.yaml
    ```

    - Tạo thư mục **order_management** trong thư mục **fcj-book-shop-sam-ws6/fcj-book-shop/**.
    - Tạo tệp **order_management.py** và sao chép mã sau vào đó.

      ```py
      import os
      import json
      import boto3

      headers = {
          "Content-Type": "application/json",
          "Access-Control-Allow-Origin": "*",
          "Access-Control-Allow-Methods": "OPTIONS,POST,GET,DELETE",
          "Access-Control-Allow-Headers": "Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token"
      }

      dynamodb = boto3.resource('dynamodb')
      sqs = boto3.client('sqs')

      # Get value of the environment variables
      table_name = os.getenv('ORDER_TABLE_NAME')
      queue_url = os.getenv('SQS_QUEUE_URL')


      def get_messages_from_sqs(messages):
          # Get the total number of messages in the queue
          response = sqs.get_queue_attributes(
              QueueUrl=queue_url,
              AttributeNames=['ApproximateNumberOfMessages']
          )
          total_messages = int(response['Attributes']['ApproximateNumberOfMessages'])

          while len(messages) < total_messages:
              # Receive messages from SQS queue
              received_message_res = sqs.receive_message(
                  QueueUrl=queue_url,
                  MaxNumberOfMessages=10,
                  WaitTimeSeconds=20
              )

              if 'Messages' in received_message_res:
                  for message in received_message_res['Messages']:
                      messages.append({
                          "receiptHandle": message["ReceiptHandle"],
                          "books": json.loads(message["Body"])['books'],
                          "price": json.loads(message["Body"])['price'],
                          "status": "Unprocessed"
                      })


      def get_messages_from_dynamodb(messages):
          table = dynamodb.Table(table_name)

          try:
              res = table.scan()
              orders = res.get('Items', [])
              aggregated_orders = {}

              for order in orders:
                  order_id = order['id']

                  book = {
                      'id': order['book_id'],
                      'name': order['name'],
                      'qty': order['qty'],
                  }

                  if order_id not in aggregated_orders:
                      aggregated_orders[order_id] = {
                          'id': order_id,
                          'books': [book],
                          'price': order['price']
                      }

                  else:
                      aggregated_orders[order_id]['books'].append(book)

              for order in aggregated_orders.values():
                  messages.append({
                      "receiptHandle": "",
                      "books": order['books'],
                      "price": order['price'],
                      "status": "Processed"
                  })

          except Exception as e:
              print(f"Error reading from DynamoDB: {e}")
              raise Exception(f"Error reading from DynamoDB: {e}")


      def lambda_handler(event, context):
          messages = []

          # Get messages from sqs
          get_messages_from_sqs(messages)

          # Get Messages from DynamoDB
          get_messages_from_dynamodb(messages)

          return {
              'statusCode': 200,
              'body': json.dumps(messages),
              'headers': headers
          }
      ```

      ![CreateOrderManagementFunction](/images/temp/1/43.png?width=90pc)

3. Bỏ bình luận khối mã này.

    ```yaml
    BookApiDeployment:
      Type: AWS::ApiGateway::Deployment
      Properties:
        RestApiId: !Ref BookApi
      DependsOn:
        - BookApiGet
        - BookApiCreate
        - BookApiDelete
        - LoginApi
        - RegisterApi
        - ConfirmApi
        - FcjCheckoutOrderApi
        - FcjOrderManagementApi

    BookApiStage:
      Type: AWS::ApiGateway::Stage
      Properties:
        RestApiId: !Ref BookApi
        StageName: !Ref stage
        DeploymentId: !Ref BookApiDeployment
    ```

    ![CreateOrderManagementFunction](/images/temp/1/44.png?width=90pc)

4. Chạy các lệnh dưới đây.

    ```bash
    sam build
    sam validate
    sam deploy
    ```

    ![CreateOrderManagementFunction](/images/temp/1/45.png?width=90pc)

#### Kiểm tra việc tạo

1. Mở [Amazon API Gateway console](https://us-east-1.console.aws.amazon.com/apigateway/main/apis?region=us-east-1).
    - Nhấp vào **fcj-serverless-api**.
      ![CreateOrderManagementFunction](/images/temp/1/38.png?width=90pc)
    - Nhấp vào **Resources** trên menu bên trái.
    - Kiểm tra **/order** vừa được tạo.
      ![CreateOrderManagementFunction](/images/temp/1/46.png?width=90pc)

2. Mở [Amazon Lambda console](https://us-east-1.console.aws.amazon.com/lambda/home?region=us-east-1#/functions).
    - Nhấp vào **Functions** trên menu bên trái.
    - Chọn hàm **order_management**.
      ![CreateOrderManagementFunction](/images/temp/1/47.png?width=90pc)
    - Tại trang **order_management**, kiểm tra hàm vừa được tạo.
      ![CreateOrderManagementFunction](/images/temp/1/48.png?width=90pc)
