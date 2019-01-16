# 纯Lua事件分发模块

## PushCenter.lua

可在`https://github.com/u0u0/Lua-utils/blob/master/src/app/utils/PushCenter.lua`获取热更新核心逻辑代码。

## 初始化

PushCenter 是单例设计的，为了避免被GC，导致内存存储的事件注册信息丢失，你需要把PushCenter 挂在一个全局变量上。如下：

```
app.PushCenter = require("app.utils.PushCenter")
```

## 注册监听

```
local PushCenter = require("app.utils.PushCenter")

PushCenter.addListener("LoginEvent", function(param)
	dump(param)
end, self)
```

其中 self 为监听者，当self销毁的时候，你需要注销监听。

```
function MainScene:onExit()
	PushCenter.removeListenersByTag(self)
end
```

removeListenersByTag可以一次性取消self的所有事件注册。

## 发送事件

```
PushCenter.pushEvent("LoginEvent", "this is param")
```