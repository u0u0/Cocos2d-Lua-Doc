# 在 Lua 中使用 luaj 直接调用 Android 的 Java 代码

通常来说，Android的各种 SDK 都是为应用服务的，基于 Java 实现的封装。
以前在 Cocos2d-lua 中接入 Android SDK 需要先封装C wrapper层，再封装Lua binding层，效率低下。为提高效率， Quick 引入了 LuaJavaBridge，可直接在 Lua 端调用 Java 代码，并支持 Java 回调 Lua 函数。接入SDK 不再需要编写 C 代码，luaj 这个黑科技让 SDK 的接入效率成倍提升。

## 内部机制

Cocos2d-x 在`C++`层提供得有一个JniHelper的类，可以方便的在`C++`端调用  Android 的 Java 接口。它使用 Android NDK 提供的反向调用机制，一个 Java 函数的调用过程大致如下：

```
jclass myclass = env->FindClass(className);
jmethodID myFunc = env->GetMethodID(myclass, functionName, argType);
env->CallStaticObjectMethod(myclass, myFunc, args);
```

从 NDK 提供的接口可以看出，我们只需要把以上几个函数做接口绑定，就能实现在Lua端调用 Java 函数。当然，luaj 的实现要复杂得多，它需要考虑各种类型参数的传递，返回值的处理，以及配套的 Java 反向调用 Lua 的模块。

## luaj 特性

* 从 Lua 调用 Java Class 里的 Static Method。
* 支持`int/float/boolean/String/Lua function` 五种参数类型。
* Lua function 作为参数传递给 Java，可实现 Java 端反向调用Lua函数。
* 从 Java 调用 Lua 的全局函数，或者指定函数。

## [luaj API reference](../../api/luaj/index.md)

## 微信 SDK 示例

分享朋友的 Lua 接口：

```
function MainScene:shareFriend()
	local function callback(result)
		print(result)
		if result == "0" then
			self.rtnLable:setString("Return:Success")
		elseif result == "1" then
			self.rtnLable:setString("Return:Cancel")
		elseif result == "2" then
			self.rtnLable:setString("Return:Denied")
		else
			self.rtnLable:setString("Return:Default error")
		end
	end

	local className = "org/cocos2dx/lua/AppActivity"
	local args = {
		"Share info",
		callback
	}
	luaj.callStaticMethod(className, "shareFriend", args)
end
```

当然，你需要在 Java 端有对应的代码配合, 在`frameworks/runtime-src/proj.android/app/src/main/java/org/cocos2dx/lua/AppActivity.java`中对应的静态函数如下：

```
	// share to friend
	public static void shareFriend(String text, final int luaFunctionId) {
		LuaId = luaFunctionId;

		WXTextObject textObj = new WXTextObject();
		textObj.text = text;

		WXMediaMessage msg = new WXMediaMessage();
		msg.mediaObject = textObj;
		msg.description = text;

		SendMessageToWX.Req req = new SendMessageToWX.Req();
		req.transaction = String.valueOf(System.currentTimeMillis());
		req.message = msg;

		api.sendReq(req);
	}
```

> 注：微信SDK的其它初始化代码，以及项目配置不在本文讨论范围之内。

另一个判断是否有安装微信的接口实例：

Lua 代码：

```
function MainScene:hasWeChat()
	local className = "org/cocos2dx/lua/AppActivity"
	local sig = "()Z"
	local callRtn, javaRtn = luaj.callStaticMethod(className, "hasWeChat", nil, sig)
	return javaRtn
end
```

Java代码：

```
	public static boolean hasWeChat() {
		if (api.isWXAppInstalled()) {
			return true;
		} else {
			return false;
		}
	}
```

在 hasWeChat 中，Java 端有返回值到 Lua 端，注意到在 Lua 端接收到的是2个返回值。

* 第一个返回值是`luaj.callStaticMethod`是否成功。
* 第二个返回值才是 hasWeChat 的。

由于我们需要返回值，所以这里必须指定`local sig = "()Z"`作为第四个参数，以保证 luaj 正确找到 Java 对应的函数。

## 方法签名

关于 NDK 调用 Java 的方法签名，详情参考 Android官方文档。这里列举一些常用的函数签名如下表：

| 签名 | 说明 |
|-----|------|
| ()V | 参数：无；返回值：无 |
| (I)V | 参数：int；返回值：无 |
| (Ljava/lang/String;)Z | 参数：字符串；返回值：布尔值 |
| (IF)Ljava/lang/String;  | 参数：整数、浮点数；返回值：字符串 |

类型见下表：

| 类型名 | 类型 |
|-----|------|
| I | 整数 |
| F | 浮点数 |
| Z | 布尔值 |
| Ljava/lang/String; | 字符串 |
| V | Void 空 |

> 注：Lua function以一个整数传递到 Java 端。

## 线程

Cocos2d-Lua 的 OpenGL 以及 Lua 都执行在一个主线程中，这意味着 Lua 调用 Java的时候，对应的 avtivity 必须是同一个线程的。否则 Android 的 NDK 会报错。`org/cocos2dx/lua/AppActivity` 是执行 OpenGL 的 activity，在这里定义的函数可以安全地被 luaj 调用。

在 SDK 接入中，某些 SDK 会启动另一个 activity 来做消息回调处理。由于 Android 机制，不同的 activity 很可能不在同一个进程中运行，这就需要你在 Java 端自行封装 activity 与 `org/cocos2dx/lua/AppActivity` 的通信机制；或者使用下面的方式强制让回调代码在 OpenGL 线程执行：

```
context.runOnGLThread(new Runnable() {
	@Override
	public void run() {
		Cocos2dxLuaJavaBridge.callLuaFunctionWithString(luaFunctionId, "success");
        Cocos2dxLuaJavaBridge.releaseLuaFunction(luaFunctionId);
	}
});
```

## Lua function 的引用计数

由于 Lua 虚拟机具有自动垃圾回收机制，传入 Java 端的 Lua function 的引用计数会在**C层的LuaJavaBridge中自动增加1**。

而 Java 层的 LuaJavaBridge 提供了 releaseLuaFunction(luaFunctionId) 函数用于减少 Lua function 的引用计数。
