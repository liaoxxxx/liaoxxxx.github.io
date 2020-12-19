### HTTP 协议
https://www.cnblogs.com/whf191/p/9712110.html

```

package main

import (
	"fmt"
	"net"
)

func main() {
	listen, _ := net.Listen("tcp", "127.0.0.1:8889")
	fmt.Println("server start listening on port 8889")
	for {
		conn, err := listen.Accept()
		if err != nil {
			fmt.Println(err)
		}
		go handleClient(conn)
	}


}

func handleClient(conn net.Conn) {
	addr := conn.RemoteAddr()
	fmt.Println(addr)

	respBody := "<h1>测试</h1>"

	length := int64(len(respBody))

	respHeader :="HTTP/1.1 200 OK\n"+
		"content-length: " +fmt.Sprintf("%d",  length) +"\n"+
		"content-type: text/html; charset=utf-8\n"

	respAll:=respHeader + "\n\r\n" + respBody
	println(respAll)
	_, _ = conn.Write([]byte (respAll))

	println(" server response end")
}

```
