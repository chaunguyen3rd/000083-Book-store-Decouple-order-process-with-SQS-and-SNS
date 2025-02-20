---
title : "Create delete_order Lambda function"
date :  2025-02-11
weight : 5
chapter : false
pre : " <b> 3.5 </b> "
---
In this step, we will create a new delete_order Lambda function using a SAM template.

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
      #     - FcjHandleOrderApi

      # BookApiStage:
      #   Type: AWS::ApiGateway::Stage
      #   Properties:
      #     RestApiId: !Ref BookApi
      #     StageName: !Ref stage
      #     DeploymentId: !Ref BookApiDeployment
      ```

      ![CreateDeleteOrderFunction](/images/temp/1/33.png?width=90pc)

2. Run the below commands.

    ```bash
    sam build
    sam validate
    sam deploy --guided
    ```

    ![CreateDeleteOrderFunction](/images/temp/1/35.png?width=90pc)

#### Create FcjDeleteOrder function

1. Open **template.yaml** in the source code you downloaded before.
    - Add the following scripts below to create **FcjDeleteOrder** function.

      ```yaml
      FcjDeleteOrderFunction:
        Type: AWS::Serverless::Function
        Properties:
          CodeUri: fcj-book-shop/delete_order
          Handler: delete_order.lambda_handler
          Runtime: python3.11
          FunctionName: delete_order
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
                    - sqs:*
                  Resource:
                    - !Sub "arn:aws:sqs:${AWS::Region}:${AWS::AccountId}:${checkoutQueueName}"

      FcjDeleteOrderApi:
        Type: AWS::ApiGateway::Method
        Properties:
          HttpMethod: DELETE
          RestApiId: !Ref BookApi
          ResourceId: !Ref FcjCheckoutOrderResource
          AuthorizationType: NONE
          Integration:
            Type: AWS_PROXY
            IntegrationHttpMethod: POST # For Lambda integrations, you must set the integration method to POST
            Uri: !Sub >-
              arn:aws:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${FcjDeleteOrderFunction.Arn}/invocations
          MethodResponses:
            - StatusCode: "200"
              ResponseParameters:
                method.response.header.Access-Control-Allow-Origin: true
                method.response.header.Access-Control-Allow-Methods: true
                method.response.header.Access-Control-Allow-Headers: true

      FcjDeleteOrderApiInvokePermission:
        Type: AWS::Lambda::Permission
        Properties:
          FunctionName: !Ref FcjDeleteOrderFunction
          Action: lambda:InvokeFunction
          Principal: apigateway.amazonaws.com
          SourceAccount: !Ref "AWS::AccountId"
      ```

      ![CreateDeleteOrderFunction](/images/temp/1/57.png?width=90pc)

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
    │   ├── delete_order
    │   │   └── delete_order.py
    │   ├── ...
    │
    └── template.yaml
    ```

    - Create **delete_order** folder in **fcj-book-shop-sam-ws6/fcj-book-shop/** folder.
    - Create **delete_order.py** file and copy the following code to it.

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

      sqs = boto3.client('sqs')

      # Get value of the environment variables
      queue_url = os.getenv('SQS_QUEUE_URL')


      def lambda_handler(event, context):
          order = json.loads(event['body'])

          try:
              if 'receiptHandle' in order and order['receiptHandle']:
                  sqs.delete_message(
                      QueueUrl=queue_url,
                      ReceiptHandle=order['receiptHandle']
                  )

              else:
                  raise Exception("No receiptHandle provided")

          except Exception as e:
              print(f"Error deleting order: {e}")
              raise Exception(f"Error deleting order: {e}")

          return {
              'statusCode': 200,
              'headers': headers,
              'body': json.dumps('Order deleted successfully')
          }
      ```

      ![CreateDeleteOrderFunction](/images/temp/1/58.png?width=90pc)

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
        - FcjDeleteOrderApi

    BookApiStage:
      Type: AWS::ApiGateway::Stage
      Properties:
        RestApiId: !Ref BookApi
        StageName: !Ref stage
        DeploymentId: !Ref BookApiDeployment
    ```

    ![CreateDeleteOrderFunction](/images/temp/1/59.png?width=90pc)

4. Run the below commands.

    ```bash
    sam build
    sam validate
    sam deploy --guided
    ```

    ![CreateDeleteOrderFunction](/images/temp/1/60.png?width=90pc)

#### Check the creation

1. Open [Amazon API Gateway console](https://us-east-1.console.aws.amazon.com/apigateway/main/apis?region=us-east-1).
    - Click **fcj-serverless-api**.
      ![CreateDeleteOrderFunction](/images/temp/1/38.png?width=90pc)
    - Click **Resources** on the left menu.
    - Check **/order** just created.
      ![CreateDeleteOrderFunction](/images/temp/1/61.png?width=90pc)

2. Open [Amazon Lambda console](https://us-east-1.console.aws.amazon.com/lambda/home?region=us-east-1#/functions).
    - Click **Functions** on the left menu.
    - Choose **delete_order** function.
      ![CreateDeleteOrderFunction](/images/temp/1/62.png?width=90pc)
    - At **delete_order** page, check the function that just created.
      ![CreateDeleteOrderFunction](/images/temp/1/63.png?width=90pc)
