---
title : "Create APIs"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 3.2 </b> "
---
We will create APIs to integrate with the functions created in the previous step.
1. Open  **template.yaml** file of source code folder - **fcj-book-store-sam-ws6**
- Add following script under **/confirm_user** api to create **/books/order** resource with GET method
```
          /books/order:
            get:
              responses:
                "200":
                  description: 200 response
                  headers:
                    Access-Control-Allow-Origin:
                      type: string
              x-amazon-apigateway-integration:
                uri:
                  Fn::Sub: "arn:aws:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${OrderManagement.Arn}/invocations"
                responses:
                  default:
                    statusCode: 200
                    responseParameters:
                      method.response.header.Access-Control-Allow-Origin: "'*'"
                passthroughBehavior: when_no_match
                httpMethod: POST #always POST
                type: aws_proxy
```

![CreateAPIs](/images/3-create-api-lambda-function/3-create-apis-1.png?featherlight=false&width=90pc)

- Add following script to create POST method
```
            post:
              responses:
                "200":
                  description: 200 response
                  headers:
                    Access-Control-Allow-Origin:
                      type: string
              x-amazon-apigateway-integration:
                uri:
                  Fn::Sub: "arn:aws:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${CheckOutOrder.Arn}/invocations"
                responses:
                  default:
                    statusCode: 200
                    responseParameters:
                      method.response.header.Access-Control-Allow-Origin: "'*'"
                passthroughBehavior: when_no_match
                httpMethod: POST #always POST
                type: aws_prox
```

![CreateAPIs](/images/3-create-api-lambda-function/3-create-apis-2.png?featherlight=false&width=90pc)

- Add following script to create DELETE method
```
            delete:
              responses:
                "200":
                  description: 200 response
                  headers:
                    Access-Control-Allow-Origin:
                      type: string
              x-amazon-apigateway-integration:
                uri:
                  Fn::Sub: "arn:aws:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${DeleteOrder.Arn}/invocations"
                responses:
                  default:
                    statusCode: 200
                    responseParameters:
                      method.response.header.Access-Control-Allow-Origin: "'*'"
                passthroughBehavior: when_no_match
                httpMethod: POST #always POST
                type: aws_proxy
```

![CreateAPIs](/images/3-create-api-lambda-function/3-create-apis-3.png?featherlight=false&width=90pc)

- Add following script to create **/books/order/handle** resource with POST method
```
          /books/order/handle:
            post:
              responses:
                "200":
                  description: 200 response
                  headers:
                    Access-Control-Allow-Origin:
                      type: string
              x-amazon-apigateway-integration:
                uri:
                  Fn::Sub: "arn:aws:apigateway:${AWS::Region}:lambda:path/2015-03-31/functions/${HandleOrder.Arn}/invocations"
                responses:
                  default:
                    statusCode: 200
                    responseParameters:
                      method.response.header.Access-Control-Allow-Origin: "'*'"
                passthroughBehavior: when_no_match
                httpMethod: POST #always POST
                type: aws_proxy
```

![CreateAPIs](/images/3-create-api-lambda-function/3-create-apis-4.png?featherlight=false&width=90pc)

2. Add event source for Lambda functions to integrate with APIs
- Add following script under the **CheckOutOrder** function
```
      Events:
        ConfirmUser:
          Type: Api
          Properties:
            Path: /books/order
            Method: post
            RestApiId:
              Ref: BookApi
```

![CreateAPIs](/images/3-create-api-lambda-function/3-create-apis-5.png?featherlight=false&width=90pc)

- Add following script under the **OrderManagement** function
```
      Events:
        ConfirmUser:
          Type: Api
          Properties:
            Path: /books/order
            Method: get
            RestApiId:
              Ref: BookApi
```

![CreateAPIs](/images/3-create-api-lambda-function/3-create-apis-6.png?featherlight=false&width=90pc)

- Add following script under the **HandleOrder** function
```
      Events:
        ProcessOrder:
          Type: Api
          Properties:
            Path: /books/order/handle
            Method: post
            RestApiId:
              Ref: BookApi
```

![CreateAPIs](/images/3-create-api-lambda-function/3-create-apis-7.png?featherlight=false&width=90pc)

- Add following script under the **DeleteOrder** function
```
      Events:
          DeleteOrder:
            Type: Api
            Properties:
              Path: /books/order
              Method: delete
              RestApiId: 
                Ref: BookApi
```

![CreateAPIs](/images/3-create-api-lambda-function/3-create-apis-8.png?featherlight=false&width=90pc)

3. Run the below commands
```
sam build
sam deploy --guided
```

- Enter "y" if asked "CheckOurOrder may not have authorization defined, Is this okay? [y/N]:"
- Enter "y" if asked "OrderManagement may not have authorization defined, Is this okay? [y/N]:"
- Enter "y" if asked "HandleOrder may not have authorization defined, Is this okay? [y/N]:"
- Enter "y" if asked "DeleteOrder may not have authorization defined, Is this okay? [y/N]:"

![CreateAPIs](/images/3-create-api-lambda-function/3-create-apis-9.png?featherlight=false&width=90pc)
![CreateAPIs](/images/3-create-api-lambda-function/3-create-apis-10.png?featherlight=false&width=90pc)

So we have completed the setup steps. The next part we will check the website operation