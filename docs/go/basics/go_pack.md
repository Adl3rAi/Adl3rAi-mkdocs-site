# Golang中的package概念

首先，新建一个 `fmt` package，以重写`fmt.Println()`方法

Golang的错误处理：失败的调用会返回错误值而非错误码，所以声明的方式为

```go
file, err := os.OpenFile(...)
```

```go
package fmt

import (
	"io"
  "log"
  "os"
)

var Logger *log.Logger

func init() {
  file, err := os.OpenFile("trace.txt", os.O_CREATE|os.O_RDWR|os.O_APPEND, 0666)
  if err != nil {
    panic(err)
  }
  
  Logger = log.New(io.MultiWriter(os.Stdout, file), "Log: ", log.LstdFlags)
}
```

`os.O_CREATE`如果不存在将创建一个新文件夹

`os.O_RDWR`读写模式打开文件

`os.O_APPEND`写操作时将数据附加到文件尾部

`0666`

---

`downloader.go`文件

```go
package downloader

import (
	"encoding/json"
  "io"
  "net/http"
)

type InfoRequest struct {
  Bvids []string
}

type VideoInfo struct {
  Code int `json: "Code"`
  Data struct {
    Bvid string `json: "bvid"`
    Title string `json: "title"`
    Desc string `json: "desc"`
  }`json: "data"`
}

type InfoReponse struct {
  Infos []VideoInfo
}

// BatchDownloadVideoInfo(request.Bvids[bvid_1, bvid_2,...]) return Infos[VideoInfo_1, VideoInfo_2, ...] / errors
func BatchDownloadVideoInfo(request InfoRequest)(InfoResponse, error) {
  var response InfoResponse
  for _, bvid := range request.Bvids {
    var videoInfo VideoInfo
    resp, err := http.Get("https:..," + bvid)
    if err != nil {
      return InfoResponse{}, nil
    }
    respBytes, err := io.ReadAll(resp.Body)
    //golang读取http.Request中body的内容到字符串respBytes当中
    if err != nil {
      return InfoResponse{}, nil
    }
    //golang解析respBytes中的json数据，存入videoInfo的地址所指向的值(也就是videoInfo)
    if err = json.Unmarschal(respBytes, &videoInfo); err != nil {
      return InfoResponse{}, err
    }
    response.Infos = append(response.Infos, videoInfo)
    if err := resp.Body.Close(); err != nil {
      return InfoResponse{}, err
    }
    response.Infos = append(response.Infos, videoInfo)
  }
  return response, nil
}
```

---

`main.go`文件

```go
package main

import (
	"fmt"
  "get-bili/downloader"
  myfmt "get-bili/fmt"
)

func main() {
  fmt.Println("...")
  myfmt.Logger.Println("...")
  
  request := downloader.InfoRequest {
    Bvids: []string{"...1", "...2"},
  }
  response, err := downloader.BatchDownloadVideoInfo(request)
  if err != nil {
    panic(err)
  }
  for _, info  := range response.Infos {
    myfmt.Logger.Printf("title: %s \n desc: %s\n", info.Data.Title, info.Data.Desc)
  }
}
```





