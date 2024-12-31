---
title : "Tạo các API"
date :  "`r Sys.Date()`" 
weight : 2
chapter : false
pre : " <b> 3.2 </b> "
---
Chúng ta sẽ tạo các API để tích hợp với các function đã tạo ở bước trước.
1. Mở tệp **template.yaml** của thư mục source code **fcj-book-store-sam-ws6**
- Thêm đoạn script sau vào dưới api **/confirm_user** để tạo resource **/books/order** với method GET
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

- Thêm đoạn script sau để tạo method POST
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

- Thêm đoạn script sau để tạo method DELETE
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

- Thêm đoạn script sau để tạo resource **/books/order/handle** với method POST
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

2. Thêm event source cho các Lambda function để tích hợp với các APIs
- Thêm đoạn script sau vào phía dưới của function **CheckOutOrder**
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

- Thêm đoạn script sau vào phía dưới của function **OrderManagement**
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

- Thêm đoạn script sau vào phía dưới của function **HandleOrder**
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

- Thêm đoạn script sau vào phía dưới của function **DeleteOrder**
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

3. Chạy các câu lệnh dưới đây:
```
sam build
sam deploy --guided
```

- Nhập "y" nếu được hỏi "CheckOurOrder may not have authorization defined, Is this okay? [y/N]:"
- Nhập "y" nếu được hỏi "OrderManagement may not have authorization defined, Is this okay? [y/N]:"
- Nhập "y" nếu được hỏi "HandleOrder may not have authorization defined, Is this okay? [y/N]:"
- Nhập "y" nếu được hỏi "DeleteOrder may not have authorization defined, Is this okay? [y/N]:"

![CreateAPIs](/images/3-create-api-lambda-function/3-create-apis-9.png?featherlight=false&width=90pc)
![CreateAPIs](/images/3-create-api-lambda-function/3-create-apis-10.png?featherlight=false&width=90pc)

Vậy là chúng ta đã hoàn thành các bước thiết lập. Phần tiếp theo chúng ta sẽ kiểm tra hoạt động của trang web