
---
title: 'Diagnostics Methods'
score: '⭐️⭐️⭐️⭐️⭐️'
tags: ['diagnostics']
author: 'gophers'
publisher: 'golang'
status: 'Finished'
weight: 1
link: 'https://golang.org/doc/diagnostics'
---

# Let's Summarize

介绍了go程序开发人员常用的问题诊断方法：

1 profiling：执行go tool pprof对cpu、内存、线程创建、锁使用问题等进行分析、问题定位、优化；
2 tracing：织入代码对运行过程中各阶段耗时进行统计等等，如golang.org/x/net/trace这个包，没怎么使用过；
3 debugging：调试器调试，有调试需求的时候，通过-gcflags="all=-N -l"禁用优化、内联汇编，生成的二进制对调试更友好；
4 runtime statistics and events：运行时信息收集，如内存、gc、stack、heap、goroutines数量等信息；
5 execution tracer：对程序执行过程中运行时行为、事件进行分析、问题定位、优化，如gc、goroutine调度latency、系统调用等等；
6 GODEBUG：该环境变量用来控制一些行为，如：
- GODEBUG=gctrace=1，每隔一定时间打印gc的信息，回收内存数，以及停顿时间；
- GODEBUG=inittrace=1，打印包初始化花费的时间；
- GODEBUG=schedtrace=X，每隔X毫秒打印调度相关信息；
GODEBUG还可以用来禁用全部或者某些指令级扩展，这个不怎么常用，先忽略。

这里的execution tracer数据的生成，可以借助：
- 代码里面import runtime/trace这个package，然后main函数里trace.Start(os.Stderr), defer trace.Stop()，运行起来go run main.go &> trace.out；
- 通过go test -trace trace.out；
- 然后通过go tool trace trace.out查看；

关于GODEBUG=schedtrace=X的输出格式的理解，see https://www.ardanlabs.com/blog/2015/02/scheduler-tracing-in-go.html。举个例子 runqueue 0 [4 4]是说全局queue里面有0个g，现在有两个p，每个p上的queue均有4个g待运行。

# Source Analysis



# References
1. https://golang.org/doc/diagnostics
