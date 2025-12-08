# RBAC权限

把权限赋给角色，把角色赋给用户

## 表的设计

1用户表，2角色表，3权限表，4用户和角色关联表，5角色和权限关联表

```go

type Employee struct {
	ID       uint   `gorm:"primaryKey;autoIncrement"`
	Name     string `json:"name" gorm:"column:name"`
	User     string `json:"user" gorm:"column:user"`
	Password string `json:"password" gorm:"column:password"`

	RoleID uint `json:"role_id" gorm:"column:role_id"`
	Role   Role `gorm:"foreignKey:RoleID"`
    //创建一个 一对多表
}
type Role struct {
	ID   uint   `gorm:"primaryKey;autoIncrement"`
	Name string `json:"name" gorm:"column:name"`

	Permissions []Permission `gorm:"many2many:role_permission"`
    //创建一个多对多表
    //一个角色可以有多个权限，一个权限可以被赋给多个角色
}

type Permission struct {
	ID   uint   `gorm:"primaryKey;autoIncrement"`
	Name string `json:"name" gorm:"column:name"`
}
```

## 中间件检查

```go
//获取用户id，找到相应角色的id，然后把相应的权限直接丢到声明中
func AuthCheck(s *services.Services) gin.HandlerFunc {
	return func(c *gin.Context) {
		claims, ok := c.Get("claims")
		if !ok {
			c.JSON(http.StatusUnauthorized, gin.H{
				"success": false,
				"message": "请先登录",
			})
			log.Println("没找到声明")
			c.Abort()
			return
		}
		e := claims.(*util.AccessClaims)
		var employee models.Employee
		s.DB.Model(&models.Employee{}).Preload("Role").Where("id = ?", e.UserId).First(&employee)
		var role models.Role
		s.DB.Model(&models.Role{}).Preload("Permissions").Where("id = ?", employee.RoleID).First(&role)
		for _, v := range role.Permissions {
			c.Set(v.Name, 1)
		}
		c.Next()
	}
}

//通过声明检查相应权限的，要给个权限名字
func CheckAction(name string) gin.HandlerFunc {
	return func(c *gin.Context) {
		_, ok := c.Get(name)
		if !ok {
			c.JSON(http.StatusUnauthorized, gin.H{
				"success": false,
				"message": "没有权限",
			})
			c.Abort()
			return
		}
		c.Next()
	}
}






```

