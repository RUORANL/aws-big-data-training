---
title: "实验1-1 - 创建资源"
date: 2023-08-12T20:08:25+08:00
draft: false

menu:
  after:
    weight: 10
    identifier: lab1-1
    parent: log-analytics-with-kinesis-and-athena
---

## 实验1-1 - 使用CloudFormation创建实验资源

点击下方的“启动堆栈”以启动Cloudformation堆栈创建，然后如下图所示点击“下一步”。  
{{< button href="https://github.com/alex-shpak/hugo-book" >}}启动堆栈{{< /button >}}    

https://console.aws.amazon.com/cloudformation/home?#/stacks/new?stackName=KinesisLab&templateURL=https://big-data-training-cfn-templates.s3.amazonaws.com/SetupLabKinesis.yaml

![图片描述：创建堆栈](/lab2-1-create-stack.png)

在堆栈详情页面，保持所有默认设置，再次点击“下一步”。  
![图片描述：堆栈详情](/lab2-1-stack-details.png)

向下滚动，在堆栈选项页面上不做任何更改，点击“下一步”。  
![图片描述：堆栈选项](/lab2-1-stack-options.png)

最后，在审查页面上，检查底部的确认框并点击“创建堆栈”。  
![图片描述：审查堆栈](/lab2-1-review-stack.png)

堆栈创建将需要大约5分钟才能完成。  
之后，您应该会看到如下显示。首先，它会显示“创建进行中(CREATE_IN_PROGRESS)”，然后最后变为“创建完成(CREATE_COMPLETE)”。

![图片描述：创建完成](/lab2-1-creation-complete.png)

一旦堆栈创建完成，请转到“输出”选项卡并记下由Cloudformation创建的Firehose Delivery和S3 bucket资源。

![图片描述：输出信息](/lab2-1-output-tab.png)


