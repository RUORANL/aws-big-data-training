---
title: "实验3-3 - 加载数据到Redshift"
date: 2023-08-12T20:08:25+08:00
draft: false

menu:
  after:
    weight: 30
    identifier: lab3-3
    parent: redshift-cluster-setup-and-data-ingestion
---

## 实验3-3 - 加载数据到Redshift

### 开始之前
请确认以下实验资源已经部署完成：
- Redshift Serverless Endpoint
- Redshift Provisioned Cluster

我们将在此实验室中使用Amazon Redshift QueryEditorV2。

### 步骤1 - 创建表
Amazon Redshift是一个遵循ANSI SQL的数据仓库。您可以使用熟悉的CREATE TABLE语句来创建表。

以下是表的数据模型。
![图片描述：TPC数据模型](/lab3-1-redshift-qev2-table-design.png)

接下来我们从TPC基准数据模型创建8个表，请复制以下的创建表语句并运行它们。

```sql
DROP TABLE IF EXISTS partsupp;
DROP TABLE IF EXISTS lineitem;
DROP TABLE IF EXISTS supplier;
DROP TABLE IF EXISTS part;
DROP TABLE IF EXISTS orders;
DROP TABLE IF EXISTS customer;
DROP TABLE IF EXISTS nation;
DROP TABLE IF EXISTS region;

CREATE TABLE region (
  R_REGIONKEY bigint NOT NULL,
  R_NAME varchar(25),
  R_COMMENT varchar(152))
diststyle all;

CREATE TABLE nation (
  N_NATIONKEY bigint NOT NULL,
  N_NAME varchar(25),
  N_REGIONKEY bigint,
  N_COMMENT varchar(152))
diststyle all;

create table customer (
  C_CUSTKEY bigint NOT NULL,
  C_NAME varchar(25),
  C_ADDRESS varchar(40),
  C_NATIONKEY bigint,
  C_PHONE varchar(15),
  C_ACCTBAL decimal(18,4),
  C_MKTSEGMENT varchar(10),
  C_COMMENT varchar(117))
diststyle all;

create table orders (
  O_ORDERKEY bigint NOT NULL,
  O_CUSTKEY bigint,
  O_ORDERSTATUS varchar(1),
  O_TOTALPRICE decimal(18,4),
  O_ORDERDATE Date,
  O_ORDERPRIORITY varchar(15),
  O_CLERK varchar(15),
  O_SHIPPRIORITY Integer,
  O_COMMENT varchar(79))
distkey (O_ORDERKEY)
sortkey (O_ORDERDATE);

create table part (
  P_PARTKEY bigint NOT NULL,
  P_NAME varchar(55),
  P_MFGR  varchar(25),
  P_BRAND varchar(10),
  P_TYPE varchar(25),
  P_SIZE integer,
  P_CONTAINER varchar(10),
  P_RETAILPRICE decimal(18,4),
  P_COMMENT varchar(23))
diststyle all;

create table supplier (
  S_SUPPKEY bigint NOT NULL,
  S_NAME varchar(25),
  S_ADDRESS varchar(40),
  S_NATIONKEY bigint,
  S_PHONE varchar(15),
  S_ACCTBAL decimal(18,4),
  S_COMMENT varchar(101))
diststyle all;

create table lineitem (
  L_ORDERKEY bigint NOT NULL,
  L_PARTKEY bigint,
  L_SUPPKEY bigint,
  L_LINENUMBER integer NOT NULL,
  L_QUANTITY decimal(18,4),
  L_EXTENDEDPRICE decimal(18,4),
  L_DISCOUNT decimal(18,4),
  L_TAX decimal(18,4),
  L_RETURNFLAG varchar(1),
  L_LINESTATUS varchar(1),
  L_SHIPDATE date,
  L_COMMITDATE date,
  L_RECEIPTDATE date,
  L_SHIPINSTRUCT varchar(25),
  L_SHIPMODE varchar(10),
  L_COMMENT varchar(44))
distkey (L_ORDERKEY)
sortkey (L_RECEIPTDATE);

create table partsupp (
  PS_PARTKEY bigint NOT NULL,
  PS_SUPPKEY bigint NOT NULL,
  PS_AVAILQTY integer,
  PS_SUPPLYCOST decimal(18,4),
  PS_COMMENT varchar(199))
diststyle even;
```

### 步骤2 - 从S3加载数据
`COPY` 命令能够有效地从Amazon S3加载大量数据到Amazon Redshift中。单个 `COPY` 命令可以从多个文件加载到一个表中。它会自动并行加载您提供的S3位置中所有文件的数据。

如果目标表为空，`COPY` 命令可以基于列数据类型自动为每列执行压缩编码，因此您不必担心为每列选择正确的压缩算法。为确保Redshift执行压缩分析，我们将在`COPY`命令中将 `COMPUPDATE` 参数设置为 `PRESET`。

本次实验中，我们使用AWS提供的示例数据来完成数据加载过程。

复制以下语句以将数据加载到7个表中。

