
---
title: 'how does a program recover'
score: '⭐️⭐️⭐️⭐️⭐️'
tags: ['defer, panic, recover']
author: 'Vincent Blanchon'
publisher: 'medium'
status: 'Finished'
link: 'https://medium.com/a-journey-with-go/go-how-does-a-program-recover-fbbbf27cc31e'
---

# Let's Summarize

defer对应newdefer
panic对应gopanic
recover对应gorecover

defer细节：

defer调用会创建_defer对象并添加到g._defer，_defer通过内部的_defer.link构成了一个LIFO的defer栈，函数return（runtime.deferreturn）或者panic（内部会遍历g._defer并执行）时，就按照栈的方式来执行注册的defer函数。

ps：defer函数执行的时候是需要考虑栈帧分配的，这个可以再逻辑一下细节，也是go defer性能优化的一个方面，如避免defer引起的栈增长；

panic细节：

panic的时候，会创建_panic对象并添加到g._panic，_panic也和_defer类似通过_panic.link维护了一个链式结构，也是LIFO的形式，这意味着，如果一开始出现了panic1，g._panic被设置为panic1，假如在后续defer的时候，defer函数中又panic2，那么g._panic被设置为panic2且panic2.link==panic1。假如defer函数中执行了recover，也是先recover g._panic，然后再将g._panic更新为g._panic.link继续执行defer。

如果某个defer执行过程中执行了recover，那么g._panic.recovered被设置为true，假如panic chain中还有panic，会调用mcall(recovery)，这个是为了恢复g上下文为recover

recover细节：

recover调用对应实现函数是gorecover，recover函数必须在defer函数的底层调用才能正常recover后面的panic。gorecover(argp)这里的参数argp是调用recover函数的caller的地址，会与g._panic.argp是defer func(){}这里函数调用func(){}的地址，如果argp和g._panic.argp不相同，表示recover不是在合适的位置调用的，也就不能修改g._panic.recovered=true，即不能recover对应的panic。

recover函数使用的位置有讲究，就是这么来的。

runtime.Goexit()

这个函数的设计实现是复用了panic、defer处理的逻辑，但是因为这里的Goexit并不是真正的panic逻辑，所以其在_panic里面加了个新字段goexit=true，以与正常的panic处理逻辑区分开，我们也能看到在gopanic执行deferChain中有对此进行特殊判断，当遇到panic.goexit=true且panic.aborted=true时，允许直接切换上下文为Goexit处理逻辑（正常的recover逻辑会绕过Goexit）。

ps：篇幅原因，还有其他一些边界情况这里没有展开细讲，感兴趣可以自己查看源码。TODO 这里的理解还需要再加强一下。

# Source Analysis



# References
1. https://medium.com/a-journey-with-go/go-how-does-a-program-recover-fbbbf27cc31e
