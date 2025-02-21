---
title : "Create handle_order Lambda function"
date :  2025-02-11
weight : 4
chapter : false
pre : " <b> 3.4 </b> "
---
In this step, we will create a new handle_order Lambda function using a SAM template.

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
      #     - FcjCheckoutOrderApi
      #     - FcjOrderManagementApi

      BookApiStage:
        Type: AWS::ApiGateway::Stage
        Properties:
          RestApiId: !Ref BookApi
          StageName: !Ref stage
      #     DeploymentId: !Ref BookApiDeployment
      ```

      ![CreateHandleOrderFunction](/images/temp/1/33.png?width=90pc)

2. Run the below commands.

    ```bash
    sam build
    sam validate
    sam deploy
    ```

    ![CreateHandleOrderFunction](/images/temp/1/35.png?width=90pc)

#### Create FcjHandleOrder function

1. Open **template.yaml** in the source code you downloaded before.
    - Add the following scripts below to create **FcjHandleOrder** function.

      ```yaml
      handleOrderPathPart:
        Type: String
        Default: handle
      ```

      ![CreateHandleOrderFunction](/images/temp/1/52.png?width=90pc)

      ```yaml
      FcjHandleOrderResource:
        Type: AWS::ApiGateway::Resource
        Properties:
          RestApiId: !Ref BookApi
          ParentId: !Ref FcjCheckoutOrderResource
          PathPart: !Ref handleOrderPathPart

      FcjHandleOrderFunction:
        Type: AWS::Serverless::Function
        Properties:
          CodeUri: fcj-book-shop/handle_order
          Handler: handle_order.lambda_handler
          Runtime: python3.11
          FunctionName: handle_order
          Environment:
            Variables:
              ORDER_TABLE_NAME: !Ref orderTable
              SQS_QUEUE_URL: !Ref checkoutQueueUrl
          Architectures:
            - x86_64
          Policies:
            - Statement:
                - Sid: VisualEditor0
                  Effect: Allow
                  Action:
                    - dynamodb:PutItem
                    - dynamodb:BatchWriteItem
                  Resource:
                    - !Sub "arn:aws:dynamodb:${AWS::Region}:${AWS::AccountId}:table/${orderTable}"
                - Sid: VisualEditor1
                  Effect: Allow
                  Action:
                    - sqs:*
                  Resource:
                    - !Sub "arn:aws:sqs:${AWS::Region}:${AWS::AccountId}:${checkoutQueueName}"

      FcjHandleOrderApiOptions:
        Type: AWS::ApiGateway::Method
        Properties:
          HttpMethod: OPTIONS
          RestApiId: !Ref BookApi
          ResourceId: !Ref FcjHandleOrderResource
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

      FcjHandleOrderApi:
        Type: AWS::ApiGateway::Method
        Properties:
          HttpMethod: POST
          RestApiId: !Ref BookApi
          ResourceId: !Ref FcjHandleOrderResource
          AuthorizationType: NONE
          Integration:
            Type: AWS_PROXY
            IntegrationHttpMethod: POST # For Lambda integrations, you must set the integration method to POST
            Uri: !Sub >-
              arn:aws:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${FcjHandleOrderFunction.Arn}/invocations
          MethodResponses:
            - StatusCode: "200"
              ResponseParameters:
                method.response.header.Access-Control-Allow-Origin: true
                method.response.header.Access-Control-Allow-Methods: true
                method.response.header.Access-Control-Allow-Headers: true

      FcjHandleOrderApiInvokePermission:
        Type: AWS::Lambda::Permission
        Properties:
          FunctionName: !Ref FcjHandleOrderFunction
          Action: lambda:InvokeFunction
          Principal: apigateway.amazonaws.com
          SourceAccount: !Ref "AWS::AccountId"
      ```

      ![CreateHandleOrderFunction](/images/temp/1/49.png?width=90pc)

2. The directory structure is as follows.

    ```bash
    fcj-book-shop-sam-ws3
    ├── fcj-book-shop
    │   ├── checkout_order
    │   │   └── checkout_order.py
    │   ├── order_management
    │   │   └── order_management.py
    │   ├── handle_order
    │   │   └── handle_order.py
    │   ├── ...
    │
    └── template.yaml
    ```

    - Create **handle_order** folder in **fcj-book-shop-sam-ws6/fcj-book-shop/** folder.
    - Create **handle_order.py** file and copy the following code to it.

      ```py
      import json
      import boto3
      import os

      headers = {
          "Content-Type": "application/json",
          "Access-Control-Allow-Origin": "*",
          "Access-Control-Allow-Methods": "OPTIONS,POST,GET,DELETE",
          "Access-Control-Allow-Headers": "Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token"
      }

      dynamodb = boto3.resource('dynamodb')
      sqs = boto3.client('sqs')

      # Define the DynamoDB table name and SQS queue URL
      table_name = os.getenv('ORDER_TABLE_NAME')
      queue_url = os.getenv('SQS_QUEUE_URL')


      def lambda_handler(event, context):
          # Get product body info from the event
          products = json.loads(event['body'])
          books = products['books']
          receipt_handle = products['receiptHandle']

          # Write product info to DynamoDB
          table = dynamodb.Table(table_name)
          try:
              for book in books:
                  data = {
                      'id': products['id'],
                      'book_id': book['id'],
                      'name': book['name'],
                      'qty': book['qty'],
                      'price': str(products['price'])
                  }

                  table.put_item(Item=data)
          except Exception as e:
              print(f"Error writing to DynamoDB: {e}")
              raise Exception(f"Error writing to DynamoDB: {e}")

          # Delete message in SQS
          try:
              sqs.delete_message(
                  QueueUrl=queue_url,
                  ReceiptHandle=receipt_handle
              )
          except Exception as e:
              print(f"Error deleting message from SQS: {e}")
              raise Exception(f"Error deleting message from SQS: {e}")

          return {
              'statusCode': 200,
              'headers': headers,
              'body': json.dumps('Order processed successfully')
          }
      ```

      ![CreateHandleOrderFunction](/images/temp/1/50.png?width=90pc)

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
        - FcjOrderManagementApi
        - FcjHandleOrderApi

    BookApiStage:
      Type: AWS::ApiGateway::Stage
      Properties:
        RestApiId: !Ref BookApi
        StageName: !Ref stage
        DeploymentId: !Ref BookApiDeployment
    ```

    ![CreateHandleOrderFunction](/images/temp/1/51.png?width=90pc)

4. Run the below commands.

    ```bash
    sam build
    sam validate
    sam deploy
    ```

    ![CreateHandleOrderFunction](/images/temp/1/53.png?width=90pc)

#### Check the creation

1. Open [Amazon API Gateway console](https://us-east-1.console.aws.amazon.com/apigateway/main/apis?region=us-east-1).
    - Click **fcj-serverless-api**.
      ![CreateHandleOrderFunction](/images/temp/1/38.png?width=90pc)
    - Click **Resources** on the left menu.
    - Check **/handle** just created.
      ![CreateHandleOrderFunction](/images/temp/1/54.png?width=90pc)

2. Open [Amazon Lambda console](https://us-east-1.console.aws.amazon.com/lambda/home?region=us-east-1#/functions).
    - Click **Functions** on the left menu.
    - Choose **handle_order** function.
      ![CreateHandleOrderFunction](/images/temp/1/55.png?width=90pc)
    - At **handle_order** page, check the function that just created.
      ![CreateHandleOrderFunction](/images/temp/1/56.png?width=90pc)
