---
layout: post
title: Pandas 脚本的单元测试
subtitle:
date: 2016-11-05
author: "Scott"
header-img:
tags:
  - Python
  - pandas
---

## 什么是单元测试?
## 为什么要单元测试？
## 如何测试？测试的关键点是什么？

拿 upload 这个脚本来说，这个脚本是我给公司写的一个产品上传脚本，上传分两部分，goods 数据和 goods_product 数据，分别上传至远程阿里云 MySQL 的产品表和规格表。

架构如下图所示，具体内容因涉及公司机密已被隐藏。

![]()

- 函数部分
  - 输入、输出是否正常，elif、except 是否考虑到各种情形？
- 类部分
  - 主类 DataWrangle(主要提取数据，以及针对 goods 做数据处理)
    - 公开属性是否是自己想要的？
    - 返回的值 type 是否正常？
    - 每个需要 Get 的 Data 是否有 get 完整？
    - get 的列是否正确，是否非空？
    - 由于是产品上传脚本，SQL 里 goods 中必要的列是否都在？
  - 子类
    - BonusWrangle(主要处理)
      - 处理过的 bonus 是否达到标准？
    - DupDecide
      - 是否能真正判断需上传品牌有在数据库里？
      - 是否能真正判断需上传二级分类，有被添加？
      - 是否能真正查重？

## References

- 官方文档

  - [Testing · pandas-dev/pandas Wiki](https://github.com/pandas-dev/pandas/wiki/Testing)
  - [Developers — pandas: Python Data Analysis Library](http://pandas.pydata.org/developers.html#testing)
  - [pandas/testing.py at master · pandas-dev/pandas](https://github.com/pandas-dev/pandas/blob/master/pandas/util/testing.py)
- pandas 例子
  - [pandas-ply/test_methods.py at master · coursera/pandas-ply](https://github.com/coursera/pandas-ply/blob/master/tests/test_methods.py)
- 书籍
  - [Python高手之路 (豆瓣)](https://book.douban.com/subject/26389274/) 第 6 章
  - [Effective Python (豆瓣)](https://book.douban.com/subject/26709315/) 第 56 条
  - [Python标准库 (豆瓣)](https://book.douban.com/subject/10773324/) 第 16 章
  - [Expert Python Programming - Second Edition (豆瓣)](https://book.douban.com/subject/26791781/) Chapter 10
- Stackoverflow

![](http://7xjuve.com1.z0.glb.clouddn.com/161106-upload_script.png?imageView2/2/w/800)
