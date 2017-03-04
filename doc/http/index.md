# HTTP 使用说明

Quick中有2套HTTP接口，

1. cc.XMLHttpRequest，对应Quick-Cocos2dx-Community/cocos/network/HttpClient
2. cc.HTTPRequest，对应Quick-Cocos2dx-Community/quick/lib/quick-src/extra/network/CCHTTPRequest

而 cc.HTTPRequest 又被封装为 network.createHTTPRequest，
cc.HTTPRequest是Quick对HTTP的优化封装，特别为Android设计了Java HTTP方式，以避免进入CURL以及OpenSSL这两个庞大的库。

## network.createHTTPRequest 用法

```
 -- http request
local function onRequestCallback(event)
	local request = event.request
	
	dump(event)

	if event.name == "completed" then
		print(request:getResponseHeadersString())
		local code = request:getResponseStatusCode()
		if code ~= 200 then
			-- 请求结束，但没有返回 200 响应代码
			print(code)
			return
		end

		-- 请求成功，显示服务端返回的内容
		print("response length" .. request:getResponseDataLength())
		local response = request:getResponseString()
		print(response)
	elseif event.name == "progress" then
		print("progress" .. event.dltotal)
	else
		-- 请求失败，显示错误代码和错误消息
		print(event.name)
		print(request:getErrorCode(), request:getErrorMessage())
		return
	end
end

-- get test
--local request = network.createHTTPRequest(onRequestCallback, "http://127.0.0.1:1234/hello", "GET")
--request:start()

-- post test
local request = network.createHTTPRequest(onRequestCallback, "http://127.0.0.1:1234/hello", "POST")
--request:addPOSTValue("name", "laoliu")
request:setPOSTData("this is poststring to server!")
request:start()
```

## Android 注意事项

如果你要使用cc.HTTPRequest，必须在`frameworks/runtime-src/proj.android/libcocos2dx/jni/Application.mk`中开启`CC_USE_CURL := 1`。

network.createHTTPRequest 无论是否开启CC_USE_CURL，都无法关闭。但是你可以选择它是用curl实现，还是用Java实现。

要减少apk包体积，是用`CC_USE_CURL := 0`配合 network.createHTTPRequest 访问HTTP即可。

由于 Java HTTP 在实现过程中，异常处理不够严谨，在部分机器上会有崩溃问题，在 3.6.5中由社区贡献了一份加强版，可处理大部分异常。如果你在一些机器上遇到了java http的崩溃，欢迎反馈并提供修改方案。