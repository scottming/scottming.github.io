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
    ```
    $ brew install id3v2
    ``` 
- **cmus**
    ```
    $ brew install --HEAD cmus
    ```

## 问题解决

- cmus 里面输入 `:set output_plugin=coreaudio` 可解决 `-`、`+` 号键调节声音问题;
- 输入 `:set softvol=true` 可解决 Warnning 问题，[参考](https://github.com/cmus/cmus/issues/345)。

## 使用

### id3v2

查看专辑信息

```
$ id3info filename
```

删除所有专辑信息

```
$ id3v2 -D filename
```

批量修改作者和专辑名

```
$ id3v2 -a 'Ann Cook' *.mp3
$ id3v2 -A 'AAT_CD5' *.mp3
```

### cmus

快捷键 |功能
-------|----
`:add` |添加歌曲
`:set` |各种设置
`Enter`|播放选中
`c`    |播放/暂停
`v`    |停止
`x`    |重新播放
`h`    |后退 5 秒
`l`    |前进 5 秒
`D`    |删除选中 Track
`Page Down`|向下翻页
`Page Up`  |向上翻页


## 效果

**cmus**

![](http://7xjuve.com1.z0.glb.clouddn.com/cmus.jpeg?imageView2/2/w/800)

**iOS Boom**

![](http://7xjuve.com1.z0.glb.clouddn.com/Boom.jpeg?imageView2/2/w/360)

## References

- 官方文档
	- [Wiki](https://github.com/cmus/cmus/wiki)
	- [Tutorial](https://github.com/cmus/cmus/blob/master/Doc/cmus-tutorial.txt)
	- [Doc](https://github.com/cmus/cmus/blob/master/Doc/cmus.txt)

- 视频
	- [Tutorial: cmus (C Music Player) - YouTube](https://www.youtube.com/watch?v=cGJZ5Cfaacg)



