---
title : "Tạo Lambda function"
date :  "`r Sys.Date()`" 
weight : 1
chapter : false
pre : " <b> 3.1 </b> "
---
In this step, we will create a new DynamoDB table to store processed order data and four Lambda functions to store orders, manage orders, delete orders, and process orders with SAM template

1. Open **template.yaml** file in source code folder - **fcj-book-store-sam-ws6** 
- Add the below script to create **Orders** table in DynamoDB

```
  OrdersTable:
    Type: AWS::Serverless::SimpleTable
    Properties:
      TableName: Orders
      PrimaryKey:
        Name: id
        Type: String
```

![CreateOrderTable](/images/3-create-api-lambda-function/3-create-lambda-function-1.png?featherlight=false&width=90pc)

2. Run the below commands
```
sam build
sam deploy --guided
```
![CreateOrderTable](/images/3-create-api-lambda-function/3-create-lambda-function-2.png?featherlight=false&width=90pc)

- Open [AWS DynamDB console](https://ap-southeast-1.console.aws.amazon.com/dynamodbv2/home?region=ap-southeast-1#tables) to check

![CreateOrderTable](/images/3-create-api-lambda-function/3-create-lambda-function-3.png?featherlight=false&width=90pc)

3. Add the below script to create **CheckOutOrder** function
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

- Add the below script to create **OrderManagement** function
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

- Add the below script to create **HandleOrder** function
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

- Add the below script to create **DeleteOrder** function
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

4. Add directories and source code files for functions. The directory structure is as follows:
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
- Create **checkout_order** folder in **fcj-book-store-sam-ws6/fcj-book-store** folder
- Create **checkout_order.py** file and copy the below code to it
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

- Create **order_management** folder in **fcj-book-store-sam-ws6/fcj-book-store** folder
- Create **order_management.py** file and copy the below code to it
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
- Create **handle_order** folder in **fcj-book-store-sam-ws6/fcj-book-store** folder
- Create **handle_order.py** file and copy the below code to it
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
- Create **delete_order** folder in **fcj-book-store-sam-ws6/fcj-book-store** folder
- Create **delete_order.py** file and copy the below code to it
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
5. Run the below commands
```
sam build
sam deploy --guided
```

![CreateFunctions](/images/3-create-api-lambda-function/3-create-lambda-function-8.png?featherlight=false&width=90pc)

6. Open [AWS Lambda console](https://ap-southeast-1.console.aws.amazon.com/lambda/home?region=ap-southeast-1#/functions) to check functions

![CreateFunctions](/images/3-create-api-lambda-function/3-create-lambda-function-9.png?featherlight=false&width=90pc)
