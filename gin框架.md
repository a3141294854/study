# `gin`包

## 路由器建立，绑定，服务器启动

```go
r := gin.Default()
//自动中间件，包含一个自动日志，一个
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
    
```

## APi的获取

常用于用户管理

http://localhost:8000/user/name/action

```go
r.GET("/aaa/:name/*action", fun())
name := c.Param("name")        // 获取 :name 参数
action := c.Param("action")    // 获取 *action 参数
action = strings.Trim(action, "/")  // 去除 action 中的前后斜杠
c.String(http.StatusOK, name+" is "+action)  // 返回响应

```

## URL参数的获取(针对url后面的额外参数，可以被大众获取的)

```go
name := c.DefaultQuery("name", "枯藤")
name := c.Query("name")
// 查询的参数 + 不存在时返回的默认值
c.String(http.StatusOK, fmt.Sprintf("hello %s", name))
```

## 表单参数的获取

跟上面差不多

```go
types := c.DefaultPostForm("type", "post")
username := c.PostForm("username")
password := c.PostForm("userpassword")
```



## 上传文件

```go
/限制上传最大尺寸
    r.MaxMultipartMemory = 8 << 20
//基础单位是byte      bite,byte,KB,MB,GB,TB
//后面也可通过file.size获取大小，进而限制
    r.POST("/upload", func(c *gin.Context) {
        file, err := c.FormFile("file")
        //这里的file必须是前端代码中的name属性
        if err != nil {
            c.String(500, "上传图片出错")
        })
        c.SaveUploadedFile(file, file.Filename)
        //file.Filename  存储文件的原始名称
        //文件名 + 绝对路径或相对路径    
        c.String(http.StatusOK, file.Filename)
```

## 处理多组文件上传

```go
form, err := c.MultipartForm()
      if err != nil {
         c.String(http.StatusBadRequest, fmt.Sprintf("get err %s", err.Error()))
      }
      // 获取所有name属性为files的文件
      files := form.File["files"]
      // 遍历，不需要索引，所以_
      for _, file := range files {
         // 逐个存
         if err := c.SaveUploadedFile(file, file.Filename); err != nil {
            c.String(http.StatusBadRequest, fmt.Sprintf("upload err %s", err.Error()))
            return
         }
      }

```



## 路由分组

简化书写，方便管理

```go
v1 := r.Group("/v1")
   // {} 是书写规范，可以不写，但为了可读性
   {
      v1.GET("/login", login)
      v1.GET("submit", submit)
   }
//自动       /v1/login  /v1/submit
```

## 数据绑定和解析

建个结构体，用上标签，告诉电脑，从哪些结构中提取哪些数据，然后用函数，方便快捷

```go
type Login struct {
   // binding:"required"修饰的字段，必须要填入值，不然报错，返回错误信息
   User    string `form:"username" json:"user" uri:"user" xml:"user" binding:"required"`
   Pssword string `form:"password" json:"password" uri:"password" xml:"password" binding:"required"`
    //从各种信息结构中获取信息，比如从表单数据中获得username
}

var json Login
      // 将request的body中的数据，自动按照json格式解析到结构体
      //相应的还有ShouldBindForm 和万能但不安全的ShouldBind
      if err := c.ShouldBindJSON(&json); err != nil {
         // 返回错误信息
         // gin.H封装了生成json数据的工具
         c.JSON(http.StatusBadRequest, gin.H{"error": err.Error()})
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
c.Status(http.StatusMovedPermanently)  // 301永久重定向，保持原方法
c.Status(http.StatusFound)             // 302临时重新定向，保持元方法
c.Status(http.StatusSeeOther)          // 303同上，改为get，常用与对post的重定向
// 客户端错误类
c.Status(http.StatusBadRequest)        // 400
c.Status(http.StatusUnauthorized)      // 401
c.Status(http.StatusForbidden)         // 403
c.Status(http.StatusNotFound)          // 404
// 服务器错误类
c.Status(http.StatusInternalServerError) // 500执行错误
c.Status(http.StatusServiceUnavailable) // 503服务暂不可用

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

## 中间件

在响应之前进行的动作，比如身份认证等

```go
r.Use(MiddleWare())
//写在最前面就是注册全局中间件
//写在路由组下面就是对这个路由组生效
//或者也可以把函数写在处理程序的前面，地址的后面，只针对一个起效

