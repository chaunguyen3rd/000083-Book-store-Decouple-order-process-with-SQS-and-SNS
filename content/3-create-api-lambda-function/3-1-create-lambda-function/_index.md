---
title : "Create Lambda function"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 3.1 </b> "
---
In this step, we will create a new DynamoDB table to store processed order data and four Lambda functions to save orders, manage orders, delete orders, and process orders using a SAM template.

#### Preparation

1. Open **template.yaml** in the source code you downloaded before.
    - Comment this code block.

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

      # BookApiStage:
      #   Type: AWS::ApiGateway::Stage
      #   Properties:
      #     RestApiId: !Ref BookApi
      #     StageName: !Ref stage
      #     DeploymentId: !Ref BookApiDeployment
      ```

      ![CreateOrderTable](/images/temp/1/33.png?width=90pc)

2. Run the below commands.

    ```bash
    sam build
    sam validate
    sam deploy --guided
    ```

    ![CreateOrderTable](/images/temp/1/35.png?width=90pc)

#### Create FcjOrdersTable DynamoDB table

1. Open **template.yaml** in the source code you downloaded before.
    - Add the following scripts below to create **FcjOrdersTable** table.

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
          KeySchema:
            - AttributeName: id
              KeyType: HASH
      ```

      ![CreateOrderTable](/images/temp/1/28.png?width=90pc)

2. Run the below commands.

    ```bash
    sam build
    sam validate
    sam deploy --guided
    ```

    ![CreateOrderTable](/images/temp/1/29.png?width=90pc)

