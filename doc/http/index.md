# HTTP 使用说明

> 适用于所有社区版。注：4.0不再提供Java HTTP模式，只有curl。

## network.createHTTPRequest 用法

network.createHTTPRequest适用于数据量小的网络请求，默认请求超时10秒，数据超时30秒，可用于小文件下载，如果大文件，则需要注意内存，以及设置数据超时时间。

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

## 回调用进度

onRequestCallback回调函数中，通过`event.name == "progress"`可获得数据下载进度，但需要注意，进度会有两次，一次header的，一次body的。底层暂时未去屏蔽header的progress。

## 超时设置

如果需要改变默认数据超时时间可以，使用`setTimeout`来设置，注意需要在start之前调用。如下：

```
local request = network.createHTTPRequest(callback, url, "GET")
request:setTimeout(15) -- 15s
request:start()
```

> 注：超时时间是整个HTTP完成的时间，不是无数据的响应时间！
> createHTTPDownload下载模式下是最低速度的持续时间。

## 上传文件

network.uploadFile用于提交form上传，示例如下：

```
  network.uploadFile(function(evt)
    if evt.name == "completed" then
	  local request = evt.request
	  printf("REQUEST getResponseStatusCode() = %d", request:getResponseStatusCode())
	  printf("REQUEST getResponseHeadersString() =\n%s", request:getResponseHeadersString())
	  printf("REQUEST getResponseDataLength() = %d", request:getResponseDataLength())
	  printf("REQUEST getResponseString() =\n%s", request:getResponseString())
	end
  end,
  "http://127.0.0.1/upload.php",
  {
    fileFieldName = "filepath",
	filePath = device.writablePath.."screen.jpg",
	contentType = "Image/jpeg",
	extra = {
	  {"act", "upload"},
	  {"submit", "upload"},
	}
  })
```

## 下载到文件

network.createHTTPDownload 从4.0.1开始加入，用于数据文件的下载，内部直接下载到文件，无需分配大内存，支持断点续传。

注意事项：

1. 不做写入文件的保护，应用层自行确保savePath是不用的。
2. 成功的response code返回值可能是200或206.
3. 如果服务器不支持短线续传，那么第一次续传会失败并自动删除缓存文件，下一次下载将自动从0开始。使用network.createHTTPDownload请确保服务器支持短点续传。
4. 不支持`request:getResponseString(),request:getResponseData()`获取数据，因为是直接下载到文件的。
5. 无需设置超时，下载模式下,内部通过最小下载速度的持续时间来判断超时。

```
 local function onRequestCallback(event)
    local request = event.request
    if event.name == "completed" then
      local code = request:getResponseStatusCode()
      if code == 200 or code == 206 then -- 206 resume from break-point
        print("download success")
        return
      end
      print("HTTP unkonw response code:", code) -- get error
    elseif event.name == "progress" then
      print("progress" .. event.dltotal)
    else
      print(event.name) -- get error
      print(request:getErrorCode(), request:getErrorMessage())
    end
  end

  local savePath = cc.FileUtils:getInstance():getWritablePath() .. "download.data"
  local request = network.createHTTPDownload(onRequestCallback, "https://baidu.com", savePath)
  request:start()
```

## Android 切换底层实现（3.7 or 3.6）

在3.7.x或3.6.x中，可以修改mk文件中的`CC_USE_CURL`，来切换HTTP的底层实现方式。

`CC_USE_CURL := 0` 使用 Java HTTP，如果设置为 1 则使用 curl HTTP。

由于 Java HTTP 在实现过程中，异常处理不够严谨，在部分机器上会有崩溃问题，在 3.6.5中由社区贡献了一份加强版，可处理大部分异常。如果你在一些机器上遇到了 Java HTTP的崩溃，欢迎反馈并提供修改方案。