```sql
-- For Redshift Serverless
COPY region FROM 's3://redshift-immersionday-labs/data/region/region.tbl.lzo'
iam_role default
region 'us-west-2' lzop delimiter '|' COMPUPDATE PRESET;

copy customer from 's3://redshift-immersionday-labs/data/customer/customer.tbl.'
iam_role default
region 'us-west-2' lzop delimiter '|' COMPUPDATE PRESET;

copy orders from 's3://redshift-immersionday-labs/data/orders/orders.tbl.'
iam_role default
region 'us-west-2' lzop delimiter '|' COMPUPDATE PRESET;

copy part from 's3://redshift-immersionday-labs/data/part/part.tbl.'
iam_role default
region 'us-west-2' lzop delimiter '|' COMPUPDATE PRESET;

copy supplier from 's3://redshift-immersionday-labs/data/supplier/supplier.json' manifest
iam_role default
region 'us-west-2' lzop delimiter '|' COMPUPDATE PRESET;

copy lineitem from 's3://redshift-immersionday-labs/data/lineitem-part/'
iam_role default
region 'us-west-2' gzip delimiter '|' COMPUPDATE PRESET;

copy partsupp from 's3://redshift-immersionday-labs/data/partsupp/partsupp.tbl.'
iam_role default
region 'us-west-2' lzop delimiter '|' COMPUPDATE PRESET;

-- For Redshift Provisioned
-- 请使用正确的账号ID替换xxxxxxxxxxxx
COPY region FROM 's3://redshift-immersionday-labs/data/region/region.tbl.lzo'
iam_role 'arn:aws:iam::xxxxxxxxxxxx:role/RedshiftServerlessImmersionRole'
region 'us-west-2' lzop delimiter '|' COMPUPDATE PRESET;

copy customer from 's3://redshift-immersionday-labs/data/customer/customer.tbl.'
iam_role 'arn:aws:iam::xxxxxxxxxxxx:role/RedshiftServerlessImmersionRole'
region 'us-west-2' lzop delimiter '|' COMPUPDATE PRESET;

copy orders from 's3://redshift-immersionday-labs/data/orders/orders.tbl.'
iam_role 'arn:aws:iam::xxxxxxxxxxxx:role/RedshiftServerlessImmersionRole'
region 'us-west-2' lzop delimiter '|' COMPUPDATE PRESET;

copy part from 's3://redshift-immersionday-labs/data/part/part.tbl.'
iam_role 'arn:aws:iam::xxxxxxxxxxxx:role/RedshiftServerlessImmersionRole'
region 'us-west-2' lzop delimiter '|' COMPUPDATE PRESET;

copy supplier from 's3://redshift-immersionday-labs/data/supplier/supplier.json' manifest
iam_role 'arn:aws:iam::xxxxxxxxxxxx:role/RedshiftServerlessImmersionRole'
region 'us-west-2' lzop delimiter '|' COMPUPDATE PRESET;

copy lineitem from 's3://redshift-immersionday-labs/data/lineitem-part/'
iam_role 'arn:aws:iam::xxxxxxxxxxxx:role/RedshiftServerlessImmersionRole'
region 'us-west-2' gzip delimiter '|' COMPUPDATE PRESET;

copy partsupp from 's3://redshift-immersionday-labs/data/partsupp/partsupp.tbl.'
iam_role 'arn:aws:iam::xxxxxxxxxxxx:role/RedshiftServerlessImmersionRole'
region 'us-west-2' lzop delimiter '|' COMPUPDATE PRESET;
```

加载数据的预计时间如下。

- REGION (5 行) - 2s
- CUSTOMER (15M 行) – 2m
- ORDERS - (76M 行) - 10s
- PART - (20M 行) - 2m
- SUPPLIER - (1M 行) - 10s
- LINEITEM - (303M 行) - 22s
- PARTSUPPLIER - (80M 行) - 15s

> 注意：从上面的COPY语句中可以得出一些关键点。
> - COMPUPDATE PRESET ON 将使用与列的数据类型相关的Amazon Redshift最佳实践来分配压缩，但不分析表中的数据。
> - REGION表的COPY指向一个特定文件（region.tbl.lzo），而其他表的COPY指向多个文件的前缀（lineitem.tbl.）。
> - SUPPLIER表的COPY指向一个清单文件（supplier.json）。清单文件是列出要加载的文件及其位置的JSON文件。

### 步骤3 - 数据加载验证
让我们对几个表进行快速的计数检查，以确保数据按预期加载。如果复制过程仍在加载某些表，您可以打开一个新的编辑器并运行以下查询。

```sql
 --行数 = 5
select count(*) from region;

 --行数 = 25
select count(*) from nation;

 --行数 = 76,000,000
select count(*) from orders;
```

### 步骤4 - 其他操作

#### 排查数据加载问题
要排查任何数据加载问题，您可以查询 `SYS_LOAD_ERROR_DETAIL`。

此外，您可以在实际加载表之前验证数据。您可以在COPY命令中使用`NOLOAD`选项，确保在执行实际数据加载之前，您的数据可以在没有任何错误的情况下加载。
> 注意，使用`NOLOAD`选项运行COPY要比加载数据快得多，因为它仅解析文件。

让我们尝试使用列不匹配的不同数据文件加载`CUSTOMER`表。

```sql
COPY customer FROM 's3://redshift-immersionday-labs/data/nation/nation.tbl.'
iam_role default
region 'us-west-2' lzop delimiter '|' noload;
```

您将收到以下错误。

```
ERROR: Load into table 'customer' failed. Check 'sys_load_error_detail' system table for details.
```

查询STL_LOAD_ERROR系统表以获取详细信息。

```
select * from SYS_LOAD_ERROR_DETAIL;
```

请注意，列c_nationkey的数据类型为int的行有一个，错误消息为“Invalid digit, Value 'h', Pos 1”，这表示您正试图将字符加载到整数列中。
