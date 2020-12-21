##| 跨域

### 1.实现中间件
```
package middleware

import (
	"fmt"
	"github.com/gin-gonic/gin"
)

func Cors() gin.HandlerFunc {
	return func(c *gin.Context) {
		method := c.Request.Method

		c.Header("Access-Control-Allow-Origin", "*")
		c.Header("Access-Control-Allow-Methods", "GET,POST,PATCH,PUT,DELETE,OPTIONS,DELETE")
		c.Header("Access-Control-Allow-Credentials", "true")

		c.Header("Access-Control-Allow-Headers", "Authori-zation,Authorization, Content-Type, If-Match, If-Modified-Since, If-None-Match, If-Unmodified-Since, X-Requested-With, AccessToken,X-CSRF-Token, Authorization, Token, token")

		c.Header("Access-Control-Max-Age", "1728000")
		c.Header("Content-Type", "application/json; charset=utf-8")
		c.Header("Access-Control-Expose-Headers", "Content-Length, Access-Control-Allow-Origin, Access-Control-Allow-Headers, Content-Type")


		//放行所有OPTIONS方法
		if method == "OPTIONS" {
			if method == "OPTIONS" {
            	c.AbortWithStatus(http.StatusNoContent)
            }
		}

		//处理请求
		c.Next()

	}
}
```

### 2.调用中间件

```
package router

import (
	"github.com/gin-gonic/gin"
	. "goApi/app/controller"
	"goApi/app/middleware"
)

var router *gin.Engine

func InitRouter() *gin.Engine {
	router = gin.Default()
       
    //###3.1  有效
	router.Use(middleware.Cors())
	router.GET("/", Index)

	//###3.1 无效
	homeGroup := router.Group("/home",middleware.Cors())
	{
		homeGroup.POST("/skeleton", Skeleton)
	}

	return router
}
```

### 3. 调用中间件的时候  `router.Use(middleware.Cors())`   , `homeGroup := router.Group("/home",middleware.Cors())` 在路由分组中调用该中间件无效