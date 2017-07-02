# 3.6.5 热更新 Demo 说明

> 热更新demo经过两次社区的贡献，这次的说明居于第二次贡献（感谢：SamSara）。本人在 SamSara 提供的代码上验证并作改进。

Demo提供在引擎 3.6.5 中;

Demo路径：Quick-Cocos2dx-Community/quick/samples/liveupdate

## 目录说明

```
GenHotUpdate.py -- 热更新版本描述文件生成工具
HttpServerRoot  -- 用于测试的HTTP服务器根目录，已加入升级文件
hotupdate       -- 存在于手机writeable目录下，存放从服务器下载的文件
res             -- 资源目录
src             -- 代码目录
version_info.json -- 版本描述，被python脚本访问
```

## 如何测试Demo

1. 启动一个简单的HTTP服务器，最便捷的途径是python。
	```
    $cd Quick-Cocos2dx-Community/quick/samples/liveupdate/HttpServerRoot
    $python -m SimpleHTTPServer 3000
    ```
2. 用player启动示例中的热更新demo，点击下方的按钮图片1。console显示热更新各信息，完成后按钮图片上的数字更新为2。

## 如何发布一次更新

1. 修改version_info.json中的版本号，比已上线的版本号高。
	```
    "version" : "1.0.2",
    ```
2. 运行GenHotUpdate.py，自动更新project.manifest和version.manifest。
3. 把`res`文件夹拷贝替换`HttpServerRoot/hotupdate/res`文件夹。

## 有关资源搜索顺序

由于热更新的资源应当高于apk包中的资源，在MyApp.lua中设置了资源的搜索路径，如下：

```
function MyApp:run()
	local writePath = cc.FileUtils:getInstance():getWritablePath() 
	cc.FileUtils:getInstance():addSearchPath(writePath .. "hotupdate/res/")
	cc.FileUtils:getInstance():addSearchPath("res/")

	print("== listing engine search path")
	local searchPaths = cc.FileUtils:getInstance():getSearchPaths()
	for k,v in pairs(searchPaths) do
		print(k,v)
	end

	self:enterScene("MainScene")
end
```

## 资源更新后的重新加载

调用引擎提供的下面接口，清空缓存。

```
cc.FileUtils:getInstance():purgeCachedEntries()
```

当创建精灵的时候，缓存中没有图片，自然会去按照资源文件夹优先级进行搜索加载。

## 只更新了资源！代码呢？

是的，我在SamSara提供的demo基础上屏蔽了src目录的更新，因为这并不符合实际项目运作。

在 [Quick-Cocos2dx-Community 在发布时加密lua源文件](../encryptlua/index.md) 文档中，提到了代码加密，最终加密后的zip文件是当做资源放在res目录中的。只要更新了zip包，就能实现代码的热更新，而剩下的就是zip包的加载顺序问题。

我的建议是，game.zip包分多个：一个最小的luncher.zip，专门实现热更新逻辑与界面，在AppDelegate.cpp中写死对这个zip的加载；luncher.zip中再加载framework.zip与game.zip等。framework.zip是相对稳定的框架，很少更新；game.zip是业务逻辑，会经常更新。

C++中加载zip如下：

```
stack->loadChunksFromZIP("res/luncher.zip");
```

Lua中加载zip如下：

```
cc.LuaLoadChunksFromZIP("res/game.zip")
```

zip包的密码与xxtea加密统一都在AppDelegate.cpp中设置。