# FairyGUI 说明

> Support from Cocos2d-Lua-Community 4.0.0.

有关 FairyGUI， 参考：https://www.fairygui.com/

## Runtime 用法示例

创建根对象并添加到当前场景，建议一个场景对应一个 Fairy 根对象。

```
self.fairyRoot = fairygui.GRoot:create(display.getRunningScene())
self.fairyRoot:retain()
```

加载 UIPackage，一次即可.

```
fairygui.UIPackage:addPackage("fairygui/package01");
```

从 UIPackage 创建 Object.

```
local view = fairygui.UIPackage:createObject("package01", "StartLayer")
```

添加 Object 到根对象.

```
self.fairyRoot:addChild(view)
```

Object事件绑定.

```
view:getChild("n9"):addEventListener(fairygui.UIEventType.TouchEnd, function(context)
    print(content)
end)
```

## Test cases

引擎提供的 Test cases 文件位于:`Cocos2d-Lua-Community/tests/src/app/views/Test_FairyGUI.lua`