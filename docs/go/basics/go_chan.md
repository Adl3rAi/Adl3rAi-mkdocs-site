# goroutine and channel

写一个简单的“一对二”传球`func`，可以打印接球，失球日志，并判断胜负。

```go
func player(name string, ch chan int) {
  defer wg.Done()
  
  for {
    ball, ok := <-ch
    if !ok {
      fmt.Printf("Channel is closed. %s wins\n", name)
    }
    n := rand.Intn(100)
    if n%10 == 0 {
      close(ch)
      fmt.Printf("%s misses the ball. %s loses\n", name, name)
      return
    }
    ball++
    fmt.Printf("%s receives ball %d\n", name, ball)
    ch <- ball
  }
}
```

```go
func main() {
  wg.Add(2)
  ch := make(chan int)
  go player("A", ch)
  go player("B", ch)
  ch <- 0
  wg.Wait()
}
```

通道`ch`让两个`go player()`函数（两个球员）共享了同一筐篮球，其中筐内篮球包含唯一的编号(#1,#2,#3...)，但通道本身并不是篮球筐，通道是将球从秋筐中拿出来的“手”，当有一名球员失球，“手”将不再从筐中取球；当没有球员失球，“手”要依据刚才扔出的球的编号，在筐中找寻下一个编号(`ball++`)的球，并扔出。

