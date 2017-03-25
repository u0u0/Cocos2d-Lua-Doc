# SimpleTCP 设计与实现

Quick-Cocos2dx-Community 中集成了 LuaSocket，为网络游戏提供 Socket 层的连接支撑。

LuaSocket 本身是跨平台的封装，支持 TCP/UDP，但本身使用并不方便。于是 Quick 中集成了一个 SocketTCP的高层封装模块，用来提高易用性。SocketTCP 最早来源于 Quick 论坛中的一份实现，后被 zrong 改进，而 Quick 社区版进一步改进了 IPV6 的网络适应与轮训频率。不过 SocketTCP 的代码质量依然不高，3个调度器直接的切换让人看得眼花缭乱。本着精益求精的精髓，我着手重新设计 TCP 的封装层，并命名为 SimpleTCP。

## SimpleTCP 解决的问题

1. 为游戏中的 TCP 长连接而设计。
2. 封装基于 LuaSocket 的 TCP 模式，使用 settimeout(0) 实现异步 TCP 调用。
3. 屏蔽 LuaSocket receive() 数据读取的一些坑。
4. 易于理解的事件与状态抽象，调用者只需关心事件通知，不用去理会内部状态变化。

## SimpleTCP 不解决的问题

1. 不解决 TCP 的粘包问题，这个问题属于需要与服务器协商定义包才能做的。
2. 不解决心跳包问题，这也是需要与服务器协商定义的。

## SimpleTCP 状态图

SimpleTCP 内部按照下面的状态图而设计，理解图就理解了 SimpleTCP 的用法。

![SimpleTCP 状态机](./SimpleTCP.png)

状态说明：

1. `self.stcp = SimpleTCP.new()` 初始化于 Inited 状态，这个状态无需记录。
2. `self.stcp:connect()` 到达 Connecting 状态。
3. Connecting 下自动达到 Connected 或 Failed 状态。
4. Connected 状态下可以正常收发数据。如果用户主动 `self.stcp:close()` 或 服务器端关闭 socket，触发状态切换到 Closed。
5. Failed 状态下可以 `self.stcp:connect()` 进行重连。
6. Closed 状态下可以 `self.stcp:connect()` 进行重连。

事件说明：

1. SimpleTCP.EVENT_CONNECTING，正在连接中，不能做任何操作。
2. SimpleTCP.EVENT_FAILED，连接失败，可重连。
3. SimpleTCP.EVENT_CONNECTED，连接成功，可收发数据。
4. SimpleTCP.EVENT_CLOSED，连接关闭，可重连。
5. SimpleTCP.EVENT_DATA，收到数据，应用层自行处理粘包、数据协议解包、数据分发。

## SimpleTCP 提供的接口

1. 导入模块

	```
	local SimpleTCP = require("framework.cc.net.SimpleTCP")
	```

2. 创建对象（类方法）

	```
	self.stcp = SimpleTCP.new("192.168.0.27", 1234, handler(self, self.onTCPEvent))
	```

	* 参数1：服务器地址（IPV6下，建议都是用域名）
	* 参数2：端口号。
	* 参数3：事件回调函数

3. 连接服务器（对象方法）

	```
	self.stcp:connect()
	```

4. 主动断开连接（对象方法）

	```
	self.stcp:close()
	```

5. 发送数据（对象方法）

	```
	self.stcp:send(string)
	```

	* 参数1：二进制流数据。如果需要拼接二进制数据，可使用引擎提供的 ByteArray 模块。

## 完整测试代码

```
local MainScene = class("MainScene", function()
	return display.newScene("MainScene")
end)

local SimpleTCP = require("framework.cc.net.SimpleTCP")

function MainScene:ctor()
	self.stcp = SimpleTCP.new("192.168.0.27", 1234, handler(self, self.onTCPEvent))
	self.stcp:connect()

	-- Reconnect
	local button = display.newSprite("connect.png"):center():addTo(self)
	button:setTouchEnabled(true)
	button:addNodeEventListener(cc.NODE_TOUCH_EVENT, function(event)
		if event.name == "began" then
			self.stcp:connect()
			return true
		end
	end)

	-- close for Reconnet testing
	self:performWithDelay(function()
		self.stcp:close()
	end, 3)
end

function MainScene:onTCPEvent(even, data)
	if even == SimpleTCP.EVENT_DATA then
		print("==receive data:", data)
	elseif even == SimpleTCP.EVENT_CONNECTING then
		print("==connecting")
	elseif even == SimpleTCP.EVENT_CONNECTED then
		print("==connected")
		self.stcp:send("Hello server, i'm SimpleTCP")
	elseif even == SimpleTCP.EVENT_CLOSED then
		print("==closed")
		-- you can call self.stcp:connect() again or help user exit game.
	elseif even == SimpleTCP.EVENT_FAILED then
		print("==failed")
		-- you can call self.stcp:connect() again or help user exit game.
	end
end

function MainScene:onEnter()
end

function MainScene:onExit()
end

return MainScene
```

## 代码下载

[SimpleTCP](./SimpleTCP.lua)

[Go测试服务器代码](./main.go)