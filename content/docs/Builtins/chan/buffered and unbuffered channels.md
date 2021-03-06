
---
title: 'buffered and unbuffered channels'
score: '⭐️⭐️⭐️⭐️⭐️'
tags: ['chan']
author: 'Vincent Blanchon'
publisher: 'medium'
status: 'Finished'
link: 'https://medium.com/a-journey-with-go/go-buffered-and-unbuffered-channels-29a107c00268'
---

# Let's Summarize

src/runtime/chan.go中hchan，bufferedChan和unbufferedChan都是用这个数据类型来表示。

1 unbuffered chan

unbufferedChan：创建chan时没有指定capacity或者capacity=0，这样的chan是无缓冲的。chan sender执行chan send操作时会阻塞，直到chan reader读取了该数据。

chan sender阻塞时，会申请一个sudog，记录chan sender对应的g，并记录chan sender要send的value，chan reader读取时直接通过memmove移动sudog中记录的数据到chan reader要read到的变量中，并resume sudog记录的g。

2 buffered chan

bufferedChan：创建chan时指定了capacity≥1：
- chan sender执行send操作时，如果chan满了则会阻塞；
- chan reader执行read操作时，如果chan空了则会阻塞；

hchan中有记录当前chan中元素数量hchan.qcount，并通过hchan.dataqsiz记录capacity，通过一个环形队列hchan.buf来存储存储的元素数量，该环形队列通过hchan.sendx/hchan.recvx来记录下次chan send操作的写入位置和下次chan read操作时的读取位置。

和unbufferedChan类似，如果chan sender、chan reader要阻塞时需要申请sudog并记录到sendq、recvq中。
- 后续chan中有空位可写入时，再选择sendq中的某个sudog并memmove其send的value然后resume其执行；
- 或者chan中有数据可读取时，再选择recvq中的某个sudog并memmove hchan.buf[recvx]中数据到要读取到的变量然后resume其执行；

大致实现过程就是这样的。

3 性能问题

chan buffer的大小、有无，chan sender、receiver的数量，会影响到chan send、recv的性能，这个buffer大小不能凭空臆想，要结合经验、benchmark来指定一个合理的值。

# Source Analysis



# References
1. https://medium.com/a-journey-with-go/go-buffered-and-unbuffered-channels-29a107c00268
