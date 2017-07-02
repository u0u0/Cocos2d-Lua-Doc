# CocoStudio 使用说明

> Update to Quick-Cocos2dx-Community 3.7

## CocoStudio 版本

Studio 按版本可分为两大类，即 Studio1.x（Studio一代）和 Studio2.x（Studio二代）。

社区版引擎基于 Quick-Cocos2d-x3.3 final，官方给出的对应的 Studio 版本为 2.x 中的 v2.0.6。更高版本的 Studio 在该引擎中也不是一定不能用，但兼容性肯定不好，所以不建议使用。

社区版引擎中也支持低版本的 Studio1.x 数据。如果在社区版中要使用低版本的 Studio1.x 数据，建议使用相对稳定且比较经典的 Studio 1.6 作为编辑器。

Studio v2.0.6中没有骨骼动画模块，所以当应用程序中涉及到骨骼动画的时候，可以考虑使用以上所说的低版本的 Studio 1.6，更或是你也可以放弃 Studio 选择 Spine。

## 加载 CocoStudio 1.x 资源

示例：

```
-- 加载UI界面导出的json（包括ExportJson）文件。
local myWidget1 = ccs.GUIReader:getInstance():widgetFromJsonFile("newUI.json")
self:addChild(myWidget1)

-- 加载UI界面导出的csb文件(注：不能加载Studio2.x导出的csb文件)。
local myWidget2 = ccs.GUIReader:getInstance():widgetFromBinaryFile("newUI.csb")
self:addChild(myWidget2)

-- 加载场景界面导出的文件。
local myScene = ccs.SceneReader:getInstance():createNodeWithSceneFile("FightScene.json")
self:addChild(myScene)
```

骨骼动画示例：

```
-- 加载动画所用到的数据
ccs.ArmatureDataManager:getInstance():addArmatureFileInfo("DemoPlayer/DemoPlayer1.png","DemoPlayer/DemoPlayer1.plist","DemoPlayer/DemoPlayer.ExportJson");
local armature = ccs.Armature:create("DemoPlayer")  -- 创建动画对象
armature:setPosition(0, 0)                    -- 设置位置
armature:getAnimation():play("walk")            -- 设置动画对象执行的动画名称
self:addChild(armature)
```

## 加载 CocoStudio 2.0.6 资源

Studio2.x 的 csb 加载方式有所改变，它使用的是 CSLoader 来加载，加载之后的UI为cocos2dx 3.x的最新UI框架，ccui。

示例：

```
local csbNode = cc.CSLoader:getInstance():createNodeWithFlatBuffersFile(csbFile)
csbNode:addTo(self)
```

### CocoStudio 下载地址

- CocosStudioForMac-v2.0.6.dmg 链接:[https://pan.baidu.com/s/1gfN43mz](https://pan.baidu.com/s/1gfN43mz)
- CocosStudioForWin-v2.0.6.exe 链接: [https://pan.baidu.com/s/1qXEi5is](https://pan.baidu.com/s/1qXEi5is)
- CocosStudio_v1.6.0.0.exe 链接: [https://pan.baidu.com/s/1pKAgMAB](https://pan.baidu.com/s/1pKAgMAB)
