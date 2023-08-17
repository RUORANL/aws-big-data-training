---
title: "实验1-3 - 生成Access Log数据并发送到Kinesis"
date: 2023-08-12T20:08:25+08:00
draft: false
menu:
  after:
    weight: 30
    identifier: lab1-3
    parent: log-analytics-with-kinesis-and-athena
---

# 实验1-3 - 生成Access Log数据并发送到Kinesis

## 概述
在本节中，您将使用Kinesis Data Generator向在前一节中创建的Kinesis Data Stream和Firehose发送模拟的访问日志数据。传输流将将传入的日志条目写入S3，不做任何格式更改。

## 前提条件
已经成功部署实验资源和Kinesis Data Generator。

## 登录到Kinesis Data Generator
Kinesis Data Generator (KDG)已在设置过程中配置到您的帐户上。我们需要检索应用程序的链接和凭据以获得访问权限。通过在AWS控制台顶部的服务搜索框中键入CloudFormation来打开CloudFormation控制台，或点击此链接到CloudFormation控制台。

在堆栈详情中，点击输出选项卡。复制KinesisDataGeneratorUrl、UserName和Password的值列，并为下一步保留它们。

使用上一步中的用户名和密码登录到KDG。选择您的区域，并确认您可以看到您上面创建的Kinesis Firehose传输流和Kinesis数据流。

![图片描述：KDG 登录](/lab2-1-kdg-login.png)

我们将使用参数化模板定义模拟数据的结构。将以下模板复制并粘贴到模板选项卡中：  
```
{{internet.ip}} - {{name.firstName}} [{{date.now("DD/MMM/YYYY:HH:mm:ss ZZ")}}] "{{random.weightedArrayElement({"weights":[0.6,0.1,0.1,0.2],"data":["GET","POST","DELETE","PUT"]})}} {{random.arrayElement(["/list","/wp-content","/wp-admin","/explore","/search/tag/list","/app/main/posts","/posts/posts/explore"])}}" {{random.weightedArrayElement({"weights": [0.9,0.04,0.02,0.04], "data":["200","404","500","301"]})}} {{random.number(10000)}}
```


每条记录都会是随机的。点击测试模板以生成一些记录。

![图片描述：KDG 模板](/lab2-1-kdg-test-data.png)


## 使用Kinesis Data Generator发送数据

点击关闭，然后选择发送数据，从Kinesis Data Generator工具发送实时流数据到Kinesis Firehose - Direct Put。运行发送1000-2000条记录，然后点击停止发送数据到Kinesis。

![图片描述：KDG 发送](/lab2-1-kdg-send-data.png)

点击关闭，然后选择发送数据，从Kinesis Data Generator工具发送实时流数据到Kinesis Data Stream。运行发送1000-2000条记录，然后点击停止发送数据到Kinesis。

![图片描述：KDG 发送](/lab2-1-kdg-send-data.png)


等待2-3分钟，Firehose将数据发送到S3存储桶。

## 导航到 S3 控制台查看结果

![图片描述：浏览 S3](/lab2-1-s3-data.png)

选择在前一节中创建的以access-logs-direct-put-tsv为前缀的S3存储桶。您将在access-logs-direct-put-tsv/yyyy/mm/dd/hh目录结构下看到Direct Put创建的数据。

选择在前一节中创建的以access-logs-data-stream-tsv为前缀的S3存储桶。您将在access-logs-data-stream-tsv/yyyy/mm/dd/hh目录结构下看到通过Data Stream创建的数据。

您现在可以使用SQL查询来分析数据。

