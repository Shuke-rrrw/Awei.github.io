---
layout: post
title: git｜回退到git某一历史版本的操作
categories: [git]
description: 回退到历史版本
keywords: bundle,ubuntu
mermaid: false
sequence: false
flow: false
mathjax: false
mindmap: false
mindmap2: false
---

## 遇到问题描述

实现多人合作程序开发的过程中，我们有时会出现错误提交的情况，此时我们希望能撤销提交操作，让程序回到提交前的样子，这里总结了两种解决方法：reset、evert。

## 解决方案

1.reset 的使用方法（会覆盖，不太建议）
![](/images/posts/git/git_reset.png)
git log查看要回退的版本号
```objc
git log
```
使用 git reset --hard （目标版本号）命令将版本回退
```objc
git reset --hard （目标版本号）
```
此时再推到远程仓库用git push 会报错，需要用git push -f强推上去,注意这样会覆盖目标版本号之后的所有版本。
```objc
git push -f origin branch
```
2.revert的使用方法
![](/images/posts/git/git_revert.png)

git log查看要回退的版本号
```objc
git log
```
使用 git revert -n （目标版本号）命令将版本回退
```objc
git revert -n （目标版本号）
```

这里可能会出现冲突，那么需要手动修改冲突的文件。然会就正常的提交流程就可以了，会生成一个新的版本，不会影响到以前的版本
```objc
git push origin branch
```
## 参考

- [https://www.cnblogs.com/fuqian/p/17187457.html][1]

[1]: https://www.cnblogs.com/fuqian/p/17187457.html
