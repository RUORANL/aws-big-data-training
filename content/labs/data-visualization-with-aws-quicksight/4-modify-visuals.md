---
title: "实验4-4 - 修改可视化组件"
date: 2023-08-12T20:08:25+08:00
draft: false

menu:
  after:
    weight: 40
    identifier: lab4-4
    parent: data-visualization-with-aws-quicksight
---

## 实验4-4 - 修改可视化组件

请按照以下程序修改您使用“教程：创建Amazon QuickSight分析”中的程序创建的可视化。

### 修改折线图可视化
通过使其按日期显示额外的度量（measure）并更改图表颜色来修改折线图可视化。

#### 要修改您的折线图可视化：
1. 在分析中，选择折线图可视化。

2. 向可视化添加另一个度量（measure）。
  在 **字段列表** 面板中选择 **New visitors SEO** 字段。此度量（measure）被添加到**值**字段窗口中，折线图用一条线来表示它进行更新。可视化标题也进行了更新。

![图片描述：修改视觉效果](/lab4-4-example-modify-visuals1.png)

3. 更改用于表示 **Return visitors** 度量的线的颜色。
  选择代表 **Return visitors** 的图表上的线。为此，选择线的末端，而不是线的中间。
  选择 **Color Return visitors**，然后从颜色选择器中选择红色图标。

![图片描述：修改视觉效果](/lab4-4-example-modify-visuals2.gif)

4. 在X轴字段窗口中选择 **Date** 字段，选择 **Aggregate**，然后选择 **Month**。

![图片描述：修改视觉效果](/lab4-4-example-modify-visuals3.png)

### 修改散点图可视化
通过更改数据的粒度来修改散点图可视化。

#### 要修改您的散点图可视化：

1. 在分析中，选择散点图可视化。

2. 选择 **Group/Color** 字段窗口，选择 **Aggregate**，然后选择 **Month**。

![图片描述：修改视觉效果](/lab4-4-example-modify-visuals4.png)

  散点图会更新以按月而不是按默认的按年显示测量值。

![图片描述：修改视觉效果](/lab4-4-example-modify-visuals5.png)

### 通过更改可视化布局和添加过滤器来修改两个可视化
通过更改可视化的大小和位置以及添加过滤器并将其应用于两者来修改两个可视化。

#### 更改可视化布局
通过更改可视化的大小和位置来修改两个可视化。

##### 要修改两个可视化：
1. 在您的分析中，选择折线图可视化。
2. 选择可视化右下角的调整大小手柄并向上和向左拖动，直到可视化在水平和垂直方向上都缩小到原来的一半大小。

![图片描述：修改视觉效果](/lab4-4-example-modify-visuals6.gif)

3. 重复此步骤对散点图可视化操作。
4. 选择散点图可视化上的移动手柄，并将其拖动到折线图可视化的右侧，使它们并排显示。

![图片描述：修改视觉效果](/lab4-4-example-modify-visuals7.gif)

#### 通过添加过滤器来修改两个可视化
通过添加过滤器并将其应用于两者来修改两个可视化。

##### 要将过滤器添加到两个可视化中：
1. 在分析中，选择散点图可视化。
2. 在左侧选择 **Filter**。
3. 在 **Filters** 面板上，选择加号图标，然后选择要进行过滤的 **Date** 字段。

![图片描述：修改视觉效果](/lab4-4-example-modify-visuals8.png)

4. 选择新过滤器以展开它。

![图片描述：修改视觉效果](/lab4-4-example-modify-visuals9.png)

5. 在 **Edit filter** 面板中，对于 **Filter type**，选择 **After** 比较类型。

![图片描述：修改视觉效果](/lab4-4-example-modify-visuals10.png)

6. 输入一个开始日期值为1/1/2014。
  选择 **Date**，为年份选择2014，为月份选择January，然后在日历上选择1。

![图片描述：修改视觉效果](/lab4-4-example-modify-visuals11.png)

7. 在 **Edit filter** 面板中，选择 **Apply** 将过滤器应用于可视化。
   - 过滤器被应用于散点图可视化。这是在可视化下拉菜单上用一个过滤器图标表示的。

![图片描述：修改视觉效果](/lab4-4-example-modify-visuals12.png)

8. 将过滤器应用于折线图可视化。
  在左侧的 **Filter** 面板中，再次选择 **Date** 过滤器，选择 **Only this visual**，然后选择 **All visuals of this dataset**。
  过滤器也应用于折线图可视化。

![图片描述：修改视觉效果](/lab4-4-example-modify-visuals13.png)


