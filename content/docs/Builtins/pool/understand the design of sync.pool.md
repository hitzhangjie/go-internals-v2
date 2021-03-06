
---
title: 'understand the design of sync.pool'
score: '⭐️⭐️⭐️⭐️⭐️'
tags: ['sync.pool']
author: 'Vincent Blanchon'
publisher: 'medium'
status: 'Finished'
link: 'https://medium.com/a-journey-with-go/go-understand-the-design-of-sync-pool-2dde3024e277?source=---------50-----------------------'
---

# Let's Summarize

1 sync.Pool设计

sync.Pool中有个local unsafe.Pointer字段，其实际类型为[P]poolLocal，当我们创建一个sync.Pool的时候，实际上是为每个P创建了一个poolLocal，每个P创建、获取对象时优先从Pool.poolLocal[p.id]中获取，这种类似的按照p.id执行sharding的方式可以有效减少lock intention，提高Pool的读写效率。

ps：准确地说，poolLocal.poolLocalInternal内部包含了private和shared字段，private只可以被当前pinned P访问，shared可以被所有P访问。这种设计有效减少了lock intention。

2 poolCleanup

sync.Pool有一个默认的poolCleanup函数，用于清理池子中不用的对象，以节省内存占用，在执行runtime.GC的时候，也会执行poolCleanup函数，当触发GC时，sync.Pool中的对象会被释放掉，一方面减少了内存占用，但是另一方面下次通过池子获取对象时又要申请内存，也会影响效率。

3 poolChain

go1.13中引入了一个lock-free的poolChain实现（poolLocalInternal.shared）和victim cache。

- lock-free poolChain，pinned P在当前poolChain的head push/pop，只有当前P可以这么干，所以不需要同步措施；所有P都可以pop tail，需要做同步，这里是通过CAS原子操作来实现的。

这里的poolChain组织比较有意思，第一次是8个元素的deque，不够了申请16个元素的deque，并通过prev/next指针和之前的deque关联起来，第三次是32个……每次申请都是size直接double并通过指针串起来。因为poolChain.head只有当前P可操作，所以不需要同步，poolChain.tail允许多个P并发操作，用CAS原子操作来同步。

- victim cache，runtime.GC()触发poolCleanup的时候，会将前一轮中poolLocal中分配的的对象赋值给victim这个cache，方便后面复用，怎么复用呢？

当执行sync.Pool.Get()的时候：
- 先获取sync.Pool.local（实际是[P]poolLocal）的poolLocal[P.id].private有没有对象；
- 没有继续检查poolLocal[P.id].shared.popTail()有没有对象；
- 还没有就会尝试看看sync.Pool.victim中有没有；
- 还没有，就检查sync.Pool.New函数有没有定义，有则执行分配对象；

如果再下一轮runtime.GC()触发poolCleanup的时候，victim就会被干掉了。

ps：这里sync.Pool的设计比预想重要复杂些，可以再分析一下。

# Source Analysis



# References
1. https://medium.com/a-journey-with-go/go-understand-the-design-of-sync-pool-2dde3024e277?source=---------50-----------------------
