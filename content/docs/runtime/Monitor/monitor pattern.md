
---
title: 'monitor pattern'
score: '⭐️⭐️⭐️'
tags: ['monitor']
author: 'Vincent Blanchon'
publisher: 'medium'
status: 'Finished'
link: 'https://medium.com/a-journey-with-go/go-monitor-pattern-9decd26fb28'
---

# Let's Summarize

监视器模式，大家可能听说过futures模式，但是监视器模式可能听的比较少。monitor模式、futures模式都是很早就出现的并发处理模式。

本文介绍了如何通过sync.Cond、chan来实现监视器模式。sync.Cond有几个重要操作：cond.Wait()让当前goroutine阻塞在当前条件变量上，等后续cond.Signal()唤醒，或者等后续cond.Broadcast()广播唤醒。

sync.Cond算是可读性比较强的一种监视器模式，但是它也存在一定的局限性，本文中就举了一个例子，但是暴露了一个问题：可能导致getMany(3000)的goroutine迟迟获取不到对应数量的items，即便是频繁被唤醒，也属于spurious wakeup很快又要Wait而阻塞执行。

这里暴露的spurious wakeup问题，应该尽力去避免以提升性能，如何解决呢？bryan mills在它的分享“rethinking classical concurrency patterns”中介绍了一种基于chan实现的思路：https://play.golang.org/p/rzSXpophC_p。

最后介绍了cond.Signal()、cond.Wait()背后的实现：ticket system，这部分翻翻源码就可以了 TODO

# Source Analysis



# References
1. https://medium.com/a-journey-with-go/go-monitor-pattern-9decd26fb28
