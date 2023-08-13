---
title: "实验5-1 - 创建并运行Glue爬虫以填充Glue数据目录"
date: 2023-08-12T20:08:25+08:00
draft: false

menu:
  after:
    weight: 10
    identifier: lab5-1
    parent: query-data-lake-directly-with-glue-and-redshift
---

## 实验5-1 - 创建并运行Glue爬虫以填充Glue数据目录
在此实验室部分，我们将执行以下活动：

- 使用Redshift Spectrum为S3上的历史数据创建外部DB。
- 内省历史数据，可能以新颖的方式汇总数据，以查看随着时间或其他维度的趋势。
  
> 注意分区方案是年、月、类型（其中类型是出租车公司）。这是一个[截图](https://s3.console.aws.amazon.com/s3/buckets/us-west-2.serverless-analytics/canonical/NY-Pub/ )。

![图片描述：分区信息](/lab5-1-partitions.png)

## 创建Redshift Spectrum的外部Schema和Database
您可以在Amazon Redshift、AWS Glue、Amazon Athena或Apache Hive元数据存储中创建一个外部表。如果您的外部表在AWS Glue、Athena或Hive元数据存储中定义，您首先创建一个引用外部数据库的外部模式。然后，您可以通过在表名前加上模式名来在SELECT语句中引用外部表，而不需要在Amazon Redshift中创建表。

在此实验室中，您将使用AWS Glue Crawler在s3://us-west-2.serverless-analytics/canonical/NY-Pub/ 下创建存储在parquet格式中的外部表adb305.ny_pub。

1. 导航到Glue爬虫页面。
  - us-east-1地区 - [链接](https://us-east-1.console.aws.amazon.com/glue/home) 

  - us-west-2地区 - [链接](https://us-west-2.console.aws.amazon.com/glue/home?region=us-west-2)

  ![图片描述：分区信息](/lab5-1-gluecrawler.png)

2. 点击**创建爬虫**，输入爬虫名NYTaxiCrawler并点击**下一步**。

  ![图片描述：分区信息](/lab5-1-createcrawler.png)

3. 点击**添加数据源**。

  ![图片描述：分区信息](/lab5-1-datasource.png)

4. 选择S3作为数据存储，选择**在另一个账户中**并输入S3文件路径s3://redshift-demos/data/NY-Pub(对于us-east-1)和s3://us-west-2.serverless-analytics/canonical/NY-Pub(对于us-west-2)。点击**添加S3数据源**。
  
  ![图片描述：分区信息](/lab5-1-s3datasource.png)

5. 为爬虫选择S3作为数据源并点击**下一步**。

  ![图片描述：分区信息](/lab5-1-s3datasourcecrawl.png)


6. 点击**创建新的IAM角色**并点击**下一步**。

  ![图片描述：分区信息](/lab5-1-iamrole.png)

7. 输入AWSGlueServiceRole-RedshiftImmersion并点击**创建**。

  ![图片描述：分区信息](/lab5-1-createiam.png)

8. 点击**添加数据库**并输入名字spectrumdb。

  ![图片描述：分区信息](/lab5-1-adddb.png)


9. 返回到Glue控制台，刷新目标数据库并选择spectrumdb。

  ![图片描述：分区信息](/lab5-1-spectrumdb.png)

10. 选择所有剩余的默认值并点击**创建爬虫**。选择爬虫-NYTaxiCrawler并点击**运行**。

  ![图片描述：分区信息](/lab5-1-runcrawler.png)


11. 完成爬虫运行后，您可以在Glue目录中看到一个新表ny_pub。
  ![图片描述：分区信息](/lab5-1-nypubtable.png)
