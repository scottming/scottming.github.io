---
layout: post
title: 终端播放神器 cmus
subtitle:   
date: 2016-11-02
author:     "Scott"
header-img: 
tags:
    - 工具箱
---

## 安装

- **id3v2**
    `brew install id3v2` 
- **cmus**
    `brew install cmus`

## 问题解决

`:set output_plugin=coreaudio` 可解决 `-`、`+` 号键调节声音问题。

```
$ brew uninstall cmus
$ brew install --HEAD cmus
$ brew cleanup
```

cmus 里输入 `:set softvol=true` 可解决 Warnning 问题，[参考](https://github.com/cmus/cmus/issues/345)。

## 使用

### id3v2

查看专辑信息

```
$ id3info file.mp3
```

批量修改作者和专辑名

```
$ id3v2 -a 'Ann Cook' *
$ id3v2 -A 'AAT_CD5' *
```

### cmus

快捷键 |功能
-------|----
`add`  |添加歌曲
`set`  |各种设置
`Enter`|播放选中
`C`    |播放
`V`    |停止
`X`    |重新播放
`H`    |后退 5 秒
`L`    |前进 5 秒







