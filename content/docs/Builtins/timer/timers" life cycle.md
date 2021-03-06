
---
title: 'timers" life cycle'
score: '⭐️⭐️⭐️⭐️⭐️'
tags: ['timer']
author: 'Vincent Blanchon'
publisher: 'medium'
status: 'Finished'
link: 'https://medium.com/a-journey-with-go/go-timers-life-cycle-403f3580093a'
---

# Let's Summarize

time.AfterFunc(time.Second, func() {...})，注册timer的同时注册了一个callback，GMP模型里面，P有一个timers队列，我们创建的timer就被link到当前g.p的timers队列中。

scheduler调度过程中，也会检查timer，当timer到了执行时间时，需要执行对应的callback，这个callback不能在scheduler中执行，这会阻碍调度逻辑的正常执行。scheduler执行go callback()创建一个goroutine来执行。这个goroutine会被加入到当前g.p的localqueue中，等待被调度。

因为不是立即执行，所以callback的执行有一定的delay。如果某个goroutine在此goroutine之前执行，且有密集循环，以前协作式调度的时候可能callback执行的delay会比较明显。现在引入了抢占式调度，10ms一次抢占，因为密集循环导致的delay就没没那么明显了。

但是如果一个P上的timers很多或者goroutine很多，受限于g.p的localqueue的长度，timer上注册的callback的执行还是有可能会有明显的delay的。如何解决这个问题呢？
- timer-stealing机制，一个P上的timers可以被其他P偷走来执行；
- work-stealing机制，一个P上的localqueue上的g可以被其他P偷走来执行；

通过这两种方式，有力减少了timer callback执行时的delay问题。

# Source Analysis



# References
1. https://medium.com/a-journey-with-go/go-timers-life-cycle-403f3580093a
