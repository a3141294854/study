# Http包





## 客户端

### HTTP客户端实例化

```go
client := new(http.Client)
```

### 构造HTTP请求

请求类型 + url +请求体

返回请求对象，错误

```go
req, err := http.NewRequest("GET", "http://localhost:8080/index", nil)
  if err != nil {
    fmt.Printf("创建请求失败: %v\n", err)
    return
  }
```

### 发送HTTP请求

客户端 + 请求

返回响应，错误

```go
res, err := client.Do(req)
  if err != nil {
    fmt.Printf("请求失败: %v\n", err)
    return
  }
  defer res.Body.Close()
```

### 接收响应

返回切片，响应体的内容

```go
b, err := io.ReadAll(res.Body)
  if err != nil {
    fmt.Printf("读取响应失败: %v\n", err)
    return
  }

//状态行的http版本
res.Proto
//状态行的状态码
res.StatusCode
//状态行的状态码 + 状态信息
res.Status
// 常见状态码判断
if res.StatusCode == http.StatusOK {
    fmt.Println("请求成功")
} else if res.StatusCode == http.StatusNotFound {
    fmt.Println("资源未找到")
} else if res.StatusCode >= 400 {
    fmt.Println("请求出错")
}

//响应头，通过get查找，是个map,或者通过range,获取全部
res.Header.Get

for key, values := range res.Header {
    fmt.Printf("  %s: %v\n", key, values)
}

// 获取特定响应头
contentType := res.Header.Get("Content-Type")
contentLength := res.Header.Get("Content-Length")
server := res.Header.Get("Server")

fmt.Printf("内容类型: %s\n", contentType)
fmt.Printf("内容长度: %s\n", contentLength)
fmt.Printf("服务器: %s\n", server)
```





## 服务端

```go
package main

import (
    "log"
    "net/http"
)

func handler(res http.ResponseWriter, req *http.Request) {
    // 记录请求信息
    log.Printf("收到请求: %s %s", req.Method, req.URL.Path)
    
    // 设置响应头
    res.Header().Set("Content-Type", "text/plain; charset=utf-8")
    
    // 写入响应体，这里默认状态码为200 OK 自动设置urf-8编码
    message := "hello 枫枫"
    if _, err := res.Write([]byte(message)); err != nil {
        log.Printf("写入响应失败: %v", err)
    }
    
    log.Printf("请求处理完成: %s", req.URL.Path)
}

func main() {
    // 注册路由
    // /index表示url路径模式，它的处理方式是handler函数
    http.HandleFunc("/index", handler)
    
    
    // 启动服务器并处理错误，监听8080端口，localhost是指向本机的，只响应这个端口的请求
    log.Println("服务器启动在 http://localhost:8080")
    if err := http.ListenAndServe(":8080", nil); err != nil {
        log.Fatalf("服务器启动失败: %v", err)
    }
}
```

