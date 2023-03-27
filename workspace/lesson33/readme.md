# 函数式编程Functional Options

[参考](https://coolshell.cn/articles/21146.html)

```go
type Server struct {
    Addr string
    Port int
    Conf *Config
}

type Config struct {
    Protocol string
    Timeout  time.Duration
    Maxconns int
    TLS      *tls.Config
}

// 创建一个函数类型，名为Option，这个名字随意
type Option func(*Server)

func MaxConns(maxconns int) Option {
    return func(s *Server) {
        s.MaxConns = maxconns
    }
}
// 上面这组代码传入一个参数，然后返回一个函数，
// 返回的这个函数会设置自己的 Server 参数。例如：
// - 当我们调用其中的一个函数用 MaxConns(30) 时
// - 其返回值是一个 func(s* Server) { s.MaxConns = 30 } 的函数。
func Protocol(p string) Option {
    return func(s *Server) {
        s.Protocol = p
    }
}
func Timeout(timeout time.Duration) Option {
    return func(s *Server) {
        s.Timeout = timeout
    }
}
func MaxConns(maxconns int) Option {
    return func(s *Server) {
        s.MaxConns = maxconns
    }
}
func TLS(tls *tls.Config) Option {
    return func(s *Server) {
        s.TLS = tls
    }
}

func NewServer(addr string, port int, options ...func(*Server)) (*Server, error) {
  srv := Server{
    Addr:     addr,
    Port:     port,
    Protocol: "tcp",
    Timeout:  30 * time.Second,
    MaxConns: 1000,
    TLS:      nil,
  }
  for _, option := range options {
    option(&srv)
  }
  //...
  return &srv, nil
}


s1, _ := NewServer("localhost", 1024)
s2, _ := NewServer("localhost", 2048, Protocol("udp"))
s3, _ := NewServer("0.0.0.0", 8080, Timeout(300*time.Second), MaxConns(1000))



```

