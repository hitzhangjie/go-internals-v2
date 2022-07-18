---
weight: 3
bookFlatSection: true
title: '准备'

title2: 'installing go from source'
description: 'installing go from source'
score: '⭐️⭐️⭐️⭐️'
tags: ['install']
author: 'gophers'
publisher: 'golang'
status: 'Finished'
link: 'https://go.dev/doc/install/source'
---

# Let's Summarize

介绍了如何从源码安装go，这对希望给go贡献代码或者希望从源码学习go的同学还是有必要掌握的。
通常情况下，如果本地安装了go：
- 只是想从源码编译go的话，cd src然后执行all.bash即可。
- 如果想阅读学习源码，IDE里面需要正确设置GOROOT指向源码目录，不然遇到一些“use of internal package not allowed”的问题，因为它默认走到了/usr/local/go/src中的代码，这个时候发现导入包的importpath又是internal就报错了，把GOROOT指向下载的源码目录即可解决。搞定了这个就可以在IDE里正常阅读，并执行单测。

# Source Analysis



# References
1. https://go.dev/doc/install/source