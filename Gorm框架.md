# Gorm框架

用于和数据库交互

## 启动数据库

```go
dsn := "root:@furenjie321@tcp(127.0.0.1:3306)/study?charset=utf8mb4&parseTime=True&loc=Local"
	db, err := gorm.Open(mysql.Open(dsn), &gorm.Config{})
	if err != nil {
		log.Fatal(err)
	}
```

## 创建表

```go
type User struct {
    ID       uint   `gorm:"primaryKey"` // 主键
    Name     string `gorm:"size:100"`     // 字符串字段，长度100
    Email    string `gorm:"uniqueIndex"` // 唯一索引
    Age      int
    Salary   float64
    IsActive bool `gorm:"default:true"` // 默认值
}

err := db.AutoMigrate(&User{})
    if err != nil {
        panic("创建表失败: " + err.Error())
    }//根据结构体，自动创建表

```

## 插入数据

```go
 user := User{Name: "张三", Email: "zhangsan@example.com", Age: 25}
    result := db.Create(&user)
    
    if result.Error != nil {
        panic(result.Error)
    }
```

## 删除数据

```go
result := db.Where("age > ? AND name LIKE ?", 25, "%张%").Delete(&User{})
//like是模糊搜索，这里表示的是名字中有张的，张%%，是头有张的，放在尾，就是以张结尾的，？是占位符
if result.Error != nil {
        panic(result.Error)
    }
```

## 查询数据

```go
var users []User
//创建一个存放User结构体的切片
db.Select("name, age").Where("age > ?", 25).Find(&users)
//把查询到的数据，放入users中，直接通过.访问
//如果有多组数据，遍历访问

```

## 更新数据

```go
db.Model(&User{}).Where("age = ?", 25).Update("name", "统一新名字")
```

## 创建索引

```go
type User struct {
    ID    uint   `gorm:"primaryKey"`
    Name  string `gorm:"index"` // 为 Name 字段创建普通索引
    Email string `gorm:"uniqueIndex"` // 为 Email 字段创建唯一索引
}

type User struct {
    ID        uint   `gorm:"primaryKey"`
    CompanyID int    `gorm:"index:idx_company_name"` // 属于复合索引 idx_company_name
    Name      string `gorm:"index:idx_company_name"` // 属于复合索引 idx_company_name
    Email     string `gorm:"uniqueIndex:uidx_company_email"` // 属于唯一复合索引 uidx_company_email
    CompanyCode string `gorm:"uniqueIndex:uidx_company_email"` // 属于唯一复合索引 uidx_company_email
}

```

## 事务

要么全部成功，要么全部失败

可以通过闭包，自动滚回

```
err := db.Transaction(func(tx *gorm.DB) error {
    // 在这个闭包内，使用 tx 执行所有数据库操作

    if err := tx.Create(&user1).Error; err != nil {
        // 返回任何错误，事务都会回滚
        return err
    }

    if err := tx.Model(&user2).Update("credit", 100).Error; err != nil {
        return err
    }

    // 返回 nil 提交事务
    return nil
})

// 检查事务是否成功
if err != nil {
    // 处理错误，事务已回滚
}
```

## 其它

```go
db.Model(&models.Luggage{})
//用于指定对哪个表进行操作

```

```go
//自动管理，创建时间，更新时间，软删除  Unscoped()查询软删除

CreatedAt time.Time
UpdatedAt time.Time
DeletedAt gorm.DeletedAt `gorm:"index"`

//为时间字段添加 json:"-" 标签，这样在序列化为 JSON 时会忽略这些字段
```

## 关联查询

用preload就是查询的结果中，有个结构体，这个结构体包含着另一个表的信息

用join就是手动选择条件

```go
1绑定外键
Guest Guest `gorm:"foreignKey:GuestID"`
2确实关联查询
Preload("Guest")
//差不多就是join
//join
Joins("JOIN guests ON luggages.guest_id = guests.id")




```

