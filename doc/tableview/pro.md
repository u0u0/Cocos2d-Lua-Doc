# 基于ccui.ScrollView的TableViewPro实现

> 感谢BogeyRuan提供TableViewPro的实现。

基于ccui.ListView的TableView，已经能优化性能，但还不够完美。BogeyRuan提供的TableViewPro，底层用ccui.ScrollView做移动控制，并实现了cell复用，完全模拟了iOS的UITableView。

你可以在[Lua-utils
](https://github.com/u0u0/Lua-utils/blob/master/src/app/utils/TableViewPro.lua)仓库中获取TableViewPro的实现。

## 用法示例

注册框架，之后便可以使用cc.TableView进行创建。

```
require("app.scenes.TableViewPro")
```

创建cc.TableView示例:

```
local amount = 1000

local tab = cc.TableView.new(cc.size(100,400))
local function size(tableview, index)
	return 100, 100
end

local function number(tableview)
	return amount
end

local function loadCell(tableview, index)
	print("loadCell:", index)
	local cell = tableview:dequeueCell()
	if not cell then
		cell = cc.TableViewCell.new()
		local text = ccui.Text:create(index, "", 50):addTo(cell, 1, 666):align(display.LEFT_BOTTOM, 0, 0)
		text:setTextColor(cc.c3b(255,255,math.random(1, 255)))
	end
	cell:getChildByTag(666):setString(index)

	return cell
end

local function unloadCell(tableview, index)
	print("unloadCell:", index)
end

tab:setDirection(cc.TableViewDirection.vertical)
tab:setFillOrder(cc.TableViewFillOrder.topToBottom)
tab:registerFunc(cc.TableViewFuncType.cellSize, size)
tab:registerFunc(cc.TableViewFuncType.cellNum, number)
tab:registerFunc(cc.TableViewFuncType.cellLoad, loadCell)
tab:registerFunc(cc.TableViewFuncType.cellUnload, unloadCell)
tab:addTo(self):align(display.CENTER, display.cx, display.cy)
tab:reloadData()

local text = ccui.Text:create("resize tableview", "", 40):addTo(self):pos(display.cx, display.cy + 250)
text:addNodeEventListener(cc.NODE_TOUCH_EVENT, function(event)
	if event.name == "ended" then
		amount = 18
		tab:reloadDataInPos()
	end
	return true
end)
text:setTouchEnabled(true)
```