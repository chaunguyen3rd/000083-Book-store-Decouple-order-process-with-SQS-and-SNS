---
title : "Create checkout_order Lambda function"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 3.2 </b> "
---
In this step, we will create a new checkout_order Lambda function using a SAM template.

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

      ![CreateCheckoutOrderFunction](/images/temp/1/33.png?width=90pc)

2. Run the below commands.

    ```bash
    sam build
    sam validate
    sam deploy --guided
    ```

    ![CreateCheckoutOrderFunction](/images/temp/1/35.png?width=90pc)

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

        ![CreateCheckoutOrderFunction](/images/temp/1/31.png?width=90pc)

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
            ParentId: !Ref BookApiResource
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

        ![CreateCheckoutOrderFunction](/images/temp/1/32.png?width=90pc)

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

      ![CreateCheckoutOrderFunction](/images/temp/1/37.png?width=90pc)

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

    ![CreateCheckoutOrderFunction](/images/temp/1/36.png?width=90pc)

4. Run the below commands.

    ```bash
    sam build
    sam validate
    sam deploy --guided
    ```

    ![CreateCheckoutOrderFunction](/images/temp/1/34.png?width=90pc)

#### Check the creation

1. Open [Amazon API Gateway console](https://us-east-1.console.aws.amazon.com/apigateway/main/apis?region=us-east-1).
    - Click **fcj-serverless-api**.
      ![CreateCheckoutOrderFunction](/images/temp/1/38.png?width=90pc)
    - Click **Resources** on the left menu.
    - Check **/order** just created.
      ![CreateCheckoutOrderFunction](/images/temp/1/39.png?width=90pc)

2. Open [Amazon Lambda console](https://us-east-1.console.aws.amazon.com/lambda/home?region=us-east-1#/functions).
    - Click **Functions** on the left menu.
    - Choose **checkout_order** function.
      ![CreateCheckoutOrderFunction](/images/temp/1/40.png?width=90pc)
    - At **checkout_order** page, check the function that just created.
      ![CreateCheckoutOrderFunction](/images/temp/1/41.png?width=90pc)
