
---
title: 'unknown parts of the test package'
score: '⭐️⭐️⭐️⭐️⭐️'
tags: ['test']
author: 'Vincent Blanchon'
publisher: 'medium'
status: 'Finished'
link: 'https://medium.com/a-journey-with-go/go-unknown-parts-of-the-test-package-df8988b2ef7f'
---

# Let's Summarize

1 go test的cache机制

go test引入了cache系统，对于没有改变的代码不会重复执行测试用例，而是会用缓存的上次的测试结果。

go test何时使用cache呢？不只是检查测试的内容有没有变更，也会检查有没有更新环境变量，有没有使用不同的flag，如果有，则不会使用缓存的结果。

如何存储测试结果以及查询应用缓存的测试结果呢？go test对content、环境变量、命令行flags计算hash，计算完成后，存储到$GOCACHE目录下，每个hash对应一个文件，可以通过`hexdump -C`查看缓存数据。

go test执行时如何绕过cache系统呢，执行`GOCACHE=off go test`。

2 go test的黑白盒测试

- 黑盒测试：只关心导出函数的测试，不关心内部实现；
- 白盒测试：关心内部实现，可以测试导出、非导出函数；

比如hello.go对应package名为hello：
- 黑盒测试：定义文件hello_test.go，package定义为hello_test；
- 白盒测试：定义文件hello_test.go，package定义为hello；

3 go test -bench自定义metrics

go test的时候，会输出一些统计数据，比如benchmark的时候可以输出op平均时间、mem allocs的一些数据，也可以通过`b.ReportMetric(value float64, desc string)`来自定义统计数据展示。

# Source Analysis



# References
1. https://medium.com/a-journey-with-go/go-unknown-parts-of-the-test-package-df8988b2ef7f
