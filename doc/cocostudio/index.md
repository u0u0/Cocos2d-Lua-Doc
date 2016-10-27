# Quick 社区版对 CocoStudio 的支持说明

## 支持的CocoStudio

Studio版本众多，但其主要可以按版本分为两大类，即 Studio1.x（Studio一代）和 Studio2.x（Studio二代）。

社区版引擎基于 Quick-Cocos2d-x3.3final，官方给出的对应的Studio版本为 2.x 中的 v2.0.6。当然，更高版本的 Studio 在该引擎中也不是一定不能用，但兼容性肯定不好（吐槽下，对应的 v2.0.6 兼容性也没见得特别好），所以不建议使用。

另外，一般来说高版本的引擎能加载低版本的 Studio1.x 数据，所以社区版引擎中也支持低版本的 Studio1.x 数据。如果在社区版中要使用低版本的 Studio1.x 数据，建议使用相对稳定且比较经典的 Studio 1.6 作为编辑器。(注：Studio1.x在社区版引擎中不完全兼容，但一般可用。）

Studio v2.0.6中没有骨骼动画模块，所以当应用程序中涉及到骨骼动画的时候，可以考虑使用以上所说的低版本的 Studio 1.6，更或是你也可以弃 Studio 择 Spine 或者 DragonBones。

## 加载ccs界面和场景的方法

社区版引擎加载ccs导出文件可以使用以前c++的那套 XXReader（如：SceneReader, GUIReader）接口进行加载。如下例所示：

	-- 加载UI界面导出的json（包括ExportJson）文件。
	local myWidget1 = ccs.GUIReader:getInstance():widgetFromJsonFile("newUI.json")
	self:addChild(myWidget1)
	-- 加载UI界面导出的csb文件(注：不能加载Studio2.x导出的csb文件)。
	local myWidget2 = ccs.GUIReader:getInstance():widgetFromBinaryFile("newUI.csb")
	self:addChild(myWidget2)
	-- 加载场景界面导出的文件。
	local myScene = ccs.SceneReader:getInstance()
		:createNodeWithSceneFile("FightScene.json")
	self:addChild(myScene)

这里 ccs.GUIReader 和 ccs.SceneReader都是c++绑定到lua时生成的类，通过以上方法加载的界面的事件机制与现有的Quick的事件机制不兼容，所以已不建议使用。

Quick 框架中封装了一套自己的方法（cc.uiloader）来加载CCS导出文件，cc.uiloader 提供对外统一接口，可以将CCS导出的文件用quick的纯lua控件构建出UI布局。加载方法如下：

	local uinNode = cc.uiloader:load("testUI.json")
	self:addChild(uinNode)

cc.uiloader能加载Studio1.x导出的json／ExportJson文件，也能加载对应Studio2.x版本导出的csb二进制文件。

比较熟悉 Cocos2d-x 的童鞋应该清楚，在 Cocos2d-x 中使用Studio2.x发布的 .csb 资源更换了加载方式，它使用的是CSLoader来加载。CSLoader在Cocos2d-x3.3RC0开始提供，Quick中也有绑定到lua的相应的API，cc.uiloader:load也封装了该方法。如下：

	function uiloader:load(jsonFile, params)
		local json
		if not params or not params.bJsonStruct then
			local pathInfo = io.pathinfo(jsonFile)
			if ".csb" == pathInfo.extname then
				-- 加载".csb"格式文件
				return cc.CSLoader:getInstance():createNodeWithFlatBuffersFile(jsonFile)
			else
				json = self:loadFile_(jsonFile)
			end
		else
			json = jsonFile
		end
		if not json then
			print("uiloader - load file fail:" .. jsonFile)
			return
		end

		local node

		if self:isScene_(json) then
			node, w, h = CCSSceneLoader:load(json, params)
		else
			node, w, h = CCSUILoader:load(json, params)
		end

		UILoaderUtilitys.clearPath()

		return node, w, h
	end

由此我们知道，在 Quick-Cocos2dx-Community 3.6 Release 中加载Studio 导出文件的方式有3中，即：

- ccs.GUIReader
- cc.CSLoader
- cc.uiloader

另外，经过本人测试，在Quick-Cocos2d-Community中得到了以下的的结论：

- Quick中可以使用 ccs.GUIReader 接口加载 Studio 1.x 导出的资源。
- 使用 cc.CSLoader 接口只能加载的 Studio 2.x 的资源，如果加载 Studio1.x 资源会失败。
- cc.uiloader 接口是 quick 自己封装的接口。使用cc.uiloader可以加载 Studio1.x 导出的 json 文件。可以加载 Studio 2.x 导出的 csb 文件。

综上所诉，Quick-Cocos2d-Community中提倡使用Studio 2.0.6作为编辑器，同时提倡用cc.uiloader(1.x)和cc.CSLoader（2.x）来加载文件。更多使用方法可参考 XX/Quick-Cocos2dx-Community/quick/samples/ccsloader 路径中的例子。

### 加载动画

Studio 2.0.6 中没有骨骼动画模块，所以当项目中涉及到骨骼动画时，可考虑使用较低版本的Studio 1.6。加载动画的例子如下：

    -- 加载动画所用到的数据
    ccs.ArmatureDataManager:getInstance():addArmatureFileInfo("DemoPlayer/DemoPlayer1.png","DemoPlayer/DemoPlayer1.plist","DemoPlayer/DemoPlayer.ExportJson");
    local armature = ccs.Armature:create("DemoPlayer")  -- 创建动画对象
    armature:setPosition(0, 0)                    -- 设置位置
    armature:getAnimation():play("walk")            -- 设置动画对象执行的动画名称
    self:addChild(armature)

### 对应CocoStudio的下载地址

- CocosStudioForMac-v2.0.6.dmg 链接:[http://pan.baidu.com/s/1sjorTrn](http://pan.baidu.com/s/1sjorTrn) 密码：r6vk
- CocosStudioForWin-v2.0.6.exe 链接: [http://pan.baidu.com/s/1sjQR6gd](http://pan.baidu.com/s/1sjQR6gd) 密码: sxse
- CocosStudio_v1.6.0.0.exe 链接: [http://pan.baidu.com/s/1sjtb76P](http://pan.baidu.com/s/1sjtb76P) 密码: bqgs