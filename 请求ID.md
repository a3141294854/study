# 请求ID

## 高贵的包

```go
import "github.com/google/uuid"

func generateRequestID() string {
    // 生成UUID v4
    return uuid.New().String()
}
```

## 中间件

```go
// RequestIDMiddleware 请求ID中间件
func RequestIDMiddleware() gin.HandlerFunc {
	return func(c *gin.Context) {

		requestID := c.GetHeader("X-Request-ID")
		if requestID == "" {
			requestID = generateRequestID()
		}

		c.Set("request_id", requestID)

		c.Header("X-Request-ID", requestID)

		c.Next()
	}
}

func generateRequestID() string {
	// 生成UUID v4
	return uuid.New().String()
}

// LogRequest 在日志中使用请求ID
func LogRequest() gin.HandlerFunc {
	return func(c *gin.Context) {
		start := time.Now()

		c.Next()

		requestID, _ := c.Get("request_id")
		logger.Logger.Info("请求处理完成",
			"request_id", requestID,
			"method", c.Request.Method,
			"path", c.Request.URL.Path,
			"status", c.Writer.Status(),
			"duration", time.Since(start),
		)
	}
}
```

