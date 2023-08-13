---
title: "实验3-2 - 配置客户端工具"
date: 2023-08-12T20:08:25+08:00
draft: false

menu:
  after:
    weight: 20
    identifier: lab3-2
    parent: redshift-cluster-setup-and-data-ingestion
---

## 实验3-2 - 配置客户端工具 - Query Editor V2

### 步骤1 - 配置查询编辑器V2
- 一旦您获得了具有所需资源的账户访问权限，让我们验证您是否可以连接到Redshift环境。这些实验室使用基于网络的Redshift **Query Editor v2**。请[导航到 Query editor v2](链接地址)。

- 如果遇到如下提示，您可能需要配置Query Editor V2。

  ![图片描述：配置查询编辑器V2](/lab3-1-configure-qev2.png)

- 在左侧，点击您想要连接的Redshift环境。

  一个弹出窗口应该已经打开。尝试选择Serverless端点（workgroup-xxxxxxxxxxxx）或Provisioned集群（redshift-provisioned-xxxxxxxxxxxx。
  ![图片描述：连接Serverless Endpoint](/lab3-1-redshift-qev2-connection-serverless.png)

  ![图片描述：连接Serverless Endpoint](/lab3-1-redshift-qev2-connection-provisioned.png)

  输入数据库名称和用户名。点击**连接**。

  **用户名:** awsuser  
  **密码:** Awsuser123

### 步骤2 - 运行示例查询

- 运行以下查询，列出redshift集群中的用户。

  ```sql
  select * from pg_user;
  ```

  ![图片描述：测试连接](/lab3-1-redshift-qev2-test-connection.png)






