---
title: "实验5-2 - 创建外部Schema并查询Glue Data Catalog中的表"
date: 2023-08-12T20:08:25+08:00
draft: false

menu:
  after:
    weight: 20
    identifier: lab5-2
    parent: query-data-lake-directly-with-glue-and-redshift
---

## 实验5-2 - 创建外部Schema并查询Glue Data Catalog中的表

### 使用Query Editor V2

点击控制台左侧的Serverless仪表板菜单项。点击先前配置的名称空间。点击查询数据。

  ![图片描述：查询结果](/lab5-2-1.png)

### 创建外部Schema

在Glue目录数据库spectrumdb中创建指向adb305的外部Schema。

```sql
CREATE external SCHEMA adb305
FROM data catalog DATABASE 'spectrumdb'
IAM_ROLE default
CREATE external DATABASE if not exists;
```

### 对数据表进行查询，确定暴风雪的日期

您可以从Redshift外部模式中查询在Glue目录中定义的表ny_pub。在2016年1月，由于暴风雪，某一天的出租车行程数量最少。你能找到那一天吗？

```sql
SELECT TO_CHAR(pickup_datetime, 'YYYY-MM-DD'),COUNT(*)
FROM adb305.ny_pub
WHERE YEAR = 2016 and Month = 01
GROUP BY 1
ORDER BY 2;
```

  ![图片描述：查询结果](/lab5-2-2.png)

### 创建内部Schema `workshop_das`

为将存放在Redshift托管存储上的表创建一个Schema `workshop_das`。

```sql
CREATE SCHEMA workshop_das;
```

### 通过CTAS创建内部表

通过从外部表选择来运行CTAS以创建并加载Redshift表workshop_das.taxi_201601
为2016年1月的绿色出租车公司创建表workshop_das.taxi_201601以加载数据。

```sql
CREATE TABLE workshop_das.taxi_201601 AS
SELECT *
FROM adb305.ny_pub
WHERE year = 2016 AND month = 1 AND type = 'green';
```

> 注意: 关于列压缩/编码呢？记住，在CTAS上，Amazon Redshift会自动分配压缩编码如下：
> - 定义为排序键的列被分配RAW压缩。
> - 定义为BOOLEAN、REAL、DOUBLE PRECISION或GEOMETRY数据类型的列被分配RAW压缩。
> - 定义为SMALLINT、INTEGER、BIGINT、DECIMAL、DATE、TIMESTAMP或TIMESTAMPTZ的列被分配AZ64压缩。
> - 定义为CHAR或VARCHAR的列被分配LZO压缩。

```sql
ANALYZE COMPRESSION workshop_das.taxi_201601;
```

  ![图片描述：查询结果](/lab5-2-3.png)

### 向内部表中继续添加数据

为其他出租车公司使用INSERT/SELECT语句向taxi_201601表添加数据。

```sql
INSERT INTO workshop_das.taxi_201601 (
SELECT *
FROM adb305.ny_pub
WHERE year = 2016 AND month = 1 AND type != 'green');

```