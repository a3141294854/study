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





