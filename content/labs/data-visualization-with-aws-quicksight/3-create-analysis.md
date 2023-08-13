---
title: "实验4-3 - 创建分析"
date: 2023-08-12T20:08:25+08:00
draft: false

menu:
  after:
    weight: 20
    identifier: lab4-3
    parent: data-visualization-with-aws-quicksight
---

## 实验4-3 - 创建分析

请按照以下步骤创建您的分析。

### 创建分析
1. 在Amazon QuickSight起始页，选择 **新建分析**。

![图片描述：新建分析](/lab4-3-example-create-an-analysis1.png)

2. 在 **数据集** 页面，选择 **Business Review** 样本数据集，然后选择 **创建分析**。

### 使用AutoGraph创建可视化
默认情况下选择AutoGraph来创建一个可视化。

1. 在分析页面中，在 **字段列表** 面板选择 **Date** 和 **Return visitors**。
2. Amazon QuickSight使用这些数据创建一个折线图。

![图片描述：新建折线图](/lab4-3-example-create-an-analysis2.png)

### 创建散点图可视化
选择一个可视化类型并将字段拖到字段窗口以创建可视化。

#### 创建散点图可视化
1. 在分析页面上，选择 **添加**，然后在应用栏上选择 **添加可视化**。这将默认创建一个新的空白可视化，并选择AutoGraph。

  ![图片描述：创建散点图可视化](/lab4-3-example-create-an-analysis3.png)

2. 在 **可视化类型** 面板中，选择散点图图标。

![图片描述：创建散点图可视化](/lab4-3-example-create-an-analysis4.png)

3. 在 **字段列表** 面板中选择字段添加到 **字段窗口** 面板：
   - 选择 **Desktop Uniques** 填充X轴字段窗口。
   - 选择 **Mobile Uniques** 填充Y轴字段窗口。
   - 选择 **Date** 填充 **Group/Color** 字段窗口。

4. 使用这些字段创建一个散点图。
![图片描述：创建散点图可视化](/lab4-3-example-create-an-analysis5.png)