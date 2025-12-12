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

## Lua脚本

```lua
-- 变量声明
local key = "mykey"
local count = 10
local isActive = true

-- 数组
local keys = {"key1", "key2", "key3"}

-- 表（类似字典）
local limiter = {
    tokens = 10,
    lastFillTime = 1640995200000,
    capacity = 100
}


if limiter.tokens > 0 then
    limiter.tokens = limiter.tokens - 1
    result = 1
else
    result = 0
end

-- Redis命令调用
redis.call('SET', key, value)
redis.call('GET', key)
redis.call('INCR', key)
redis.call('EXPIRE', key, 60)


-- 将表转换为JSON
local jsonStr = cjson.encode(limiter)

-- 将JSON解析为表
local data = cjson.decode(jsonStr)

-- 将字符串转换为数字
local num = tonumber("123")  -- 结果：123
```

向脚本传递，执行脚本

```go
// Go代码
result, err := s.RdbLim.Eval(
    context.Background(),
    luaScript,
    []string{"limiter:local"},  // KEYS
    100, 5000, 1640995200000,  // ARGV
).Result()
```



## 完整例子

```go
func LimiterAllow(Name string, s *services.Services, c *gin.Context) bool {
	luaScipt := `
--获取数据
local data = redis.call('GET', KEYS[1])

if not data then
	return {0, 0}
end

--解析
local limiter = cjson.decode(data)
--计算
local elapsed = tonumber(ARGV[1]) - limiter.LastFillTime 
local count = math.floor(elapsed / limiter.FillRate)
limiter.Tokens = math.min(limiter.Tokens + count, limiter.Capacity)
limiter.LastFillTime = tonumber(ARGV[1])
--检查
local flag = 0
if limiter.Tokens > 0 then
	limiter.Tokens = limiter.Tokens - 1
	flag = 1
end
--序列化
local insert = cjson.encode(limiter)
--更新
redis.call('SET', KEYS[1], insert)
return {flag, limiter.Tokens}
 `
	result, err := s.RdbLim.Eval(c, luaScipt, []string{Name}, time.Now().UnixNano()).Result()
	if err != nil {
		logger.Logger.WithFields(logrus.Fields{
			"name":  Name,
			"error": err,
		}).Error("限流器执行脚本失败")
		return false
	}
	results := result.([]interface{})
	flag := results[0].(int64)
	tokens := results[1].(int64)
	logger.Logger.WithFields(logrus.Fields{
		"name":   Name,
		"tokens": tokens,
	}).Debug("限流器状态")

	if flag == 1 {
		return true
	} else {
		return false
	}
}
```