func MiddleWare() gin.HandlerFunc {
    return func(c *gin.Context) {
        t := time.Now()
        fmt.Println("中间件开始执行了")
        // 设置变量到Context的key中，可以通过Get()取
        //就是存入上下文，用于中间件的交流信息
        c.Set("request", "中间件")
        status := c.Writer.Status()
        //获取即将发送的状态码
        fmt.Println("中间件执行完毕", status)
        t2 := time.Since(t)
        //返回从时间点t到现在的时间
        fmt.Println("time:", t2)
    }
}
func main() {
    r := gin.Default()
    r.Use(MiddleWare())
    {
        r.GET("/ce", func(c *gin.Context) {
            // 取值
            req, _ := c.Get("request")
            fmt.Println("request:", req)
            // 页面接收
            c.JSON(200, gin.H{"request": req})
        })

    }
    r.Run()
}


c.Next()
//程序运行到这里时，执行下一个中间件和响应程序，最后来执行，如果有多个，从最后推到最前，用来记录时间等
c.Abort()
//直接停止整个响应链条，要跟return一起连用
c.Redirect(http.StatusFound, "/login")
//重新定向到指定网址，要绝对路径
```

## COOKIE

用于检测是否是同一个客户端

```go
r.GET("/cookie", func(c *gin.Context) {
      // 获取客户端是否携带cookie
      cookie, err := c.Cookie("key_cookie")
      if err != nil {
         cookie = "NotSet"
         //cookie的名字
         //它的值
         // 给客户端设置cookie
         //  maxAge int, 单位为秒，存活的时间
         // path,cookie所在目录，就是访问什么的时候会发送这个cookie
         // domain string,域名
         //   secure 是否智能通过https访问
         // httpOnly bool  是否允许别人通过js获取自己的cookie
         c.SetCookie("key_cookie", "value_cookie", 60, "/",
            "localhost", false, true)
      }
      fmt.Printf("cookie的值是： %s\n", cookie)
   })
   r.Run(":8000")




//如果这样写的话，可以实现闭包，初始化参数，方便配置，最常用的
//在进行一次初始化后，每次直接调用作用函数
func AuthMiddleWare() gin.HandlerFunc {
	return func(c *gin.Context) {
		// 获取客户端cookie并校验
		if cookie, err := c.Cookie("abc"); err == nil {
			if cookie == "123" {
				c.Next()
				return
			}
		}
		// 返回错误
		c.JSON(http.StatusUnauthorized, gin.H{"error": "err"})
		// 若验证不通过，不再调用后续的函数处理
		c.Abort()
		return
	}
}
```

## Session

主要面向服务器，c.set()的高级版本，可以在被多个请求使用

所以还是要借助cookie，不过是通过cookie获取名字，值还是存储在服务端上，而不是浏览器上，安全点

```go
//注册一个引擎，填写加密密钥
//必须是 16、24 或 32 字节的长度（对应 AES-128、AES-192、AES-256）
store := cookie.NewStore([]byte("secret-key"))
//为所有请求配置中间件，就是应用到所有，自己顶定义一个session名字，加上引擎
c.Use(sessions.Sessions("mysession", store))
//在工作函数中获取sessions，第一个默认的
func (c *gin.context){
    session := sessions.Default(c)
    //获取指定名称的sessions
    sessions.DefaultMany(c, "名称")
}

//写入，保存，获取
session.Set("key", "value")
session.Save()
session.Get("key")

//删除单个,所有的记得保存
session.Delete()
//清空所有的
session.Clear()

//设置session过期时间
session.Options(sessions.Options{
    MaxAge: 3600, // 3600秒 = 1小时后过期
    // 会话期有效（浏览器关闭就失效）
    MaxAge: 0
})


```

## 日志记录

```go
f, _ := os.Create("gin.log")//创建文件
gin.DefaultWriter = io.MultiWriter(f, os.Stdout)//gin框架的日志输出
log.SetOutput(f)//log包的日志输出
```

