---
title: "实验3-1 - 部署Redshift集群"
date: 2023-08-12T20:08:25+08:00
draft: false

menu:
  after:
    weight: 10
    identifier: lab3-1
    parent: redshift-cluster-setup-and-data-ingestion
---

## 实验3-1 - 部署Redshift集群

### 步骤1 - 创建堆栈
点击下方的“启动堆栈”以启动Cloudformation堆栈创建，然后如下图所示点击“下一步”。  
{{< button href="https://console.aws.amazon.com/cloudformation/home?#/stacks/new?stackName=RedshiftLab&templateURL=https://big-data-training-cfn-templates.s3.amazonaws.com/SetupLabRedshift.yaml" >}}启动堆栈{{< /button >}}    

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

### 步骤2 - 查看堆栈输出信息
一旦堆栈创建完成，请转到“输出”选项卡并记下由Cloudformation创建的Firehose Delivery和S3 bucket资源。

![图片描述：输出信息](/lab2-1-output-tab.png)



## 模板介绍
```yaml
AWSTemplateFormatVersion: 2010-09-09
Parameters:
  DatabaseName:
    Description: The name of the first database to be created when the cluster is created
    Type: String
    Default: dev
    AllowedPattern: '([a-z]|[0-9])+'
  AdminUsername:
    Description: >-
      The user name that is associated with the admin user account for the
      cluster that is being created
    Type: String
    Default: awsuser
    AllowedPattern: '([a-z])([a-z]|[0-9])*'
  AdminPassword:
    Description: >-
      The password that is associated with the admin user account for the
      cluster that is being created. Default is Awsuser123
    Type: String
    Default: Awsuser123
    NoEcho: 'true'
    MinLength: 8
    MaxLength: 64
    AllowedPattern: >-
      ^(?=.*[a-z])(?=.*[A-Z])(?=.*\d)[^\x00-\x20\x22\x27\x2f\x40\x5c\x7f-\uffff]+
  BaseRPU:
    Description: Base RPU for Redshift Serverless Workgroup.
    Type: Number
    MinValue: 32
    MaxValue: 512
    Default: 32
    AllowedValues:
      - 32
      - 40
      - 48
      - 56
      - 64
      - 72
      - 80
      - 88
      - 96
      - 104
      - 112
      - 120
      - 128
      - 136
      - 144
      - 152
      - 160
      - 168
      - 176
      - 184
      - 192
      - 200
      - 208
      - 216
      - 224
      - 232
      - 240
      - 248
      - 256
      - 264
      - 272
      - 280
      - 288
      - 296
      - 304
      - 312
      - 320
      - 328
      - 336
      - 344
      - 352
      - 360
      - 368
      - 376
      - 384
      - 392
      - 400
      - 408
      - 416
      - 424
      - 432
      - 440
      - 448
      - 456
      - 464
      - 472
      - 480
      - 488
      - 496
      - 504
      - 512
  PortNumber:
    Description: >-
      The port number on which the data sharing consumer cluster accepts
      incoming connections.
    Type: Number
    Default: '5439'
Metadata:
  'AWS::CloudFormation::Interface':
    ParameterGroups:
      - Label:
          default: Connection Details
        Parameters:
          - DatabaseName
          - AdminUsername
          - AdminPassword
          - BaseRPU
          - PortNumber
Resources:
  RedshiftRole:
    Type: 'AWS::IAM::Role'
    Properties:
      RoleName: RedshiftServerlessImmersionRole
      AssumeRolePolicyDocument:
        Version: 2012-10-17
        Statement:
          - Effect: Allow
            Principal:
              Service:
                - redshift.amazonaws.com
            Action:
              - 'sts:AssumeRole'
          - Effect: Allow
            Principal:
              Service:
                - redshift-serverless.amazonaws.com
            Action:
              - 'sts:AssumeRole'
          - Effect: Allow
            Principal:
              Service:
                - sagemaker.amazonaws.com
            Action:
              - 'sts:AssumeRole'
          - Effect: Allow
            Principal:
              Service:
                - events.amazonaws.com
            Action:
              - 'sts:AssumeRole'
          - Effect: Allow
            Principal:
              Service:
                - scheduler.redshift.amazonaws.com
            Action:
              - 'sts:AssumeRole'
      Path: /
      ManagedPolicyArns:
        - 'arn:aws:iam::aws:policy/AmazonS3FullAccess'
        - 'arn:aws:iam::aws:policy/AWSGlueConsoleFullAccess'
        - 'arn:aws:iam::aws:policy/AmazonRedshiftFullAccess'
        - 'arn:aws:iam::aws:policy/AmazonSageMakerFullAccess'
        - 'arn:aws:iam::aws:policy/AmazonKinesisFullAccess'
  VPC:
    Type: 'AWS::EC2::VPC'
    Properties:
      CidrBlock: 10.0.0.0/16
      EnableDnsHostnames: true
      EnableDnsSupport: true
  Subnet1:
    Type: 'AWS::EC2::Subnet'
    Properties:
      MapPublicIpOnLaunch: true
      CidrBlock: 10.0.0.0/24
      AvailabilityZone: !Select 
        - 0
        - !GetAZs ''
      VpcId:
        Ref: VPC
  Subnet2:
    Type: 'AWS::EC2::Subnet'
    Properties:
      MapPublicIpOnLaunch: true
      CidrBlock: 10.0.1.0/24
      AvailabilityZone: !Select 
        - 1
        - !GetAZs ''
      VpcId:
        Ref: VPC
  Subnet3:
    Type: 'AWS::EC2::Subnet'
    Properties:
      MapPublicIpOnLaunch: true
      CidrBlock: 10.0.2.0/24
      AvailabilityZone: !Select 
        - 2
        - !GetAZs ''
      VpcId:
        Ref: VPC
  Subnet4:
    Type: 'AWS::EC2::Subnet'
    Properties:
      MapPublicIpOnLaunch: true
      CidrBlock: 10.0.3.0/24
      AvailabilityZone: !Select 
        - 3
        - !GetAZs ''
      VpcId:
        Ref: VPC
  SecurityGroup:
    Type: 'AWS::EC2::SecurityGroup'
    Properties:
      GroupDescription: Security group
      VpcId:
        Ref: VPC
  SecurityGroupIngress:
    Type: 'AWS::EC2::SecurityGroupIngress'
    Properties:
      GroupId: !GetAtt 
        - SecurityGroup
        - GroupId
      IpProtocol: tcp
      FromPort: 5439
      ToPort: 5439
      CidrIp: 52.23.63.224/27
  SecurityGroupSelfReference:
    Type: 'AWS::EC2::SecurityGroupIngress'
    Properties:
      Description: Self Referencing Rule
      FromPort: -1
      IpProtocol: -1
      GroupId: !GetAtt 
        - SecurityGroup
        - GroupId
      SourceSecurityGroupId: !GetAtt 
        - SecurityGroup
        - GroupId
      ToPort: -1
  InternetGateway:
    Type: 'AWS::EC2::InternetGateway'
  AttachGateway:
    Type: 'AWS::EC2::VPCGatewayAttachment'
    Properties:
      VpcId:
        Ref: VPC
      InternetGatewayId:
        Ref: InternetGateway
  RouteTable:
    Type: 'AWS::EC2::RouteTable'
    Properties:
      VpcId:
        Ref: VPC
  Route:
    Type: 'AWS::EC2::Route'
    DependsOn: AttachGateway
    Properties:
      RouteTableId:
        Ref: RouteTable
      DestinationCidrBlock: 0.0.0.0/0
      GatewayId:
        Ref: InternetGateway
  SubnetRouteTableAssociation:
    Type: 'AWS::EC2::SubnetRouteTableAssociation'
    Properties:
      SubnetId:
        Ref: Subnet1
      RouteTableId:
        Ref: RouteTable
  SubnetRouteTableAssociation2:
    Type: 'AWS::EC2::SubnetRouteTableAssociation'
    Properties:
      SubnetId:
        Ref: Subnet2
      RouteTableId:
        Ref: RouteTable
  SubnetRouteTableAssociation3:
    Type: 'AWS::EC2::SubnetRouteTableAssociation'
    Properties:
      SubnetId:
        Ref: Subnet3
      RouteTableId:
        Ref: RouteTable
  SubnetRouteTableAssociation4:
    Type: 'AWS::EC2::SubnetRouteTableAssociation'
    Properties:
      SubnetId:
        Ref: Subnet4
      RouteTableId:
        Ref: RouteTable
  RedshiftServerlessNamespace:
    Type: 'AWS::RedshiftServerless::Namespace'
    Properties:
      AdminUsername:
        Ref: AdminUsername
      AdminUserPassword:
        Ref: AdminPassword
      DbName:
        Ref: DatabaseName
      NamespaceName: !Sub 'namespace-${AWS::AccountId}'
      IamRoles:
        - !GetAtt RedshiftRole.Arn
      DefaultIamRoleArn: !GetAtt RedshiftRole.Arn
  RedshiftServerlessWorkgroup:
    Type: 'AWS::RedshiftServerless::Workgroup'
    Properties:
      WorkgroupName: !Sub 'workgroup-${AWS::AccountId}'
      NamespaceName: !Sub 'namespace-${AWS::AccountId}'
      BaseCapacity:
        Ref: BaseRPU
      PubliclyAccessible: 'false'
      SubnetIds:
        - Ref: Subnet1
        - Ref: Subnet2
        - Ref: Subnet3
        - Ref: Subnet4
      SecurityGroupIds:
        - Ref: SecurityGroup
    DependsOn:
      - RedshiftServerlessNamespace
  RedshiftProvisionedSubnetGroup:
    Type: 'AWS::Redshift::ClusterSubnetGroup'
    Properties:
      Description: RedshiftProvisionedSubnetGroup
      SubnetIds:
        - Ref: Subnet1
        - Ref: Subnet2
        - Ref: Subnet3
        - Ref: Subnet4
    UpdateReplacePolicy: Delete
    DeletionPolicy: Delete
  RedshiftProvisioned:
    Type: 'AWS::Redshift::Cluster'
    Properties:
      ClusterType: single-node
      DBName: !Ref DatabaseName
      MasterUsername: !Ref AdminUsername
      MasterUserPassword: !Ref AdminPassword
      NodeType: dc2.large
      AllowVersionUpgrade: true
      AutomatedSnapshotRetentionPeriod: 1
      ClusterIdentifier: redshift-provisioned
      ClusterSubnetGroupName:
        Ref: RedshiftProvisionedSubnetGroup
      IamRoles: !GetAtt RedshiftRole.Arn
      Encrypted: true
      PubliclyAccessible: true
      VpcSecurityGroupIds:
        - 'Fn::GetAtt':
            - SecurityGroup
            - GroupId
    UpdateReplacePolicy: Delete
    DeletionPolicy: Delete
    DependsOn: AttachGateway
  S3Bucket:
    Type: 'AWS::S3::Bucket'
    Properties:
      BucketName: !Sub 'csv-file-store-${AWS::AccountId}'
Outputs:
  NamespaceName:
    Description: Namespace Name
    Value:
      Ref: RedshiftServerlessNamespace
  Workgroupname:
    Description: Workgroup Name
    Value:
      Ref: RedshiftServerlessWorkgroup
  AdminUsername:
    Description: Username to login to your Redshift cluster
    Value:
      Ref: AdminUsername
  AdminPassword:
    Description: Username to login to your Redshift cluster
    Value:
      Ref: AdminPassword
  VPC:
    Description: VPC ID for this configuration
    Value:
      Ref: VPC
  PortNumber:
    Description: Redshift port number for connections and security
    Value:
      Ref: PortNumber
  RedshiftRoleName:
    Description: Name of Redshift Role
    Value:
      Ref: RedshiftRole
  RedshiftRoleNameArn:
    Description: Name of Redshift Role with ARN FN tag
    Value:
      'Fn::GetAtt':
        - RedshiftRole
        - Arn
  RedshiftClusterSecurityGroupName:
    Description: Name of security group
    Value:
      Ref: SecurityGroup
  S3Bucket:
    Description: S3 bucket name
    Value:
      Ref: S3Bucket
  RedshiftServerlessEndpoint:
    Description: Redshift Serverless endpoint
    Value:
      'Fn::Join':
        - ':'
        - - 'Fn::GetAtt':
              - RedshiftServerlessWorkgroup
              - Workgroup.Endpoint.Address
          - '5439'
```
