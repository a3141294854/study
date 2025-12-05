# Redis数据库

## 链接

```go
rdb := redis.NewClient(&redis.Options{
		Addr:     "localhost:6379",
		Password: "",
		DB:       0,
	})
```

## 增删改查

```go
// 1. 添加数据
rdb.Set(c, "key", "value", 0)  // 0 表示永不过期

// 2. 获取数据
value,err := rdb.Get(c, "key").Result()
value:= rdb.Get(c, "key").Val()

// 3. 修改数据
rdb.Set(c, "key", "new value", 0)

// 4. 删除数据
rdb.Del(c, "key")

```

## 存结构体

转成成json格式，可以存进去

```go
import "encoding/json"

// 序列化
data, err := json.Marshal(yourStruct)
if err != nil {
    // 处理错误
}

// 反序列化
var yourStruct YourStruct
err = json.Unmarshal(data, &yourStruct)
第一个参数：必须是 []byte （不能是string）
if err != nil {
    // 处理错误
}

```

## 缓存

1为了防止雪崩，存储时间要加绝对值

## 限流

写的是令牌桶限流，加互斥锁。限流器的设置存到redis中，name传递

```go
func NewTokenBucketLimiter(name string, capacity int, fillRate time.Duration, s *services.Services) {
	limiter := models.TokenBucketLimiter{
		Capacity:     capacity,
		FillRate:     fillRate,
		Tokens:       capacity,
		LastFillTime: time.Now(),
	}
	insert, err := json.Marshal(limiter)
	if err != nil {
		log.Println(name, "限流器序列化失败", err)
		return
	}
	s.RdbLim.Set(context.Background(), name, string(insert), 0)
}

func CreatLock(name string, s *services.Services) bool {
	lock, err := s.RdbLim.SetNX(context.Background(), name+"locked", true, 100*time.Millisecond).Result()
	if err != nil {
		log.Println(name, "锁创建失败", err)
		return false
	}
	if lock {
		return true
	} else {
		return false
	}
}

// LimiterAllow 检查是否允许请求通过，返回布尔值
func LimiterAllow(Name string, s *services.Services, c *gin.Context) bool {
	result := CreatLock(Name, s)
	defer s.RdbLim.Del(c, Name+"locked")
	if !result {
		//log.Println(Name, "有锁")
		return false
	}

	temp := s.RdbLim.Get(c, Name).Val()
	var limiter models.TokenBucketLimiter
	err := json.Unmarshal([]byte(temp), &limiter)
	if err != nil {
		log.Println(Name, "限流器反序列化失败", err)
		return false
	}
	now := time.Now()
	count := int(now.Sub(limiter.LastFillTime) / limiter.FillRate)
	limiter.Tokens = min(limiter.Tokens+count, limiter.Capacity)
	limiter.LastFillTime = now
	flag := true
	if limiter.Tokens > 0 {
		limiter.Tokens--
		flag = true
	} else {
		flag = false
	}

	insert, err := json.Marshal(limiter)
	if err != nil {
		log.Println(Name, "限流器序列化失败", err)
		return false
	}
	s.RdbLim.Set(c, Name, string(insert), 0)
	//log.Println(Name, "限流器", limiter.Tokens)
	return flag
}
```





