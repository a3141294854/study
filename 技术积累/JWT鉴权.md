

# JWT鉴权

通过发送令牌（自己编写的规范的结构体），让客户端通过令牌验证，通过两个令牌实现体验优化

## 制造令牌

```go

//创造密钥
var JwtSecret = []byte("secret")

//建立令牌结构体
type  CustomClaims struct {
	UserId uint `json:"user_id"`
	UserName string `json:"user_name"`
	jwt.RegisteredClaims
}

//制造令牌
func GenerateToken(userId uint, userName string) (string, error) {
	claims := CustomClaims{
		UserId: userId,
		UserName: userName,
		RegisteredClaims: jwt.RegisteredClaims{
			ExpiresAt: jwt.NewNumericDate(time.Now().Add(time.Hour * 24)),
		},
	}
    //选择加密算法
	token := jwt.NewWithClaims(jwt.SigningMethodHS256, claims)
    //创立令牌
	tokenString, err := token.SignedString(JwtSecret)
	if err != nil {
		return "", err
	}
	return tokenString, nil

}


```

## 获取

```go
// JwtCheck jwt检查中间件
func JwtCheck() gin.HandlerFunc {
	return func(c *gin.Context) {
		authHeader := c.GetHeader("Authorization")
		if authHeader == "" {
			c.JSON(http.StatusUnauthorized, gin.H{
				"success": false,
				"message": "请先登录",
			})
			c.Abort()
			return
		}

		tokenString := strings.TrimPrefix(authHeader, "Bearer ")
		if tokenString == authHeader {
			c.JSON(http.StatusUnauthorized, gin.H{
				"success": false,
				"message": "Authorization格式应为Bearer {token}",
			})
			c.Abort()
			return
		}

		claims, err := util.ParseAccessToken(tokenString)
		if err != nil {
			c.JSON(http.StatusUnauthorized, gin.H{
				"success": false,
				"message": "token无效",
			})
			c.Abort()
			return
		}
		c.Set("claims", claims)
		c.Next()
	}
}
```



## 解析

```go
// ParseToken 解析JWT
func ParseToken(tokenString string) (*CustomClaims, error) {
	// 解析token，token变为CustomClaims结构体
	token, err := jwt.ParseWithClaims(tokenString, &CustomClaims结构体}, func(token *jwt.Token) (interface{}, error) {
		return jwtSecret, nil
	})
    
    //错误
	if err != nil {
		return nil, err
	}

	// 验证token并获取claims
	if claims, ok := token.Claims.(*CustomClaims); ok && token.Valid {
		return claims, nil
	}
    
    //返回错误
	return nil, errors.New("invalid token")
}
```

具体结构，过期时间必填

```go
type RegisteredClaims struct {
    // 签发者（Issuer）
    Issuer string `json:"iss,omitempty"`
    
    // 主题（Subject），通常是用户ID
    Subject string `json:"sub,omitempty"`
    
    // 接收者（Audience）
    Audience []string `json:"aud,omitempty"`
    
    // 过期时间（Expiration Time）
    ExpiresAt *NumericDate `json:"exp,omitempty"`
    
    // 生效时间（Not Before）
    NotBefore *NumericDate `json:"nbf,omitempty"`
    
    // 签发时间（Issued At）
    IssuedAt *NumericDate `json:"iat,omitempty"`
    
    // JWT ID（唯一标识符）
    ID string `json:"jti,omitempty"`
}
```

