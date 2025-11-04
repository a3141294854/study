# `gin`包

## 路由器建立，绑定，服务器启动

```go
r := gin.Default()
//自动中间件
r.GET("/ping",fun)
//gin包用 *gin.Context封装了响应和请求的信息
r.Run("0.0.0.0:8080")

```

## 获取请求行，请求头，请求体

```go
func fun(c *gin.Context){
    // 获取单个请求头
    contentType := c.GetHeader("Content-Type")
    userAgent := c.GetHeader("User-Agent")
    authorization := c.GetHeader("Authorization")
    token := c.GetHeader("X-Auth-Token")
    变量 := c.Request.Header("数据名字")//获取所有请求头
    
    // 请求行信息
    method := c.Request.Method
    url := c.Request.URL.String()
    path := c.Request.URL.Path
    query := c.Request.URL.RawQuery
    protocol := c.Request.Proto
    
   // 绑定JSON到结构体
    if err := c.ShouldBindJSON(&user); err != nil {
        c.JSON(400, gin.H{"error": "JSON解析失败: " + err.Error()})
        return
    }
    
```



## 写入状态行，响应头，响应体

```go
    // 成功类
c.Status(http.StatusOK)           // 200 OK
c.Status(http.StatusCreated)      // 201 Created
c.Status(http.StatusNoContent)    // 204 No Content
// 重定向类
c.Status(http.StatusMovedPermanently)  // 301
c.Status(http.StatusFound)             // 302
c.Status(http.StatusSeeOther)          // 303
// 客户端错误类
c.Status(http.StatusBadRequest)        // 400
c.Status(http.StatusUnauthorized)      // 401
c.Status(http.StatusForbidden)         // 403
c.Status(http.StatusNotFound)          // 404
// 服务器错误类
c.Status(http.StatusInternalServerError) // 500
c.Status(http.StatusServiceUnavailable) // 503

// 设置Content-Type
c.Header("Content-Type", "application/json; charset=utf-8")
// 设置缓存控制
c.Header("Cache-Control", "no-cache, no-store, must-revalidate")
c.Header("Pragma", "no-cache")
c.Header("Expires", "0")
// 设置CORS头
c.Header("Access-Control-Allow-Origin", "*")
c.Header("Access-Control-Allow-Methods", "GET, POST, PUT, DELETE")
c.Header("Access-Control-Allow-Headers", "Content-Type, Authorization")
// 自定义头
c.Header("X-API-Version", "v1.0.0")
c.Header("X-Request-ID", "req-123456")

//字符串响应
c.String(200,"Hello,wrold")
//Json响应
    c.JSON(200, gin.H{"message": "Hello JSON"})
    
    // 结构体转JSON
    type User struct {
        Name string `json:"name"`
        Age  int    `json:"age"`
    }
    user := User{Name: "张三", Age: 25}
    c.JSON(200, user)
    
    // 不转义HTML的JSON
    c.PureJSON(200, gin.H{"message": "特殊字符: <>&"})



```

