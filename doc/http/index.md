# HTTP 使用说明

> Update to Quick-Cocos2dx-Community 3.7

network.createHTTPRequest是 Quick 对 HTTP 的优化封装，特别为 Android 设计了 Java HTTP 方式，以避免引入 CURL 以及 OpenSSL 这两个庞大的库。

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
--request:addPOSTValue("name", "u0u0")
request:setPOSTData("this is poststring to server!")
request:start()
```

## Android 注意事项

network.createHTTPRequest 无论是否开启CC_USE_CURL，都无法关闭。但是你可以选择它是用 curl 实现，还是用 Java 实现。

`CC_USE_CURL := 0` 使用 Java HTTP，如果设置为 1 则使用 curl HTTP。

由于 Java HTTP 在实现过程中，异常处理不够严谨，在部分机器上会有崩溃问题，在 3.6.5中由社区贡献了一份加强版，可处理大部分异常。如果你在一些机器上遇到了 Java HTTP的崩溃，欢迎反馈并提供修改方案。