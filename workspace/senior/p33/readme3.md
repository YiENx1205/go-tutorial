# Go 1.20要来了，看看都有哪些变化-第2篇

## 前言

Go官方团队在2022.12.08发布了Go 1.20 rc1(release candidate)版本，Go 1.20的正式release版本预计会在2023年2月份发布。

让我们先睹为快，看看Go 1.20给我们带来了哪些变化。

安装方法：

```bash
$ go install golang.org/dl/go1.20rc1@latest
$ go1.20rc1 download
```

这是Go 1.20版本更新内容详解的第3篇，欢迎大家关注公众号，及时获取本系列最新更新。

## Go 1.20发布清单

和Go 1.19相比，改动内容适中，主要涉及语言(Language)、可移植性(Ports)、工具链(Go Tools)、运行时(Runtime)、编译器(Compiler)、汇编器(Assembler)、链接器(Linker)和核心库(Core library)等方面的优化。

第1篇主要涉及Go 1.20在语言、可移植性方面的优化，原文链接：[Go 1.20版本升级内容第1篇](https://mp.weixin.qq.com/s?__biz=Mzg2MTcwNjc1Mg==&mid=2247484629&idx=1&sn=60a01d3cc85ef2462156f0565c30738d&chksm=ce124bbaf965c2ac351cd9c602e8b67d5119b2a89a7f2de0289bdeb7608ae589c329eb8f7275&token=1619842941&lang=zh_CN#rd)。

第2篇主要涉及Go命令和工具链方面的优化，原文链接：[Go 1.20版本升级内容第2篇](https://mp.weixin.qq.com/s?__biz=Mzg2MTcwNjc1Mg==&mid=2247484638&idx=1&sn=459a22d4a9bf5d9715e70d3c25b05b93&chksm=ce124bb1f965c2a76bacc1135799ab268be66a861e99391b354a9f2dfd8c22a60853cc1d689d&token=1342188569&lang=zh_CN#rd)。

本文重点介绍Go 1.20在运行时、编译器、汇编器和链接器等方面的优化。

## Runtime

The runtime now has experimental support for memory-safe arena allocation that makes it possible to eagerly free memory in bulk. When used appopriately, it has the potential to improve CPU performance by up to 15% in memory-allocation-heavy applications. To try it out, build your Go program with `GOEXPERIMENT=arenas`, which will make the `arena` package visible to your program. Source files that import the `arena` package must require the `goexperiment.arenas` build tag.

Some of the garbage collector's internal data structures were reorganized to be both more space and CPU efficient. This change reduces memory overheads and improves overall CPU performance by up to 2%.

The garbage collector behaves less erratically with respect to goroutine assists in some circumstances.

Go 1.20 adds a new `runtime/coverage` package containing APIs for writing coverage profile data at runtime from long-running and/or server programs that do not terminate via `os.Exit()`.

## Compiler

Go 1.20 adds preview support for profile-guided optimization (PGO). PGO enables the toolchain to perform application- and workload-specific optimizations based on run-time profile information. Currently, the compiler supports pprof CPU profiles, which can be collected through usual means, such as the `runtime/pprof` or `net/http/pprof` packages. To enable PGO, pass the path of a pprof profile file via the `-pgo` flag to `go` `build`, as mentioned [above](https://tip.golang.org/doc/go1.20#go-command). Go 1.20 uses PGO to more aggressively inline functions at hot call sites. Benchmarks for a representative set of Go programs show enabling profile-guided inlining optimization improves performance about 3–4%. We plan to add more profile-guided optimizations in future releases. Note that profile-guided optimization is a preview, so please use it with appropriate caution.

The Go 1.20 compiler upgraded its front-end to use a new way of handling the compiler's internal data, which fixes several generic-types bugs and enables local types in generic functions and methods.

The compiler now [disallows anonymous interface cycles](https://go.dev/issue/56103).

Relative to Go 1.19, generated code performance is generally slightly improved, build wall times are slightly increased, build user times are slightly decreased.

## Linker

On Linux, the linker now selects the dynamic interpreter for `glibc` or `musl` at link time.

On Windows, the Go linker now supports modern LLVM-based C toolchains.

Go 1.20 uses `go:` and `type:` prefixes for compiler-generated symbols rather than `go.` and `type.`. This avoids confusion for user packages whose name starts with `go.`. The [`debug/gosym`](https://tip.golang.org/pkg/debug/gosym) package understands this new naming convention for binaries built with Go 1.20 and newer.

## Bootstrap

When building a Go release from source and `GOROOT_BOOTSTRAP` is not set, previous versions of Go looked for a Go 1.4 or later bootstrap toolchain in the directory `$HOME/go1.4` (`%HOMEDRIVE%%HOMEPATH%\go1.4` on Windows). Go 1.18 and Go 1.19 looked first for `$HOME/go1.17` or `$HOME/sdk/go1.17` before falling back to `$HOME/go1.4`, in ancitipation of requiring Go 1.17 for use when bootstrapping Go 1.20. Go 1.20 does require a Go 1.17 release for bootstrapping, but we realized that we should adopt the latest point release of the bootstrap toolchain, so it requires Go 1.17.13. Go 1.20 looks for `$HOME/go1.17.13` or `$HOME/sdk/go1.17.13` before falling back to `$HOME/go1.4` (to support systems that hard-coded the path $HOME/go1.4 but have installed a newer Go toolchain there). In the future, we plan to move the bootstrap toolchain forward approximately once a year, and in particular we expect that Go 1.22 will require the final point release of Go 1.20 for bootstrap.

## 总结

下一篇会介绍Go 1.20在核心库方面的优化工作，有一些内容值得学习，欢迎大家保持关注。



## 推荐阅读

* [Go 1.20要来了，看看都有哪些变化-第1篇](https://mp.weixin.qq.com/s?__biz=Mzg2MTcwNjc1Mg==&mid=2247484629&idx=1&sn=60a01d3cc85ef2462156f0565c30738d&chksm=ce124bbaf965c2ac351cd9c602e8b67d5119b2a89a7f2de0289bdeb7608ae589c329eb8f7275&token=1342188569&lang=zh_CN#rd)

* [Go 1.20要来了，看看都有哪些变化-第2篇](https://mp.weixin.qq.com/s?__biz=Mzg2MTcwNjc1Mg==&mid=2247484638&idx=1&sn=459a22d4a9bf5d9715e70d3c25b05b93&chksm=ce124bb1f965c2a76bacc1135799ab268be66a861e99391b354a9f2dfd8c22a60853cc1d689d&token=1342188569&lang=zh_CN#rd)

* [Go面试题系列，看看你会几题](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=Mzg2MTcwNjc1Mg==&action=getalbum&album_id=2199553588283179010#wechat_redirect)

* [Go常见错误和最佳实践系列](https://mp.weixin.qq.com/mp/appmsgalbum?__biz=Mzg2MTcwNjc1Mg==&action=getalbum&album_id=2549657749539028992#wechat_redirect)

  

## 开源地址

文章和示例代码开源在GitHub: [Go语言初级、中级和高级教程](https://github.com/jincheng9/go-tutorial)。

公众号：coding进阶。关注公众号可以获取最新Go面试题和技术栈。

个人网站：[Jincheng's Blog](https://jincheng9.github.io/)。

知乎：[无忌](https://www.zhihu.com/people/thucuhkwuji)。



## 福利

我为大家整理了一份后端开发学习资料礼包，包含编程语言入门到进阶知识(Go、C++、Python)、后端开发技术栈、面试题等。

关注公众号「coding进阶」，发送消息 **backend** 领取资料礼包，这份资料会不定期更新，加入我觉得有价值的资料。还可以发送消息「**进群**」，和同行一起交流学习，答疑解惑。



## References

* https://tip.golang.org/doc/go1.20