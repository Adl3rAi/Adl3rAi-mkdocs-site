# Golang `context.go`文档

`context`主要用于goroutine之间传递上下文信息，包括：取消信号、超时时间、截止时间、k-v等。

> Golang中的server是一个“协程模型”，即一个协程处理一个请求。例如在业务高发的高峰期，某个下游服务的响应变慢，当前系统的请求没有超时控制，或超时时间设置过大，那么下游服务返回数据的协程会越来越大。内存占用飙升，会导致服务器崩溃。
>
> 如果给下游设置的`timeout`，如果超过这个值还没有接收到返回数据，就直接向客户端返回一个默认值或者错误。

```go
type Context interface {
  Done() <-chan struct{}
  // 返回一个(只读)channel，可以表示context被取消的信号，只有当channel被关闭时会读出0，其余任何情况下都为nil，正是利用这点，子协程从channel中读出值后，尽快做一些收尾工作，并退出
  Err() error
  Deadline() (dealine time.Time, ok bool)
  Value(key interface{}) interface{}
}
```

```go
type canceler interface {
  cancel(removeFromParent bool, err error)
  Done() <-chan struct{}
}
```

```go
type emptyCtx int

func (*emptyCtx) Deadline() (deadline time.Time, ok bool) {
  return
}

func (*emptyCtx) Done() <-chan struct{} {
  return nil
}

func (*emptyCtx) Err() error {
  return nil
}

func (*emptyCtx) Value(key interface{}) interface{} {
  return nil
}
// 每个函数要么是直接return，要么是return nil
// 所以是一个空的context，永远不会被cancel，也没有储存值和deadline
```

所以，有

```go
var(
  background = new(emptyCtx)
  todo = new(emptyCtx)
)
```

通过如下两个导出的函数(首字母大写)对外公开

```go
func Background() Context {
  return background
}
// background通常在main函数中，作为所有context的根节点
func TODO() Context {
  return todo
}
// todo通常在并不知道传递什么context的情形（”占位“）
```

```go
type cancelCtx struct {
  Context
  // 可以被看成是一个Context
  mu sync.Mutex
  done chan struct{}
  children map[canceler]struct{}
  // 实现了canceler接口
  err error
}
```

```go
func (c *cancelCtx) Done() <- chan struct{} {
  c.mu.Lock()
  if c.done == nil {
    c.done = make(chan struct{})
  }
  d := c.done
  c.mu.Unlock()
  return d
}
//直接调用这个只读channel，协程会被block住
//
```

```go
func (c *cancelCtx) cancel(removeFromParent bool, err error) {
  if err == nil {
    panic("context: internal error: missing cancel error")
  }
  c.mu.Lock()
  if c.err != nil {
    c.mu.Unlock()
    return // 已经被其他协程取消
  }
  c.err = err
  if c.done == nil { // 关闭channel，通知其他协程
    c.done = closedchan
  } else {
    close(c.done)
  }
  for child := range c.children {
    //递归取消所有子节点
    child.cancel(false, err)
  }
  c.children = nil
  c.mu.Unlock()
  if removeFromParent {
    removeChild(c.Context, c)
  }
}
```

总体来看，`cancel()`的功能就是关闭channel: `c.done`；递归取消它的所有子节点；从父节点中删除自己。goroutine接收到取消信号的方式就是`select`中的`c.done`被选中

```go
func WithCancel(parent Context) (ctx Context, cancel CancelFunc) {
  c := newCancelCtx(parent)
  propagateCancel(parent, &c)
  return &c, func() { c.cancel(true, Canceled) }
}
// 公开的方法，传入一个父Context(通常为一个background)，返回一个新建的context，新context的done channel是新建的

func newCancelCtx(parent Context) cancelCtx {
  return cancelCtx{Context: parent}
}
```

