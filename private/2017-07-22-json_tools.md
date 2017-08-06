---
layout: post
title: Json 神器
subtitle:   
date: 2017-07-22
author:     "Scott"
header-img: 
tags:
    - 工具箱
---

## jq

### Json 数据探索

查看第一个 array 的 keys

```
jq -r '.[0]|keys' all_articles.txt
```

转成 csv 并按列输出
```
jq -r '.[0]|keys|@csv' all_articles.txt | tr ',' '\n'
```

### 提取数据

提取第一层级所有 array 下的 content 字段

```
jq -r '.[]|.content' all_articles.txt > new_weixin_content.txt
```

### 其他参考

* [jq Manual (development version)](https://stedolan.github.io/jq/manual/#Invokingjq) 官方文档。
* [Querying Json Datasets With Jq - Mutable Ideas](http://arjon.es/2014/11/18/querying-json-datasets-with-jq/)
* [json - Select objects based on value of variable in object using jq - Stack Overflow](https://stackoverflow.com/questions/18592173/select-objects-based-on-value-of-variable-in-object-using-jq)
* [Bash that JSON (with jq) — Librato Blog](http://blog.librato.com/posts/jq-json)
* [jq : Linux下json的命令行工具-Bean_lee-ChinaUnix博客](http://blog.chinaunix.net/uid-24774106-id-3830242.html)
* [命令行 JSON 处理工具 jq 的使用介绍](https://www.ibm.com/developerworks/cn/linux/1612_chengg_jq/index.html)

