# 基于ccui.ListView的TableView实现

ccui.ListView在一次性加载大量item的时候，会有明显的卡顿，尤其是游戏图鉴这种应用场景比较吃力。在不改变ccui.ListView的cpp实现基础上，我从Lua端模拟了一个iOS TableView用法的实现。

你可以在[Lua-utils
](https://github.com/u0u0/Lua-utils/blob/master/src/app/utils/TableView.lua)仓库中获取TableView的实现。

## 用法示例

```
-- create listview, or get listview from csb
local lv = ccui.ListView:create()
lv:setContentSize(cc.size(300, 200))
lv:center():addTo(self)
lv:setBackGroundColorType(1)
lv:setBackGroundColor(cc.c3b(0, 100, 0))
lv:setAnchorPoint(cc.p(0.5, 0.5))
lv:setItemsMargin(4)
-- convert to tablevlew
local TableView = require("app.utils.TableView")
local sizeSource = function(self, index)
	return cc.size(300, 15)
end
local loadSoruce = function(self, index)
	return ccui.Text:create(index, "Airal", 18)
end
local unloadSoruce = function(self, index)
	print("do texture unload here:", index)
end
TableView.attachTo(lv, sizeSource, loadSoruce, unloadSoruce)
lv:initDefaultItems(300)
lv:jumpTo(300)
lv:addScrollViewEventListener(function(ref, type)
	print("event:", type)
end)
```

首先，拿到 ccui.ListView 的实例 lv，可以是自己创建的，也可以从csb中获取。

然后调用 TableView.attachTo() 来改变 lv 的属性和方法，并设置了3个回调函数。

sizeSource 是用来性能优化的，每个item的大小可以不一样，根据自己的实际场景返回cc.size.

loadSoruce 是加载真正的item的回调，你创建自己的显示内容，并返回。

unloadSoruce 是当某个item移出屏幕之外被调用的回调，你可以在这里释放图片资源。

要让TableView显示出条目，必须先调用initDefaultItems来初始化条目的总个数，紧接着调用jumpTo来跳到某个位置显示，jumpTo的参数为1～n之间，n为initDefaultItems的参数值。

## 机制与性能

TableView实际上是创建N个layout来占位，layout的创建和加载比复杂的csb item快多了，然后当需要显示某个item的是，调用loadSoruce去创建真实的item并附加在layout上。

实际测试1000个的item刷新第一屏，差不多在1s以内，比直接用pushBackDefaultItem快多了。如果一行分2～3个小item并排显示，那么可以放更多item。

如果你的item总数超过1000以上，建议配合分屏来优化。

另外 TableView 将屏蔽 ccui.ListView的一些方法，如下：

```
listview.pushBackDefaultItem = protectInfo
listview.insertDefaultItem = protectInfo
listview.pushBackCustomItem = protectInfo
listview.insertCustomItem = protectInfo
listview.removeLastItem = protectInfo
listview.removeItem = protectInfo
listview.removeAllItems = protectInfo
listview.getItem = protectInfo
listview.getItems = protectInfo
listview.getIndex = protectInfo
```

新增了4个方法如下：

```
listview.jumpTo = jumpTo
listview.initDefaultItems = initDefaultItems
listview.insertRow = insertRow
listview.deleteRow = deleteRow
```