
---
title: 'how does go runtime handles syscall'
score: '⭐️⭐️⭐️⭐️⭐️'
tags: ['syscall']
author: 'zhangjie'
publisher: 'tencent'
status: 'Finished'
link: 'https://www.hitzhangjie.pro/blog/2021-06-06-how-go-handles-syscall/'
---

# Let's Summarize

介绍了linux系统调用的一种分类方式：阻塞型系统调用和非阻塞型系统调用。

线程执行阻塞型系统调用时，一定会陷入不可中断等待状态，除了内核本身，没有任何外部事件可以将线程唤醒。go runtime需要感知对阻塞型系统调用的使用，避免线程被阻塞导致无线程调度goroutines。

go对系统调用进行了包装，阻塞型系统调用对应的是Syscall，非阻塞型系统调用是RawSyscall，后者不会导致线程阻塞，没什么好讨论的，我们只讨论Syscall。

Syscall内部会向运行时报告，当前线程即将进入一个阻塞型系统调用，然后运行时会执行一些对应的处理，如将M和P分离，挂起G，可能还会创建新的M来获得P来执行G。这里涉及到调度亲和性的一些细节，这里的总结就不过多展开了。

本文中的叙述风格关于故事化，关于为什么要尝试让G在原来的M、M在原来的P上执行，注意一下调度亲和性，就比较容易理解。

# Source Analysis



# References
1. https://www.hitzhangjie.pro/blog/2021-06-06-how-go-handles-syscall/
