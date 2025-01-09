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
              QUEUE_NAME: !Ref checkoutQueueName
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
