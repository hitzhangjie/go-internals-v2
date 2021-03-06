
---
title: 'how are random numbers generated'
score: '⭐️⭐️⭐️⭐️⭐️'
tags: ['random']
author: 'Vincent Blanchon'
publisher: 'medium'
status: 'Finished'
link: 'https://medium.com/a-journey-with-go/go-how-are-random-numbers-generated-e58ee8696999'
---

# Let's Summarize

go标准库提供了两个package来生成随机数：math/rand和crypto/rand。

- math/rand：生成的是伪随机数，可以通过rand.Seed(int)指定种子，然后math/rand会根据种子生成一个sequence source，然后rand.Int()生成随机数，但是因为这里的sequence source是固定的，所以生成的随机数是可预测的。

- crypto/rand：这种方式做了更多的数学运算，可以实现更好的随机性；

math/rand这种方式性能好一点，大约是crypto/rand性能的10倍+；但是crypto/rand生成的随机数的随机性要好的多。

上述都是并发安全的实现，比如math/rand实现内部包含了一个mutex，如果不需要在并发环境中使用，也可以自定义一个随机数生成器，如通过：`rand.New(rand.NewSource(1).(rand.Source64)`，性能又可以获得较大提升。

ps: rand.NewSource(seed int)返回的source是非goroutine并发安全的。

# Source Analysis



# References
1. https://medium.com/a-journey-with-go/go-how-are-random-numbers-generated-e58ee8696999
