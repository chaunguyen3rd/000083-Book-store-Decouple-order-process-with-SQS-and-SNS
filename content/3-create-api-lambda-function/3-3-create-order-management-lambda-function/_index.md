---
title : "Create order_management Lambda function"
date :  "`r Sys.Date()`" 
weight : 3
chapter : false
pre : " <b> 3.3 </b> "
---
In this step, we will create a new order_management Lambda function using a SAM template.

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

      ![CreateOrderManagementFunction](/images/temp/1/33.png?width=90pc)

2. Run the below commands.

    ```bash
    sam build
    sam validate
    sam deploy --guided
    ```

    ![CreateOrderManagementFunction](/images/temp/1/35.png?width=90pc)

#### Create FcjOrderManagement function

1. Open **template.yaml** in the source code you downloaded before.
    - Add the following scripts below to create **FcjOrderManagement** function.

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
          Architectures:
            - x86_64
          Policies:
            - Statement:
                - Sid: VisualEditor0
                  Effect: Allow
                  Action:
                    - dynamodb:Query
                  Resource:
                    - !Sub "arn:aws:dynamodb:${AWS::Region}:${AWS::AccountId}:${orderTable}"
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

      <!-- ADD IMAGE 42 HERE -->

2. The directory structure is as follows.

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

    - Create **order_management** folder in **fcj-book-shop-sam-ws6/fcj-book-shop/** folder.
    - Create **order_management.py** file and copy the following code to it.

      ```py
      import os
      import json
      import boto3

      # Initialize
      sqs = boto3.client('sqs')
      queue_url = os.getenv('SQS_QUEUE_URL')
      headers = {
          "Content-Type": "application/json",
          "Access-Control-Allow-Origin": "*",
          "Access-Control-Allow-Methods": "OPTIONS,POST,GET,DELETE",
          "Access-Control-Allow-Headers": "Content-Type,X-Amz-Date,Authorization,X-Api-Key,X-Amz-Security-Token"
      }


      def get_messages_from_sqs(messages):
          messages = []

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
                          "processed": False
                      })

      # Get Messages from DynamoDB


      def get_messages_from_dynamodb():
          pass


      def lambda_handler(event, context):
          messages = []

          # Get messages from sqs
          get_messages_from_sqs(messages)

          print(f"messages: {messages}")

          # Get Messages from DynamoDB

          return {
              'statusCode': 200,
              'body': json.dumps(messages),
              'headers': headers
          }
      ```

      <!-- ADD IMAGE 43 HERE -->

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

    BookApiStage:
      Type: AWS::ApiGateway::Stage
      Properties:
        RestApiId: !Ref BookApi
        StageName: !Ref stage
        DeploymentId: !Ref BookApiDeployment
    ```

    ![CreateOrderManagementFunction](/images/temp/1/44.png?width=90pc)

4. Run the below commands.

    ```bash
    sam build
    sam validate
    sam deploy --guided
    ```

    ![CreateOrderManagementFunction](/images/temp/1/45.png?width=90pc)

#### Check the creation

1. Open [Amazon API Gateway console](https://us-east-1.console.aws.amazon.com/apigateway/main/apis?region=us-east-1).
    - Click **fcj-serverless-api**.
      ![CreateOrderManagementFunction](/images/temp/1/38.png?width=90pc)
    - Click **Resources** on the left menu.
    - Check **/order** just created.
      ![CreateOrderManagementFunction](/images/temp/1/46.png?width=90pc)

2. Open [Amazon Lambda console](https://us-east-1.console.aws.amazon.com/lambda/home?region=us-east-1#/functions).
    - Click **Functions** on the left menu.
    - Choose **order_management** function.
      ![CreateOrderManagementFunction](/images/temp/1/47.png?width=90pc)
    - At **order_management** page, check the function that just created.
      <!-- ADD IMAGE 48 HERE -->