3. Open [AWS DynamoDB](https://us-east-1.console.aws.amazon.com/dynamodbv2/home?region=us-east-1#tables) console to check.
    ![CreateOrderTable](/images/temp/1/30.png?width=90pc)

#### Create FcjCheckOutOrder function

1. Open **template.yaml** in the source code you downloaded before.
    - Add the following scripts below to create **FcjCheckOutOrder** function.
      - Change **checkoutQueueUrl** and **orderTopicArn** value to your value.

        ```yaml
        checkoutQueueName:
          Type: String
          Default: checkout-queue

        checkoutQueueUrl:
          Type: String
          Default: https://sqs.us-east-1.amazonaws.com/017820706022/checkout-queue

        orderTopicName:
          Type: String
          Default: order-notice

        orderTopicArn:
          Type: String
          Default: arn:aws:sns:us-east-1:017820706022:order-notice

        checkoutPathPart:
          Type: String
          Default: order
        ```

        ![CreateOrderTable](/images/temp/1/31.png?width=90pc)

        ```yaml
        FcjCheckOutOrderFunction:
          Type: AWS::Serverless::Function
          Properties:
            CodeUri: fcj-book-shop/checkout_order
            Handler: checkout_order.lambda_handler
            Runtime: python3.11
            FunctionName: checkout_order
            Environment:
              Variables:
                SQS_QUEUE_URL: !Ref checkoutQueueUrl
                SNS_TOPIC_ARN: !Ref orderTopicArn
            Architectures:
              - x86_64
            Policies:
              - Statement:
                  - Sid: VisualEditor0
                    Effect: Allow
                    Action:
                      - sqs:*
                    Resource:
                      - !Sub "arn:aws:sqs:${AWS::Region}:${AWS::AccountId}:${checkoutQueue}"
                  - Sid: VisualEditor1
                    Effect: Allow
                    Action:
                      - sns:Publish
                    Resource:
                      - !Sub "arn:aws:sns:${AWS::Region}:${AWS::AccountId}:${orderTopic}"

        FcjCheckoutOrderResource:
          Type: AWS::ApiGateway::Resource
          Properties:
            RestApiId: !Ref BookApi
            ParentId: !GetAtt BookApi.RootResourceId
            PathPart: !Ref checkoutPathPart

        FcjCheckoutOrderApiOptions:
          Type: AWS::ApiGateway::Method
          Properties:
            HttpMethod: OPTIONS
            RestApiId: !Ref BookApi
            ResourceId: !Ref FcjCheckoutOrderResource
            AuthorizationType: NONE
            Integration:
              Type: MOCK
              IntegrationResponses:
                - StatusCode: "200"
                  ResponseParameters:
                    method.response.header.Access-Control-Allow-Origin: "'*'"
                    method.response.header.Access-Control-Allow-Methods: "'OPTIONS,POST,GET,DELETE'"
                    method.response.header.Access-Control-Allow-Headers: "'Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token'"
            MethodResponses:
              - StatusCode: "200"
                ResponseParameters:
                  method.response.header.Access-Control-Allow-Origin: true
                  method.response.header.Access-Control-Allow-Methods: true
                  method.response.header.Access-Control-Allow-Headers: true

        FcjCheckoutOrderApi:
          Type: AWS::ApiGateway::Method
          Properties:
            HttpMethod: POST
            RestApiId: !Ref BookApi
            ResourceId: !Ref FcjCheckoutOrderResource
            AuthorizationType: NONE
            Integration:
              Type: AWS_PROXY
              IntegrationHttpMethod: POST # For Lambda integrations, you must set the integration method to POST
              Uri: !Sub >-
                arn:aws:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${FcjCheckOutOrderFunction.Arn}/invocations
            MethodResponses:
              - StatusCode: "200"
                ResponseParameters:
                  method.response.header.Access-Control-Allow-Origin: true
                  method.response.header.Access-Control-Allow-Methods: true
                  method.response.header.Access-Control-Allow-Headers: true

        FcjCheckoutOrderApiInvokePermission:
          Type: AWS::Lambda::Permission
          Properties:
            FunctionName: !Ref FcjCheckOutOrderFunction
            Action: lambda:InvokeFunction
            Principal: apigateway.amazonaws.com
            SourceAccount: !Ref "AWS::AccountId"
        ```

        ![CreateOrderTable](/images/temp/1/32.png?width=90pc)

2. The directory structure is as follows.

    ```bash
    fcj-book-shop-sam-ws3
    ├── fcj-book-shop
    │   ├── checkout_order
    │   │   └── checkout_order.py
    │   ├── ...
    │
    └── template.yaml
    ```

    - Create **checkout_order** folder in **fcj-book-shop-sam-ws6/fcj-book-shop/** folder.
    - Create **checkout_order.py** file and copy the following code to it.

      ```py
      import json
      import boto3
      import os


      def handle_checkout(event, context):
          sqs = boto3.client('sqs')
          sns = boto3.client('sns')

          sqs_queue_url = os.environ['SQS_QUEUE_URL']
          sns_topic_arn = os.environ['SNS_TOPIC_ARN']
          sns_topic_subject = "New order received. Please process."

          try:
              body = json.loads(event['body'])

              print(f"body: {body}")

              # Send to SQS
              sqs_response = sqs.send_message(
                  QueueUrl=sqs_queue_url,
                  MessageBody=json.dumps(body)
              )

              # Send to SNS
              sns_response = sns.publish(
                  TopicArn=sns_topic_arn,
                  Message=f"New order received: {json.dumps(body)}",
                  Subject=sns_topic_subject
              )

              return {
                  'statusCode': 200,
                  'body': json.dumps({
                      'message': 'Order processed successfully',
                      'sqs_message_id': sqs_response['MessageId'],
                      'sns_message_id': sns_response['MessageId']
                  })
              }

          except Exception as e:
              print(f"Error processing order: {e}")
              raise Exception(f"Error processing order: {e}")
      ```

      ![CreateOrderTable](/images/temp/1/37.png?width=90pc)

3. Uncomment this code block.

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

    BookApiStage:
      Type: AWS::ApiGateway::Stage
      Properties:
        RestApiId: !Ref BookApi
        StageName: !Ref stage
        DeploymentId: !Ref BookApiDeployment
    ```

    ![CreateOrderTable](/images/temp/1/36.png?width=90pc)

4. Run the below commands.

    ```bash
    sam build
    sam validate
    sam deploy --guided
    ```

    ![CreateOrderTable](/images/temp/1/34.png?width=90pc)

3. Thêm đoạn script dưới đây để tạo function **CheckOutOrder**

```
  CheckOutOrder:
    Type: AWS::Serverless::Function
    Properties:
      FunctionName: checkout_order
      CodeUri: fcj-book-store/checkout_order
      Handler: checkout_order.lambda_handler
      Runtime: python3.9
      Architectures:
        - x86_64
      Policies:
        - Statement:
            - Sid: VisualEditor0
              Effect: Allow
              Action:
                - sqs:*
              Resource:
                - !Sub arn:aws:sqs:${AWS::Region}:${AWS::AccountId}:checkout-queue
            - Sid: VisualEditor1
              Effect: Allow
              Action:
                - sns:Publish
              Resource:
                - !Sub arn:aws:sns:${AWS::Region}:${AWS::AccountId}:order-notice
      Environment:
        Variables:
          QUEUE_NAME: "checkout-queue"
```

![CreateFunctions](/images/3-create-api-lambda-function/3-create-lambda-function-4.png?featherlight=false&width=90pc)

- Thêm đoạn script dưới đây để tạo function **OrderManagement**

```
  OrderManagement:
    Type: AWS::Serverless::Function
    Properties:
      FunctionName: order_management
      CodeUri: fcj-book-store/order_management
      Handler: order_management.lambda_handler
      Runtime: python3.9
      Architectures:
        - x86_64
      Policies:
        - Statement:
            - Sid: VisualEditor0
              Effect: Allow
              Action:
                - sqs:*
              Resource:
                - !Sub arn:aws:sqs:${AWS::Region}:${AWS::AccountId}:checkout-queue
            - Sid: VisualEditor1
              Effect: Allow
              Action:
                - dynamodb:Query
              Resource:
                - !Sub arn:aws:dynamodb:${AWS::Region}:${AWS::AccountId}:table/Orders
      Environment:
        Variables:
          QUEUE_NAME: "checkout-queue"
```

![CreateFunctions](/images/3-create-api-lambda-function/3-create-lambda-function-5.png?featherlight=false&width=90pc)

- Thêm đoạn script dưới đây để tạo function **HandleOrder**

```
  HandleOrder:
    Type: AWS::Serverless::Function
    Properties:
      FunctionName: handle_order
      CodeUri: fcj-book-store/handle_order
      Handler: handle_order.lambda_handler
      Runtime: python3.9
      Architectures:
        - x86_64
      Policies:
        - Statement:
            - Sid: VisualEditor0
              Effect: Allow
              Action:
                - dynamodb:PutItem
                - dynamodb:BatchWriteItem
                - sqs:*
              Resource:
                - !Sub arn:aws:dynamodb:${AWS::Region}:${AWS::AccountId}:table/Orders
                - !Sub arn:aws:sqs:${AWS::Region}:${AWS::AccountId}:checkout-queue
      Environment:
        Variables:
          QUEUE_NAME: "checkout-queue"
```

![CreateFunctions](/images/3-create-api-lambda-function/3-create-lambda-function-6.png?featherlight=false&width=90pc)

- Thêm đoạn script dưới đây để tạo function **DeleteOrder**

```
  DeleteOrder:
    Type: AWS::Serverless::Function
    Properties:
      FunctionName: delete_order
      CodeUri: fcj-book-store/delete_order
      Handler: delete_order.lambda_handler
      Runtime: python3.9
      Architectures:
        - x86_64
      Policies:
        - Statement:
            - Sid: VisualEditor0
              Effect: Allow
              Action:
                - sqs:*
                - dynamodb:DeleteItem
              Resource:
                - !Sub arn:aws:sqs:${AWS::Region}:${AWS::AccountId}:checkout-queue
                - !Sub arn:aws:dynamodb:${AWS::Region}:${AWS::AccountId}:table/Orders
      Environment:
        Variables:
          QUEUE_NAME: "checkout-queue"
```

![CreateFunctions](/images/3-create-api-lambda-function/3-create-lambda-function-7.png?featherlight=false&width=90pc)

4. Thêm các thư mục và tệp source code cho các function. Cấu trúc thư mục như sau:

```
fcj-book-store-sam-ws6
├── fcj-book-store
│   ├── checkout_order
│   │   └── checkout_order.py
│   ├── order_management
│   │   └── order_management.py
│   ├── handle_order
│   │   └── handle_order.py
│   ├── delete_order
│   │   └── delete_order.py
│   ├── ....
└── template.yaml
```

- Tạo thư mục tên **checkout_order** trong thư mục **fcj-book-store-sam-ws6/fcj-book-store**
- Tạo tệp **checkout_order.py** và sao chép đoạn code sau vào nó.

```
import json
import boto3
import os

    
def lambda_handler(event, context):
    client = boto3.client("sqs")
    sns = boto3.client('sns')
    queue_name = os.getenv("QUEUE_NAME")
    status = 200
    try:
        response = client.get_queue_url(
            QueueName=queue_name
        )
        
        send_response = client.send_message(
            QueueUrl=response['QueueUrl'], 
            MessageBody=event["body"]
        )
    except Exception as e:
        status = 400
    
    try:
        response1 = sns.publish(
            TopicArn=os.environ['SNS_ARN'],    
            Message="There is a new order. Please check it!",    
        )
    except Exception as e:
        status = 400
        print(e)

    return {
        'statusCode': status,
        'body': json.dumps(response["ResponseMetadata"]),
        'headers': {
                'Content-Type': 'application/json',
                'Access-Control-Allow-Origin': '*'
            }
    }
```

- Tạo thư mục tên **order_management** trong thư mục **fcj-book-store-sam-ws6/fcj-book-store**
- Tạo
 **order_management.py** và sao chép đoạn code sau vào nó.

```
import boto3
import json
import os
from boto3.dynamodb.types import TypeDeserializer

# Create SQS client
sqs_client = boto3.client('sqs')
# Create DynamoDB client
dynamodb_client = boto3.client('dynamodb')
serializer = TypeDeserializer()


def deserialize(data):
    if isinstance(data, list):
        return [deserialize(v) for v in data]

    if isinstance(data, dict):
        try:
            return serializer.deserialize(data)
        except TypeError:
            return {k: deserialize(v) for k, v in data.items()}
    else:
        return data


def format_db_data(messages, db_data):
    if 'Items' in db_data:
        format_data = deserialize(db_data["Items"])
    price = 0
    for book_item in format_data:
        price = book_item['price']
        del book_item['price']
        del book_item['id']
    messages.append({
        "receiptHandle": "",
        "books": format_data,
        "price": price,
        "status": "Processed"
    })


def get_order_from_dynamodb(messages):
    data = []
    i = 1
    while True:
        id = str(i)
        data = dynamodb_client.query(
            TableName="Orders", KeyConditionExpression="id = :id", ExpressionAttributeValues={":id": {"S": id}})
        if not data["Items"]:
            break
        format_db_data(messages, data)
        i += 1


def get_order_from_sqs(messages):
    queue_name = os.getenv("QUEUE_NAME")
    queue = sqs_client.get_queue_url(QueueName=queue_name)
    queue_url = queue['QueueUrl']
    response = sqs_client.get_queue_attributes(
        QueueUrl=queue_url,
        AttributeNames=['ApproximateNumberOfMessages']
    )

    number_of_message = int(
        response['Attributes']['ApproximateNumberOfMessages'])
    print(number_of_message)
    i = 0
    while i < number_of_message:
        msg_list = sqs_client.receive_message(
            QueueUrl=queue_url,
            MaxNumberOfMessages=10,
            WaitTimeSeconds=20,
            VisibilityTimeout=3
        )
        if 'Messages' in msg_list:
            for m in msg_list['Messages']:
                print(json.loads(m["Body"]))
                messages.append({
                    "receiptHandle": m["ReceiptHandle"],
                    "books": json.loads(m["Body"])['books'],
                    "price": json.loads(m["Body"])['price'],
                    "status": "Unprocessed"
                })
                i += 1


def lambda_handler(event, context):
    messages = []

    get_order_from_dynamodb(messages)
    get_order_from_sqs(messages)
    print(messages)
    return{
        'statusCode': 200,
        'body': json.dumps(messages),
        'headers': {
            'Content-Type': 'application/json',
            'Access-Control-Allow-Origin': '*'
        },
    }

```

- Tạo thư mục tên **handle_order** trong thư mục **fcj-book-store-sam-ws6/fcj-book-store**
- Tạo
 **handle_order.py** và sao chép đoạn code sau vào nó.

```
import boto3
import json
import os

dynamodb_client = boto3.resource('dynamodb')
table = dynamodb_client.Table('Orders')
sqs_client = boto3.client('sqs')


def lambda_handler(event, context):
    order_item = json.loads(event["body"])
    products_infor = order_item['books']
    print(order_item)
    for book_item in products_infor:
        print(book_item)
        data = {
            "id": str(order_item['id']),
            "book_id": book_item['id'],
            "name": book_item['name'],
            "qty": str(book_item['qty']),
            "price": str(order_item['price'])
        }
        print(data)
        table.put_item(Item=data)

    queue_name = os.getenv("QUEUE_NAME")
    queue = sqs_client.get_queue_url(QueueName=queue_name)
    queue_url = queue['QueueUrl']
    response = sqs_client.delete_message(
        QueueUrl=queue_url,
        ReceiptHandle=order_item['receiptHandle']
    )

    response = {
        'statusCode': 200,
        'body': 'successfully handle order!',
        'headers': {
            'Content-Type': 'application/json',
            "Access-Control-Allow-Headers": "Access-Control-Allow-Headers, Origin, Accept, X-Requested-With, Content-Type, Access-Control-Request-Method,X-Access-Token, XKey, Authorization",
            "Access-Control-Allow-Origin": "*",
            "Access-Control-Allow-Methods": "GET,PUT,POST,DELETE,OPTIONS"
        },
    }
    return response

```

- Tạo thư mục tên **delete_order** trong thư mục **fcj-book-store-sam-ws6/fcj-book-store**
- Tạo tệp **delete_order.py** và sao chép đoạn code sau vào nó.

```
import boto3
import json
import os

dynamodb_client = boto3.client('dynamodb')
sqs_client = boto3.client('sqs')


def lambda_handler(event, context):
    order_item = json.loads(event["body"])
    if order_item['receiptHandle']:
        queue_name = os.getenv("QUEUE_NAME")
        queue = sqs_client.get_queue_url(QueueName=queue_name)
        queue_url = queue['QueueUrl']
        response = sqs_client.delete_message(
            QueueUrl=queue_url,
            ReceiptHandle=order_item['receiptHandle']
        )

    response = {
        'statusCode': 200,
        'body': 'successfully handle order!',
        'headers': {
            'Content-Type': 'application/json',
            "Access-Control-Allow-Headers": "Access-Control-Allow-Headers, Origin, Accept, X-Requested-With, Content-Type, Access-Control-Request-Method,X-Access-Token, XKey, Authorization",
            "Access-Control-Allow-Origin": "*",
            "Access-Control-Allow-Methods": "GET,PUT,POST,DELETE,OPTIONS"
        },
    }
    
    return response
```

5. Chạy các lệnh dưới đây

```
sam build
sam deploy --guided
```

![CreateFunctions](/images/3-create-api-lambda-function/3-create-lambda-function-8.png?featherlight=false&width=90pc)

6. Mở bảng điều khiển của [AWS Lambda](https://ap-southeast-1.console.aws.amazon.com/lambda/home?region=ap-southeast-1#/functions) để kiểm tra các function.

![CreateFunctions](/images/3-create-api-lambda-function/3-create-lambda-function-9.png?featherlight=false&width=90pc)
