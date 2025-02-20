---
title : "Create OrdersTable DynamoDB table"
date :  2025-02-11
weight : 1
chapter : false
pre : " <b> 3.1 </b> "
---
In this step, we will create a new DynamoDB table using a SAM template.

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
            - AttributeName: book_id
              AttributeType: S
          KeySchema:
            - AttributeName: id
              KeyType: HASH
            - AttributeName: book_id
              KeyType: RANGE
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
