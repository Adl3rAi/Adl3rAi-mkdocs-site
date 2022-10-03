# Golang Context

`context`用于解决goroutine之间退出通知、元数据传递的功能

```go
func doSomething(ctx context.Context) {
  // 监听
  select {
    case <- ctx.Done():
    err := ctx.Err()
    if err != nil {
      fmt.Println(err.Error())
    }
    case <- time.After(5 * time.Second):
    fmt.Println("finish doing something")
  }
}

func main() {
  ctx := context.Background()
  ctx, cancel := context.WithCancel(ctx)
  
  go func {
    time.Sleep(4 * time.Second)
    cancel()
  }()
  
  doSomething(ctx)
}
```

#1 传递共享的数据

```go
package main

import (
	"context"
  "fmt"
)

func main() {
  ctx := context.Background()
  process(ctx)
  ctx = context.WithValue(ctx, "traceId", "macOS")
  process(ctx)
}

func process(ctx context.Context) {
  traceId, ok := ctx.Value("traceId").(string)
  if ok {
    fmt.Printf("process over. trace id = %s\n", traceId)
  } else {
    fmt.Printf("process over. No trace id,\n")
  }
}
```

#2 取消goroutine

> 打开外卖的订单页，地图显示外卖的位置，每秒更新一次。app端向后台发起请求后，后台启动一个协程，每隔1s计算1次位置，并发送给端。如果用户退出次页面，则后台需要取消此过程，退出goroutine，系统回收资源。

```go
func Perform(ctx context.Context) {
  for {
    calculatePos() // 计算位置
    sendResult() // 发送结果
    select {
      case <-ctx.Done():
      return
      case <-time.AfterTime(time.Second):
      ...
    }
  }
}

func main() {
  ctx, cancel := context.WithTimeout(context.Background(), time.Hour)
  go Perform(ctx)
  // ...
  // app端返回页面，调用cancel函数
  cancel()
}
```