## 模板介绍
```yaml
AWSTemplateFormatVersion: 2010-09-09
Parameters:
  KinesisFirehoseDeliveryStream:
    Description: The Name for KinesisFirehose DeliveryStream
    Type: String  
    Default: 'access-logs-kdf-deliverstream'
    
  KinesisDataStream: 
    Description: The Name for KinesisDataStream
    Type: String
    Default: 'access-logs-kds-stream'

  Department:
    Description: The department for this resource
    Type: String
    Default: 'AWSBigData'
  Team:
    Description: The team for this resource
    Type: String
    Default: 'Kinesis'
  Owner:
    Description: The owner of this resource
    Type: String

Resources:
  MyS3Bucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: !Sub 'access-logs-tsv-${AWS::AccountId}'
      AccessControl: Private
  MyAthenaResultBucket:
    Type: AWS::S3::Bucket
    Properties:
      BucketName: !Sub 'access-logs-tsv-${AWS::AccountId}'
      AccessControl: Private
  KinesisDataStreamRole:
    Type: "AWS::IAM::Role"
    Properties:
      Path: '/'
      AssumeRolePolicyDocument:
        Version: "2012-10-17"
        Statement:
          -
            Effect: "Allow"
            Principal:
              Service:
                - "kinesis.amazonaws.com"
                - "firehose.amazonaws.com"
            Action:
              - "sts:AssumeRole"
            Condition:
              StringEquals:
                "sts:ExternalId": !Ref "AWS::AccountId"

  KinesisDataStreamPolicy:
    Type: "AWS::IAM::Policy"
    Properties:
      PolicyName: kinesis_data_stream_policy
      PolicyDocument:
        Version: "2012-10-17"
        Statement:
          -
            Effect: "Allow"
            Action:
              - "firehose:PutRecord"
              - "firehose:PutRecordBatch"
              - "kinesis:DescribeStream"
              - "kinesis:GetShardIterator"
              - "kinesis:GetRecords"
              - "kinesis:ListShards"
            Resource: "*"
          -
            Effect: "Allow"
            Action:
              - "kms:Decrypt"
              - "kms:GenerateDataKey"
            Resource: "*"
      Roles:
        - !Ref KinesisDataStreamRole

  FirehoseDeliveryRole:
    Type: "AWS::IAM::Role"
    Properties:
      Path: '/'
      AssumeRolePolicyDocument:
       Version: "2012-10-17"
       Statement:
         -
          Effect: "Allow"
          Principal:
            Service:
             - "firehose.amazonaws.com"
          Action:
             - "sts:AssumeRole"
          Condition:
             StringEquals:
               "sts:ExternalId": !Ref "AWS::AccountId"

  FirehoseDeliveryPolicy:
   Type: AWS::IAM::Policy
   Properties:
      PolicyName: firehose_delivery_policy
      PolicyDocument:
        Version: 2012-10-17
        Statement:
          - Effect: Allow
            Action:
              - 's3:AbortMultipartUpload'
              - 's3:GetBucketLocation'
              - 's3:GetObject'
              - 's3:ListBucket'
              - 's3:ListBucketMultipartUploads'
              - 's3:PutObject'
            Resource:
              - !Sub 'arn:aws:s3:::${MyS3Bucket}'
              - !Sub 'arn:aws:s3:::${MyS3Bucket}*'
      Roles:
        - !Ref FirehoseDeliveryRole
  MyKDS:
    Type: "AWS::Kinesis::Stream"
    Properties:
      Name: !Ref KinesisDataStream
      ShardCount: 1
      RetentionPeriodHours: 24
      StreamEncryption:
        EncryptionType: "KMS"
        KeyId: "alias/aws/kinesis"
      Tags:
        - Key: Name
          Value: !Ref KinesisDataStream
        - Key: Department
          Value: !Ref Department
        - Key: Team
          Value: !Ref Team
        - Key: Owner
          Value: !Ref Owner
        
  MyKDFfromDirectPut:
    Type: AWS::KinesisFirehose::DeliveryStream
    DependsOn: MyS3Bucket 
    Properties:
     ExtendedS3DestinationConfiguration:
       BucketARN: !Sub 'arn:aws:s3:::${MyS3Bucket}'
       BufferingHints:
         IntervalInSeconds: 60
         SizeInMBs: 1
       CompressionFormat: UNCOMPRESSED
       Prefix: access-logs-direct-put-tsv/
       RoleARN: !GetAtt FirehoseDeliveryRole.Arn
     DeliveryStreamName: !Sub '${KinesisFirehoseDeliveryStream}-direct-put'
     DeliveryStreamType: 'DirectPut'

  MyKDFfromKDS:
    Type: AWS::KinesisFirehose::DeliveryStream
    DependsOn: MyS3Bucket 
    Properties:
     KinesisStreamSourceConfiguration: 
      KinesisStreamARN: !GetAtt MyKDS.Arn
      RoleARN: !GetAtt KinesisDataStreamRole.Arn
     ExtendedS3DestinationConfiguration:
       BucketARN: !Sub 'arn:aws:s3:::${MyS3Bucket}'
       BufferingHints:
         IntervalInSeconds: 60
         SizeInMBs: 1
       CompressionFormat: UNCOMPRESSED
       Prefix: access-logs-data-stream-tsv/
       RoleARN: !GetAtt FirehoseDeliveryRole.Arn
     DeliveryStreamName: !Sub '${KinesisFirehoseDeliveryStream}-data-stream'
     DeliveryStreamType: 'KinesisStreamAsSource'

Outputs:
   KinesisFirehoseDeliveryStreamDirectPut:
     Description: Kinesis Firehose DeliveryStream Name for Direct Put
     Value: !Sub '${KinesisFirehoseDeliveryStream}-direct-put'
   KinesisFirehoseDeliveryStreamDataStream:
     Description: Kinesis Firehose DeliveryStream Name for DataStream
     Value: !Sub '${KinesisFirehoseDeliveryStream}-data-stream'
   KinesisDataStream:
     Description: Kinesis Data Stream Name
     Value: !Ref KinesisDataStream
   S3bucketname: 
     Value: !Ref MyS3Bucket
     Description: Name of the bucket created
```

这是一个AWS CloudFormation模板，用于创建和配置Kinesis数据流、Firehose投递流、S3桶及相关IAM角色和策略。

### 参数(Parameters): 这部分定义了一些模板的输入参数，如Kinesis Firehose的名称、Kinesis数据流的名称、部门、团队和所有者。

### 资源(Resources):    

#### S3桶：

定义了两个S3桶 
  - MyS3Bucket作为Kinesis Data Firehose传输的目的地    
  - MyAthenaResultBucket作为存放Athena查询结果的位置    
IAM角色和策略：

为Kinesis数据流和Firehose创建了相关的IAM角色和策略，以便于访问和管理相关的资源。

Kinesis数据流(MyKDS)：

创建一个Kinesis数据流，并为其配置了加密方式、分片数量等属性。

Firehose投递流：

定义了两个Firehose投递流 
- MyKDFfromDirectPut 用于直接存放数据到S3
- MyKDFfromKDS 用于从Kinesis数据流获取数据

输出(Outputs): 

这部分定义了模板的输出，包括
- 两个Firehose投递流的名称
- 一个Kinesis数据流的名称
- 两个S3桶的名称。
