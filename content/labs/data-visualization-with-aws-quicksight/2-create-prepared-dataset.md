---
title: "实验4-2 - 调整数据集"
date: 2023-08-12T20:08:25+08:00
draft: false

menu:
  after:
    weight: 20
    identifier: lab4-2
    parent: data-visualization-with-aws-quicksight
---

## 实验4-2 - 调整数据集

## 使用以下步骤准备市场营销数据集并创建分析
如果您在Amazon QuickSight中没有看到Web和Social Media Analytics样本数据，您可以下载它：web-and-social-analytics.csv.zip。

### 为市场营销数据集准备并创建分析
1. 在Amazon QuickSight起始页，左侧选择 **数据集**。
![图片描述：创建堆栈](/lab4-1-example-prepared-data-set1.png)

2. 在 **数据集** 页面，点击 **新建数据集**。
![图片描述：创建堆栈](/lab4-1-example-prepared-data-set2.png)

3. 在**从现有数据源**部分的 **创建数据集** 页面，选择 **Web and Social Media Analytics** Amazon S3数据源，然后选择 **编辑数据集**。



4. 对于数据集名称，输入 **市场营销样本** 来替换数据集名称中的 **Web and Social Media Analytics**。
![图片描述：创建堆栈](/lab4-1-example-prepared-data-set3.png)



5. 从数据集中排除一些字段。
   - 在 **字段** 面板中，选择 **Twitter followers cumulative** 和 **Mailing list cumulative** 字段的字段菜单，然后选择 **排除字段**。若要一次选择多个字段，请按住Ctrl键进行选择（Mac上的Command键）。
   ![图片描述：创建堆栈](/lab4-1-example-prepared-data-set4.png)

7. 重命名字段。
   - 在 **数据集预览** 面板中，滚动到 **Website Pageviews** 字段并选择编辑图标。
   ![图片描述：创建堆栈](/lab4-1-example-prepared-data-set5.png)
   
   - 在打开的 **编辑字段** 页面中，对于 **名称**，输入 **网站页面浏览量**，然后点击 **应用**。

8. 添加一个计算字段，该字段将 **Events** 字段中的任何0长度字符串值替换为文本字符串：
   - 在数据准备页面，滚动到 **字段** 面板的顶部，然后点击 **添加计算字段**。
   ![图片描述：创建堆栈](/lab4-1-example-prepared-data-set6.png)
   
   - 在打开的 **添加计算字段** 页面中，对于 **添加名称**，输入 **populated_event**。
   - 在右侧的 **函数** 面板中，双击函数列表中的 **ifelse** 函数。这会将函数添加到计算字段公式中。
   ![图片描述：创建堆栈](/lab4-1-example-prepared-data-set7.png)
   - 通过选择下拉箭头展开 **字段列表** 面板，然后双击 **Events** 字段。这会将字段添加到计算字段公式中。
   ![图片描述：创建堆栈](/lab4-1-example-prepared-data-set8.png)
   - 在公式编辑器中，输入以下所需的其他函数和参数，如下文所加粗显示的部分：`ifelse(strlen({Events})=0, 'Unknown', {Events})`。
     最终的公式应如下：`ifelse(strlen({Events})=0, 'Unknown', {Events})`。
   ![图片描述：创建堆栈](/lab4-1-example-prepared-data-set9.png)
   - 点击 **保存**。新的计算字段被创建，并显示在 **字段** 面板的顶部。
   ![图片描述：创建堆栈](/lab4-1-example-prepared-data-set10.png)
9. 点击 **保存**。

![图片描述：创建堆栈](/lab4-1-example-prepared-data-set11.png)
