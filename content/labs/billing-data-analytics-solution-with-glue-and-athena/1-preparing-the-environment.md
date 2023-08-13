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

Follow the Amazon Simple Storage Service documentation page to create an S3 bucket
Select your new S3 bucket and click the Create folder button, enter cur for your folder name, and click Create folder
Click on your new cur folder to enter it, and follow the same process to create further folders until you have a folder structure which resembles (bucket name)/cur/WorkshopCUR/WorkshopCUR/year=2018
Then create three further folders within the year=2018 folder (month=10, month=11, month=12)
Download the three parquet files below
October 2018 Usage
November 2018 Usage
December 2018 Usage
Upload each months file into the corresponding folder, (so upload October’s parquet file to (bucket name)/cur/WorkshopCUR/WorkshopCUR/year=2018/month=10/