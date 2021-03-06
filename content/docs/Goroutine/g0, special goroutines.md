
---
title: 'g0, special goroutines'
score: '⭐️⭐️⭐️⭐️⭐️'
tags: ['goroutine, select-case']
author: 'Vincent Blanchon'
publisher: 'medium'
status: 'Reading'
link: 'https://medium.com/a-journey-with-go/go-g0-special-goroutine-8c778c6704d8'
---

# Let's Summarize

goroutines是由go scheduler来调度的，go scheduler在某些goroutine出现阻塞的时候调度其他goroutines继续执行，以提高cpu的利用率。

goroutines其实是运行在操作系统线程里面的，GOMAXPROCS限制了最多可以并发执行的线程数量（实际线程可能有阻塞的，线程总数会大于等于该环境变量值）。go scheduler负责将goroutines调度到操作系统线程上执行，在程序初始化阶段，会为每个持有P的线程M创建一个特殊的协程g0，这里的g0其实就是会干go scheduler的工作，g0会调度就绪的goroutines在关联的线程M上执行。

g0的职责

g0的职责并不仅仅是scheduler，它还有其他的用途。g0的协程栈（一开始就是8KB）比普通的协程的栈（一开始为2KB）要大很多，它适用于执行一些对栈容量要求比较大、又不希望栈扩容的情境下。

列举下g0的职责：
- goroutine创建，如go func(){}()是在g0中完成的，将其创建完放到P的队列中；
- defer function创建；
- GC操作，如STW、扫描协程栈、标记整理等操作；
- 栈增长，在函数序言prologue部分执行栈增长；

在其他的一些操作中（比如需要更大的内存分配的cgo操作），这个特殊协程g0使我们的程序更加高效的执行管理操作，而又不会带来过高的内存开销。

# Source Analysis

TODO 结合源码来分析下go scheduler的详细过程，以及g0的详细用途

# References
1. https://medium.com/a-journey-with-go/go-g0-special-goroutine-8c778c6704d8
