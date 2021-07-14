## websocket

### 1. 简单使用

#### 1.1 使用 `github.com/gorilla/websocket.Upgrader`  升级到websocket 协议

```go
package api

import (
	"github.com/gorilla/websocket"
	"net/http"
	"time"
)

const (
	// Time allowed to write a message to the peer.
	writeWait = 10 * time.Second

	// Time allowed to read the next pong message from the peer.
	pongWait = 60 * time.Second

	// Send pings to peer with this period. Must be less than pongWait.
	pingPeriod = (pongWait * 9) / 10

	// Maximum message size allowed from peer.
	maxMessageSize = 512
)

var Upgrader = websocket.Upgrader{
	ReadBufferSize:  1024,
	WriteBufferSize: 1024,
	CheckOrigin: func(r *http.Request) bool {
		return true
	},
}
```


#### 2  获得 `Upgrader后的 websocket客户端链接句柄`,进行简单的收发信息
```go
package recycler

import (
	"fmt"
	"github.com/gin-gonic/gin"
	"github.com/gorilla/websocket"
	"goApi/internal/app/websocket/api"
	"goApi/internal/repository"
	"goApi/pkg/logger"
	"goApi/pkg/util/helper"
	"log"
)

type orderServer struct{}

var OrderServer orderServer

//客户端列表 map
var OrderDistributeClients = make(map[int64]map[int64]*websocket.Conn)

func (*orderServer) OrderDistribute(ctx *gin.Context) {
	recerId := helper.GetRecIdByCtx(ctx)
	working, err := repository.RecyclerWorkingRepo.FindByUid(recerId)
	if err != nil {
		logger.Logger.Warn(err.Error())
		return
	}
	cityId := working.CityId
	wrt := ctx.Writer
	req := ctx.Request
	//1.获得 Upgrader 后的 websocket客户端链接句柄
	wsConn, err := api.Upgrader.Upgrade(wrt, req, nil)

	if err != nil {
		logger.Logger.Warn(err.Error())
		return
	}
	//2.存储 websocket客户端链接句柄 起来
	cityMap := OrderDistributeClients[cityId]
	if cityMap == nil {
		OrderDistributeClients[cityId] = map[int64]*websocket.Conn{recerId: wsConn}
	} else {
		OrderDistributeClients[cityId][recerId] = wsConn
	}
	logger.Logger.Info(fmt.Sprintf("OrderDistributeClients city-code: %v lenght is %v  ", cityId, len(OrderDistributeClients[cityId])))
	for {
		// 3. 读取客户端发过来的信息
		mt, message, err := wsConn.ReadMessage()
		if err != nil {
			log.Println("read message:", err)
			break
		}
		log.Printf("receive message : %s", message)
		// 4.向客户端发回接收到的信息
		err = wsConn.WriteMessage(mt, message)
		if err != nil {
			log.Println("write message:", err)
			break
		}
	}
}

```






<br>

*****************************************


###常见错误

#### 1 . `2021/07/15 02:03:49 read message: websocket: close 1006 (abnormal closure): unexpected EOF` 

> 原因 ：客户端与服务端连接已经断开,所以无法读取到有效的信息

#### 解决方案一 延长超时时间   `proxy_read_timeout 1200s;`
```config
server
{
    listen 80;
    listen [::]:80;
    server_name rec_ws.liaoxx.top;
    index index.php index.html index.htm default.php default.htm default.html;
    root /www/wwwroot/lj_recycle/goApi;
    
    
     location / {
           proxy_pass http://localhost:8010; 
           proxy_read_timeout 1200s;    //延长超时时间
           proxy_http_version 1.1;
           proxy_set_header Upgrade $http_upgrade;
           proxy_set_header Connection 'Upgrade';
        }    
  
}

```


#### 解决方案二 :   在 proxy_read_timeout 超时之前 客户端与服务端之间定时发送 ping 心跳包  