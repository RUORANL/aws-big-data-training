---
title: "实验1-4 - 使用Amazon Athena分析数据"
date: 2023-08-12T20:08:25+08:00
draft: false
menu:
  after:
    weight: 40
    identifier: lab1-4
    parent: log-analytics-with-kinesis-and-athena
---

# 实验1-4 - 使用Amazon Athena分析数据

## 概述
Amazon Athena是一个交互式查询服务，可以轻松地使用标准SQL直接在Amazon Simple Storage Service (Amazon S3)中分析数据。在AWS管理控制台中进行几个操作，您就可以将Athena指向存储在Amazon S3中的数据，并开始使用标准SQL运行临时查询，并在几秒钟内获得结果。在这个模块中，我们将在访问日志数据上创建Amazon Athena表。

## Amazon Athena设置
在使用Amazon Athena查询表之前，您需要在Amazon S3中配置查询结果位置。

- 打开Athena控制台：[Athena Console](链接地址)



- 选择"Explore the query editor"以打开查询编辑器。

  ![图片描述：Athena 设置1](/lab2-1-athena-settings-1.png)

- 选择“Edit Settings”以在Amazon S3中设置查询结果位置。
  
  ![图片描述：Athena 设置2](/lab2-1-athena-settings-2.png)


- 在“Settings”选项卡上，选择“Manage”。
  
  ![图片描述：Athena 设置3](/lab2-1-athena-manage.png)


- 在“Manage Settings”屏幕上，选择“Browse S3”，选择您创建的Amazon S3桶（例如：access-logs-tsv-{aws_account-id}，然后点击“Choose”，将"/athena-results"作为桶的文件夹名添加，然后点击“save”。
  
  ![图片描述：Athena 设置4](/lab2-1-athena-settings-3.png)



## 创建Amazon Athena表
- 切换到[Athena Query Editor](链接地址)。

- 复制以下的创建表语句，并粘贴到查询编辑器中。用您的S3桶名称（例如：access-logs-tsv-xxxxxxxxxxxxx）替换bucket-name，然后点击“Run”以创建表。

  **注意**：使用在本实验室环境准备部分创建的S3桶名称替换bucket-name。

  ```sql
  CREATE EXTERNAL TABLE apache_logs(
  client_ip string,
  client_id string,
  user_id string,
  request_received_time string,
  client_request string,
  server_status string,
  returned_obj_size string
  )
  ROW FORMAT SERDE
    'com.amazonaws.glue.serde.GrokSerDe'
  WITH SERDEPROPERTIES (
    'input.format'='^%{IPV4:client_ip} %{DATA:client_id} %{USERNAME:user_id} %{GREEDYDATA:request_received_time} %{QUOTEDSTRING:client_request} %{DATA:server_status} %{DATA: returned_obj_size}$'
    )
  STORED AS INPUTFORMAT
    'org.apache.hadoop.mapred.TextInputFormat'
  OUTPUTFORMAT
    'org.apache.hadoop.hive.ql.io.HiveIgnoreKeyTextOutputFormat'
  LOCATION
    's3://access-logs-592336536196/access-logs-data-stream-tsv/';
   ```


  ![图片描述：Athena 设置2](/lab2-1-create-tsv-table.png)

  您将在左侧面板上看到apache_logs表

  ![图片描述：Athena 设置2](/lab2-1-table-success.png)


## 查询apache_logs表
- 将以下查询复制到查询编辑器中，并点击“Run”执行查询。

  ```sql
  Copy code
  SELECT * FROM "default"."apache_logs" limit 10;
  ```


- 您将在结果面板中看到查询结果。
  ![图片描述：Athena 设置2](/lab2-1-results.png)

恭喜！您已经分析了原始的Apache访问日志数据。