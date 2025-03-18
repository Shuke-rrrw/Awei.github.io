---
layout: post
title: Latex｜解决ubuntu下安装TexLive时进度卡住的问题
categories: [Latex]
description: apt-get安装texlive遇到bug卡在Pregenerating ConTeXt MarkIV format. This may take some time...
keywords: Latex,ubuntu
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

## 遇到问题描述

使用下面的命令安装TexLive
```objc
sudo apt-get install texlive-full
```

进度卡在
```objc
Pregenerating ConTeXt MarkIV format. This may take some time...
```

开始我以为确实需要时间就静等了一会儿，结果好几小时卡在这个位置。

## 故障原因

貌似是缺乏context依赖，而context现在已经无法直接用这种方式安装


## 解决方案

连续按压Enter键，然后再静等几分钟就可以了。

## 参考

- [https://blog.csdn.net/sdy691/article/details/137226717][1]
- [https://askubuntu.com/questions/956006/pregenerating-context-markiv-format-this-may-take-some-time-takes-forever][2]

[1]: https://blog.csdn.net/sdy691/article/details/137226717
[2]: https://askubuntu.com/questions/956006/pregenerating-context-markiv-format-this-may-take-some-time-takes-forever
