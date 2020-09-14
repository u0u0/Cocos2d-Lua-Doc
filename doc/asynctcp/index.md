# AsyncTCP

> 从 Cocos2d-Lua-Community 4.0.1 开始提供。

LuaSocket配合SimpleTCP的封装，已可适应大部分应用情况，不过依然有不足的地方：

* LuaSocket 的connect不是异步的，可能被阻塞，这是超时是不确定的。
* settimeout(0)不是真正的异步。缺乏独立线程收发数据在大数据的时候可能堆到一个update中。
* 单纯的长链接用法，LuaSocket框架不够精简，过多封装。

AsyncTCP 意在替代SimpleTCP，提供一个简洁的异步TCP通道。

## AsyncTCP 解决的问题

1. 为游戏中的 TCP 长连接而设计。
2. 减少Lua与CPP的交互。
3. 收发数据和包处理，减少Lua层的数据拼接，减少send io操作。
4. 异步的connect。

## AsyncTCP 不处理的问题

1. 不解决TCP粘包、半包。
2. 不解决心跳包。

## AsyncTCP 用法接口

1. 创建实例

	```
	self.asyncTCP = AsyncTCP:create()
	```

2. 设置事件回调方法

	```
	self.asyncTCP:setEventCB(handler(self, self.onTCPEvent))
	```

3. 连接服务器

	```
	self.asyncTCP:open("127.0.0.1", 1234, 5)
	```
	
	参数1:服务器地址，参数2:服务器端口，参数3: 连接超时时间(秒)

4. 主动断开连接

	```
	self.asyncTCP:close()
	```
	
	断开是异步操作，需要等待EVENT_CLOSED事件才能进行下一步操作。

5. 发送数据（对象方法）

	```
	self.asyncTCP:send("Hello AsyncTCP")
	```

## 回调函数说明

state状态说明:

* EVENT_CONNECTING, 连接中.
* EVENT_FAILED, 连接失败.
* EVENT_CONNECTED, 连接成功，可以开始发送数据.
* EVENT_CLOSED, 连接断开.
* EVENT_DATA, 收到服务器数据，data为数据.

```
-- sync from AsyncTCP.h
local EVENT_CONNECTING = 0
local EVENT_FAILED = 1
local EVENT_CONNECTED = 2
local EVENT_CLOSED = 3
local EVENT_DATA = 4

function TestCase:onTCPEvent(state, data)
	if state == EVENT_CONNECTING then
		print("** AsyncTCP connecting **")
	elseif state == EVENT_FAILED then
		print("** AsyncTCP failed **")
		self.asyncTCP = nil
	elseif state == EVENT_CONNECTED then
		self.asyncTCP:send("Hello AsyncTCP")
	elseif state == EVENT_DATA then
		print(data)
	elseif state == EVENT_CLOSED then
		print("** AsyncTCP closed **")
		self.asyncTCP = nil
	end
end
```

## 测试用例

[Test_AsyncTCP.lua](https://github.com/u0u0/Cocos2d-Lua-Community/blob/master/tests/src/app/views/Test_AsyncTCP.lua)