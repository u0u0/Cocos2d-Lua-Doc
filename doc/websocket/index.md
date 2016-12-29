# WebSocket 使用说明

WebSocket 是 HTML5 开始提供的一种浏览器与服务器间进行全双工通讯的网络技术。在 WebSocket API 中，浏览器和服务器只需要做一次握手的动作，然后浏览器和服务器之间形成一条快速通道，通过快递通道两者之间可以互相传送数据。

Cocos2d-x 引擎集成 libwebsockets，并在 libwebsockets 的客户端 API 基础上封装了一层易用的接口，使得引擎在 C\+\+, JS, Lua 层都能方便的使用 WebSocket 来进行游戏的网络通讯。

## 基本用法

创建一个网络连接如下：

```
socket = cc.WebSocket:create(url)
```

网络通信的异步状态回调需要注册事件回调函数：

```
socket:registerScriptHandler(handlerFunc, eventType)
```

事件类型有四种：

* cc.WEBSOCKET_OPEN 握手成功，通道打开
* cc.WEBSOCKET_MESSAGE 接收到服务器信息
* cc.WEBSOCKET_CLOSE 通道关闭
* cc.WEBSOCKET_ERROR 其它错误

handlerFunc 需要类型转换处理，后面的示例将展示。

发送数据到服务器：

```lua
socket:sendString(str)
```

客户端可以主动关闭网络连接：

```lua
socket:close()
```

## 完整示例

测试 WebSocket，你依然需要自己搭建一个服务器，后面提供了 Golang 编写了一个配套的 WebSocket 服务器 demo 供客户端测试连接。

在 MainScene 中加入如下的代码：

```
function MainScene:ctor()
	self:performWithDelay(function ()
		self.socket = cc.WebSocket:create("wss://127.0.0.1:1234")

		if self.socket then
			self.socket:registerScriptHandler(handler(self, self.onOpen_), cc.WEBSOCKET_OPEN)
			self.socket:registerScriptHandler(handler(self, self.onMessage_), cc.WEBSOCKET_MESSAGE)
			self.socket:registerScriptHandler(handler(self, self.onClose_), cc.WEBSOCKET_CLOSE)
			self.socket:registerScriptHandler(handler(self, self.onError_), cc.WEBSOCKET_ERROR)
		end
	end, 1.0)
end

function MainScene:onOpen_()
	print("onOpen")
	self.socket:sendString("Hello server, i'm Quick websocket")
	-- delay to close
	self:performWithDelay(function () self.socket:close() end, 1.0)
end

function MainScene:onMessage_(message)
	print("onMessage:" .. message)
end

function MainScene:onClose_()
	print("onClose")
	self.socket = nil
end

function MainScene:onError_(error)
	print("onError:" .. error)
end
```

服务器 url 包含ip地址（或域名）和端口号，这里把套接字对象保存到场景中，以便回调函数访问。

registerScriptHandler 注册的事件回调函数，第一个参数需要使用 Quick框架提供的 handler 函数做转换，主要到这里的 `self.onOpen_` 用的是*点*调用方法，handler 的作用就是让 self 自动传入 `self.onOpen_`，以防止出现 "Invalid self" 这样的错误。

注意，我需要收到 cc.WEBSOCKET_OPEN 事件后才能向服务器发送数据。在 `MainScene:onOpen_()` 中使用 `self:performWithDelay` 来延迟关闭套接字通道，目的是让前面的 `self.socket:sendString()` 网络过程走完。

用上面的代码测试我们提供的 golang 服务器，客户端的 log 信息如下：

```
[WebSocket::init] _host: 127.0.0.1, _port: 1234, _path: /
[1422189039:5365] NOTICE: Initial logging level 7
[1422189039:5365] NOTICE: Library version: 1.3 66b3e53
[1422189039:5365] NOTICE:  Started with daemon pid 0
[1422189039:5365] NOTICE:  static allocation: 4472 + (16 x 2560 fds) = 45432 bytes
[LUA-print] onOpen
[websocket:send] total: 33, sent: 0, remaining: 33, buffer size: 33
[websocket:send] bytesWrite => 33
[LUA-print] onMessage:Hello client!
websocket (0x7fac80a3cbe0) connection closed by client
connection closing..
[LUA-print] onClose
```

## Golang 测试服务器代码

完整代码如下：

```
package main

import (
	"code.google.com/p/go.net/websocket"
	"log"
	"net/http"
)

func wsHandler(ws *websocket.Conn) {
	var err error

	// need loop to keep socket connect
	for {
		var reply string

		if err = websocket.Message.Receive(ws, &reply); err != nil {
			log.Printf("connect closed!")
			break
		}

		// client message
		println(reply)

		if err := websocket.Message.Send(ws, "Hello client!"); err != nil {
			log.Printf("Send fail")
		}
	}
}

func main() {
	log.Print("starting socket server ...")
	http.Handle("/", websocket.Handler(wsHandler))
	if err := http.ListenAndServeTLS(":1234", "cert.pem", "key.pem", nil); err != nil {
		// if err := http.ListenAndServe(":1234", nil); err != nil {
		log.Fatal("ListenAndServe:", err)
	}
}
```

服务器使用wss方式，需要自签名证书。

在Mac下生成签名证书的命令如下：

```
$openssl genrsa -out key.pem 2048
$openssl req -new -x509 -key key.pem -out cert.pem -days 3650
```

## 关于wss安全连接

由于苹果最新审核要求所有网络请求加入SSL安全链接，Quick社区版从 [commit 617427c99760467a7814993a643c91312ad87f00](https://github.com/u0u0/Quick-Cocos2dx-Community/commit/617427c99760467a7814993a643c91312ad87f00)
开始支持 WebSocket 的安全连接。