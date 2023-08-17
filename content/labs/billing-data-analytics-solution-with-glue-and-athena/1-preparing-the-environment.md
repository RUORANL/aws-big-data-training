---
title: "实验2-1 - 上传测试数据到S3"
date: 2023-08-12T20:08:25+08:00
draft: false

menu:
  after:
    weight: 10
    identifier: lab2-1
    parent: billing-data-analytics-solution-with-glue-and-athena
---

## 实验2-1 - 上传测试数据到S3

### 1. 从S3下载测试数据

[October 2018 Usage](https://big-data-training-cfn-templates.s3.amazonaws.com/Oct2018-WorkshopCUR-00001.snappy.parquet)

[November 2018 Usage](https://big-data-training-cfn-templates.s3.amazonaws.com/Nov2018-WorkshopCUR-00001.snappy.parquet)

[December 2018 Usage](https://big-data-training-cfn-templates.s3.amazonaws.com/Dec2018-WorkshopCUR-00001.snappy.parquet)

### 2. 创建文件夹结构

我们会使用在使用KinesisLab中创建的billing-data-bucket来存放测试数据。

导航到S3控制台并找到对应的存储桶，在存储桶中创建以下目录结构

`(bucket name)/cur/WorkshopCUR/WorkshopCUR/year=2018`

例如,

```sql
billing-data-xxxxxxxxxxxx/cur/WorkshopCUR/WorkshopCUR/year=2018
```

### 3. 上传测试数据

使用S3命令行或者图形界面将三个测试文件上传到对应的目录里。

例如，

将`October 2018 Usage` parquet 文件上传到(bucket name)/cur/WorkshopCUR/WorkshopCUR/year=2018/month=10/

