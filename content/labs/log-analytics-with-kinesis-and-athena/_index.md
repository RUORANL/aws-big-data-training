---
title: "使用Kinesis和Athena进行日志分析"
date: 2023-08-12T20:08:25+08:00
draft: false

menu:
  after:
    weight: 10
    parent: labs
    identifier: log-analytics-with-kinesis-and-athena
---
## 使用Kinesis和Athena进行日志分析

### 概述
日志分析是一种允许您分析来自网站、移动设备、服务器、传感器等的日志数据的用例，应用范围广泛，包括安全事件监控、数字营销、应用监控、欺诈检测、广告技术、游戏和物联网。在此实验中，您将学习如何使用Amazon Kinesis Data Firehose将Apache日志摄取并传送到Amazon S3，而无需管理任何基础设施。然后，您可以使用Amazon Athena查询日志文件，了解访问模式和网站性能问题。

### 架构
![图片描述：架构](/lab2-1-architecture.png)

### 涉及的服务

#### Amazon Kinesis Data Firehose
Amazon Kinesis Data Firehose是将流数据可靠地加载到数据湖、数据存储和分析服务中的最简单方法。它可以捕获、转换并将流数据传输到Amazon S3、Amazon Redshift、Amazon OpenSearch Service（Amazon Elasticsearch Service的后续产品）、通用HTTP端点以及Datadog、New Relic、MongoDB和Splunk等服务提供商。这是一个完全托管的服务，可以自动扩展以匹配您数据的吞吐量，并且不需要持续的管理。它还可以在加载之前对您的数据流进行批处理、压缩、转换和加密，从而最小化存储使用量并增加安全性。

#### Amazon Kinesis Data Stream
Amazon Kinesis Data Stream是一种强大的实时数据流处理服务。它可以持续捕获、存储和处理大量数据流，如日志文件、社交媒体、市场数据喂养等。Kinesis Data Stream可以处理任何量级的流数据，并且可以从源实时处理数据，这对于实时分析、实时仪表板、IoT设备数据处理等场景非常有用。此外，Kinesis Data Stream还提供了强大的安全性、可用性和耐久性保证，使其成为处理实时大数据的理想选择。

#### Amazon S3
Amazon S3（Amazon Simple Storage Service）是一个对象存储服务，提供业界领先的可扩展性、数据可用性、安全性和性能。这意味着所有规模和行业的客户都可以使用它存储和保护任何数量的数据，用于各种用例，如数据湖、网站、云原生应用、备份、归档、机器学习和分析。Amazon S3设计为99.999999999%（11个9）的耐久性，并为全球数百万客户存储数据。

#### Amazon Athena
Amazon Athena是一个交互式查询服务，使您可以轻松地使用标准SQL分析Amazon S3中的数据。Athena是无服务器的，因此没有基础设施可供管理，您只需为运行的查询付费。使用Athena非常简单。只需指向Amazon S3中的数据，定义模式，然后开始使用标准SQL进行查询。大多数结果在几秒钟内提供。使用Athena，您不需要复杂的ETL作业来准备分析数据。这使得任何具有SQL技能的人都可以快速分析大规模数据集。

