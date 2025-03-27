---
layout: post
title: Bundle｜解决bundle install时进度卡住的问题
categories: [笔记]
description: bundle install时要等很久或者卡住不动
keywords: bundle,ubuntu
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

## 遇到问题描述

近期在配置自己的github pages，想在本地生成预览静态网页，需要用到bundle和jekyll,在运行下面的命令的时候
```objc
bundle install
```
进度一直卡着不动或者要等很久，即使挂了梯子也没用。

## 故障原因

貌似是Ruby源的问题，国内访问不了，但奇怪的是挂上梯子后在浏览器访问https://rubygems.org是没问题的，但依旧安装不了，搞不懂。


## 解决方案

检查下Gemfile文件，更改一下Ruby源为国内镜像即可：
```objc
# source 'https://rubygems.org'
source 'https://gems.ruby-china.com'
```
重新运行：
```objc
bundle install
```
可以看到正常安装。
## 参考

- [https://blog.csdn.net/weixin_42215775/article/details/102951401][1]

[1]: https://blog.csdn.net/weixin_42215775/article/details/102951401
