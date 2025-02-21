---
title : "Preparation"
date :  2025-02-11
weight : 1 
chapter : false
pre : " <b> 1. </b> "
---
Before we get to the main content of this workshop, we need to reset the web application.

1. Download the below source code.

    {{%attachments title="Source code" pattern=".*\.(zip)$"/%}}

2. Unzip the source code, open **template.yaml**.
    - Change **chaunguyen.site** value to your domain name.

      ```yaml
      route53HostedZoneName:
        Type: String
        Default: chaunguyen.site
      ```

      ![Preparation](/images/temp/1/89.png?width=90pc)
3. Run the below commands.
{{% notice note %}}
Ensure you have the AWS CLI and SAM CLI installed on your machine, configure AWS credentials before running the commands.
{{% /notice %}}

    ```bash
    sam build
    sam validate
    sam deploy --guided
    ```

4. Enter the following content. Leave as default.
    - Stack Name []: `fcj-book-store`
    - AWS Region []: `us-east-1`
    - Confirm changes before deploy [Y/n]: y
    - Allow SAM CLI IAM role creation [Y/n]: y
    - Disable rollback [y/N]: n
    - Save arguments to configuration file [Y/n]: y
      ![Preparation](/images/temp/1/1.png?width=90pc)

5. Follow this instruction if your domain registrar isn't AWS.
  {{% notice note %}}
  [Making Route 53 the DNS service for a domain that's in use](https://docs.aws.amazon.com/Route53/latest/DeveloperGuide/migrate-dns-domain-in-use.html)
  {{% /notice %}}
      - Copy these NS type records from your Route53 hosted zone.
        ![Preparation](/images/temp/1/4.png?width=90pc)
      - Paste those NS records to your DNS domain registrar.
        ![Preparation](/images/temp/1/5.png?width=90pc)

6. Open **template.yaml** in the source code you downloaded before.
    - Uncomment this code block.

      ```yaml
      # FcjCertificate:
      #   Type: AWS::CertificateManager::Certificate
      #   Properties:
      #     DomainName: !Sub "*.${route53HostedZoneName}"
      #     SubjectAlternativeNames:
      #       - !Ref route53HostedZoneName
      #     ValidationMethod: DNS
      #     DomainValidationOptions:
      #       - DomainName: !Sub "*.${route53HostedZoneName}"
      #         HostedZoneId: !Ref FcjRoute53HostedZone

      # FcjCloudFrontDistribution:
      #   Type: AWS::CloudFront::Distribution
      #   Properties:
      #     DistributionConfig:
      #       Enabled: true
      #       Origins:
      #         - DomainName: !GetAtt FcjBookShop.RegionalDomainName
      #           Id: !Ref FcjBookShop
      #           CustomOriginConfig:
      #             HTTPPort: 80
      #             HTTPSPort: 443
      #             OriginProtocolPolicy: match-viewer
      #             OriginSSLProtocols:
      #               - TLSv1
      #               - TLSv1.1
      #               - TLSv1.2
      #       Aliases:
      #         - !Sub "www.${route53HostedZoneName}"
      #         - !Ref route53HostedZoneName
      #       DefaultCacheBehavior:
      #         CachePolicyId: !Ref cachePolicyId
      #         ViewerProtocolPolicy: redirect-to-https
      #         TargetOriginId: !Ref FcjBookShop
      #         AllowedMethods:
      #           - GET
      #           - HEAD
      #       DefaultRootObject: index.html
      #       ViewerCertificate:
      #         AcmCertificateArn: !Ref FcjCertificate
      #         SslSupportMethod: sni-only

      # FcjRoute53RecordSet:
      #   Type: AWS::Route53::RecordSet
      #   Properties:
      #     HostedZoneId: !Ref FcjRoute53HostedZone
      #     Name: !Sub "www.${route53HostedZoneName}."
      #     Type: A
      #     AliasTarget:
      #       DNSName: !GetAtt FcjCloudFrontDistribution.DomainName
      #       HostedZoneId: !Ref cloudFrontHostedZoneId
      ```

    ![Preparation](/images/temp/1/6.png?width=90pc)

7. Run the below commands.
    > Cloudfront Distribution can take a while to complete, so please be patient.

    ```bash
    sam build
    sam validate
    sam deploy
    ```

    ![Preparation](/images/temp/1/7.png?width=90pc)

8. Download the **FCJ-Serverless-Workshop** code to your device.
    - Open a terminal on your computer in the folder where you want to save the source code.
    - Copy the below command.

      ```bash
      git clone https://github.com/AWS-First-Cloud-Journey/FCJ-Serverless-Workshop.git
      cd FCJ-Serverless-Workshop
      ```

    - Open **FCJ-Serverless-Workshop** with VSCode and edit.
      - Open **src/App.js** and edit ``isAdmin: true`` as below.
        ![Preparation](/images/temp/1/64.png?width=90pc)

    - Back to **FCJ-Serverless-Workshop** root path and run the commands below.

      ```bash
      yarn
      yarn build
      ```

9. We have finished building the front-end. Next execute the following command to upload the **build** folder to S3.

    ```bash
    aws s3 cp build s3://fcj-book-shop-by-myself --recursive
    ```

10. Enter the following links in a new tab in your web browser: ``http://www.DOMAIN``, replace all DOMAIN with your domain name. All those links redirect to the new path, replace http with https.
    ![Preparation](/images/temp/1/8.png?width=90pc)

So we have rebuilt the web application.
