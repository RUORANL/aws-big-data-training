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
{{< button href="https://console.aws.amazon.com/cloudformation/home?#/stacks/new?stackName=KinesisLab&templateURL=https://big-data-training-cfn-templates.s3.amazonaws.com/SetupLabRedshift.yaml" >}}启动堆栈{{< /button >}}    

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



