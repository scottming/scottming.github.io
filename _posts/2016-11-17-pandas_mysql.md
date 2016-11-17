---
layout: post
title: Pandas 和 MySQL，一对好基友
subtitle:   
date: 2016-11-17
author:     "Scott"
header-img: 
tags:
    - Python
    - MySQL
---


## 背景
公司服务器托管在阿里云，系统为 CentOS，所有商品数据在其 MySQL 数据库中。

现在需要一个批量/自动化脚本能把采购经理整理的 Excel 数据一键上传到数据库。

## 解决方案
也许你会想「这个简单，把 Excel 转化为 SQL 语句不就好了」，且不说转化起来不容易，本身 Excel 的数据就不够干净，需先做数据处理与清洗。

最后，经过一番搜索，找到了 Python 的这个好用的 SSH Tunnel 包：

* [pahaz/sshtunnel: SSH tunnels to remote server.](https://github.com/pahaz/sshtunnel) 一个用 SSH 连接远程数据库的 Python 包。

## 解决思路
* 结合 pandas 写两个函数，一个用于查询数据库，一个用于推送数据到数据库。
* 结合 sshtunnels 远程用 Python 访问数据库
* 结合 docopt 等工具写成命令行脚本
* 结合 pandas 写成数据清理、推送一体化脚本。

## 具体实现
sshtunnels 顾名思义就是  SSH tunnels to remote server(SSH 连接远程服务器)。

**[原理](https://github.com/pahaz/sshtunnel#usage-scenarios)**

```
----------------------------------------------------------------------

                            |
-------------+              |    +----------+
    LOCAL    |              |    |  REMOTE  | :22 SSH
    CLIENT   | <== SSH ========> |  SERVER  | :8080 web service
-------------+              |    +----------+
                            |
                         FIREWALL (only port 22 is open)

----------------------------------------------------------------------
```

大意就是只通过一个 ssh 通道就能访问 8080 端口的服务。

**连接数据库**

```python
import os
import pandas as pd

from sqlalchemy import create_engine
from sshtunnel import SSHTunnelForwarder


HOSTNAME = '127.0.0.1'  # host
DATABASE = database  # 数据库名，
USERNAME = usename  # 数据库用户名
PASSWORD =  password  # 数据库密码
HOME_PATH = os.getenv('HOME')  # 获取 ~ 目录地址
PKEY_PATH = os.path.join(HOME_PATH, '.ssh', 'id_rsa')


def zkquery(q):
    """The function is support for quering zokoodb.

    Args:
    - q: quering string
    Return:
    - A DataFrame
    """
    with SSHTunnelForwarder(
        (remoteip, port),  # 换成你的远程 ip 和 ssh port
        ssh_username=username,
        ssh_pkey=PKEY_PATH,  # sshkey 地址
        remote_bind_address=('localhost', 3306)
    ) as server:
        engine = create_engine(
            'mysql+mysqlconnector://{}:{}@{}:{}/{}'.format(
                USERNAME, PASSWORD, HOSTNAME,
                server.local_bind_port, DATABASE
                ), echo=False)
        return pd.read_sql_query(q, engine)
```

通过上方 30 行左右的代码就能读取数据库并返回一个 DataFrame，同理写个函数推送 csv 或 Excel 数据至数据库。

```python
def df2zkdb(df, table):
    with SSHTunnelForwarder(
        (remoteip, port),
        ssh_username=username,
        ssh_pkey=PKEY_PATH,
        remote_bind_address=('localhost', 3306)
    ) as server:
        engine = create_engine(
            'mysql+mysqlconnector://{}:{}@{}:{}/{}'.format(
                USERNAME, PASSWORD, HOSTNAME,
                server.local_bind_port, DATABASE
                ), echo=False)
        df.to_sql(name=table, con=engine, if_exists='append', index=False)
        print("You have successfully pushed {0} records "
              "to the {1} table of zokoodb.".format(df.shape[0], table))
        print("Done!!!")
```

**命令行脚本**

```python
# -*- coding: utf-8 -*-
import sys
from zkMySQL import zkquery


def cli():
    try:
        df = zkquery(sys.argv[1])
        return df.to_csv(sys.stdout, index=False)
    except (BrokenPipeError, IOError):
        pass
    sys.stderr.close()


if __name__ == '__main__':
    cli()

```

效果：

```bash
$ zkquery 'select dt_add, id from goods' | head | csvlook
|----------------------+-------|
|  dt_add              | id    |
|----------------------+-------|
|  2016-07-07 16:22:41 | 1948  |
|  2016-07-07 17:27:46 | 1949  |
|  2016-07-26 12:48:39 | 1953  |
|  2016-07-18 15:08:50 | 1950  |
|  2016-07-18 15:37:04 | 1951  |
|  2016-07-25 17:50:24 | 1952  |
|  2016-08-03 14:31:43 | 1954  |
|  2016-08-04 16:11:11 | 1956  |
|  2016-08-08 10:29:39 | 1957  |
|----------------------+-------|
```

同理写个推送用的，效果：

```bash
$ df2zkdb -h
df2zkdb
A script to push DataFrame to zokoodb.

Usage:
    df2zkdb <input> -t <table>

Options:
    -h --help   Show this screen.
    -t --table  to table.

$ df2zkdb goods_temp.csv -t goods_temp
You have pushed 4 records to the goods_temp table of zokoodb.
Done!!!
```

## References
* [Query your database over an SSH tunnel with Pandas – Medium](https://medium.com/@amirziai/query-your-database-over-an-ssh-tunnel-with-pandas-603ce49b35a1#.ootfbcclj)
* [【Python实战】Pandas：让你像写SQL一样做数据分析（二） - Treant - 博客园](http://www.cnblogs.com/en-heng/p/5686062.html)
