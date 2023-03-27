# Go单元测试

## 概念

Go语言有比较傲完善的工具链可以支持对Go程序做单元测试。

通过`go test`命令来运行单元测试用例。

## 语法

[参考](https://geektutu.com/post/quick-go-test.html)

```go
import "testing"
// 导包testing
// 函数名必须以大写Test开头, Xxx自定义
func TestXxx(t *testing.T) {
    
}

```



`go test`执行当前package下所有测试

`go test -run TestXxx`指定测试

## References



