# CocoStudio 使用说明

> Update to Quick-Cocos2dx-Community 3.7.3

## CocoStudio 版本

Studio 按版本可分为两大类，即 Studio1.x（Studio一代）和 Studio2.x（Studio二代）。

Quick-Cocos2dx-Community 3.7.3 支持 1.6 和 3.10 两个版本的 studio编辑器。

> CocosStudio 3.10 其实也是 Studio2.x 系列。

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

## 加载 CocoStudio 3.10 资源

> 注：社区版整个runtime不支持编辑器的所有3D功能。另外310的骨骼动画也没经过测试，谨慎使用。推荐只使用Node方式新建文件，来解决UI布局问题。

Studio2.x 的 csb 加载方式有所改变，它使用的是 CSLoader 来加载，加载之后的UI 为 cocos2dx 3.x 的最新 UI 框架 ccui。

示例：

```
local csbNode = cc.CSLoader:getInstance():createNodeWithFlatBuffersFile(csbFile)
csbNode:addTo(self)
```

### CocoStudio 下载地址

- CocosForMac-v3.10.dmg 链接:[https://pan.baidu.com/s/1xB7ETHj99Kiis9EKaLFhwg](https://pan.baidu.com/s/1xB7ETHj99Kiis9EKaLFhwg)
- CocosForWin-v3.10.exe 链接: [https://pan.baidu.com/s/1wL6ptpm7VXOdf7e2ZzT6iw](https://pan.baidu.com/s/1wL6ptpm7VXOdf7e2ZzT6iw)
- CocosStudio_v1.6.0.0.exe 链接: [https://pan.baidu.com/s/1pKAgMAB](https://pan.baidu.com/s/1pKAgMAB)
