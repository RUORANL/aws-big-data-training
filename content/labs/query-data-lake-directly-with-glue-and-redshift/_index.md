---
title: "使用Glue和Redshift直接查询数据湖（S3）中的数据"
date: 2023-08-12T20:08:25+08:00
draft: false

menu:
  after:
    weight: 50
    parent: labs
    identifier: query-data-lake-directly-with-glue-and-redshift
---
## 使用Glue和Redshift直接查询数据湖（S3）中的数据

在这个实验中，我们将向您展示如何使用Amazon Redshift查询Amazon S3数据湖中的数据，而无需加载或移动数据。您可以查询Amazon S3中文件的结构化和半结构化数据，而无需将数据复制或移动到Amazon Redshift表中。有关可以使用Redshift Spectrum查询的文件类型的最新指南，请参阅支持的数据格式。

### 使用的数据集

**数据描述**：

纽约市出租车行程数据，包括三家不同出租车公司 - fhv，green和yellow，按年和月计算的出租车行程数量。

**存储位置**：

us-east-1 region - https://s3.console.aws.amazon.com/s3/buckets/redshift-demos?region=us-east-1&prefix=data/NY-Pub/ 

us-west-2 region - https://s3.console.aws.amazon.com/s3/buckets/us-west-2.serverless-analytics?prefix=canonical/NY-Pub/

### 本实验中各个服务的工作原理

![图片描述：工作原理](/lab2-1-dataflow.png)