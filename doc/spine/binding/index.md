# Quick-Cocos2d-x 社区版 spine 新增 Lua 绑定接口说明

## setFlippedX

```
spineAnimation:setFlippedX(true)
```

动画x轴反转，以前可以用setScaleX(-1) 来实现。


## setFlippedY

```
spineAnimation:setFlippedY(true)
```

动画y轴反转，以前可以用setScaleY(-1) 来实现。

## findAnimation

```
spineAnimation:findAnimation("animation")
```

判断一个动画是否存在，返回bool

## getBoundingBox

```
self:performWithDelay(function ()
    -- must call after first draw to get current value
    dump(spineAnimation:getBoundingBox())
end, 1.0)
```

获取动画的rect区域，需要在第一次draw之后调用才能正确获取数据。

## spine 换装接口 setAttachment

首先你在制作骨骼的时候，需要给一个 slot 设置多个 attachment，spine在一个时刻只会显示其中的一个 attachment，动态切换 attachment 调用 `SkeletonRenderer::setAttachment`接口。这个接口自动绑定并未提供，社区版手动绑定提供给开发者使用。

> 注：只有一个参数时，表示去掉slot的Attachment，不显示图片。

接口示例：

```
local hero = sp.SkeletonAnimation:create("build_yellowlightfinished.json", "build_yellowlightfinished.atlas")
hero:setAttachment("changegun", "pc_gungirl_crossbow3")
```

## 获取 bone 信息接口 findBone

spine的runtime还提供了 findBone 与 findSlot这样的接口，可获取内部动画播放某一时刻的信息，这些信息是可读、不建议写入的信息。社区版手动绑定为 table 提供给开发者，这样可有效避免内存管理混乱的问题。
目前只暴露了部分有用的信息。

接口示例：

```
dump(hero:findBone("changegun"))
```

output:

```
[LUA-print] - "<var>" = {
[LUA-print] -     "worldSignX" = 1
[LUA-print] -     "worldSignY" = 1
[LUA-print] -     "worldX"     = 15.590363502502
[LUA-print] -     "worldY"     = -29.508033752441
[LUA-print] - }
```

## 获取 slot 信息接口 findSlot

接口示例：

```
dump(hero:findSlot("changegun"))
```

output:

```
[LUA-print] - "<var>" = {
[LUA-print] -     "attachmentHeight" = 90
[LUA-print] -     "attachmentWidth"  = 90
[LUA-print] -     "points" = {
[LUA-print] -         1 = {
[LUA-print] -             "x" = -35.660007476807
[LUA-print] -             "y" = 22.880001068115
[LUA-print] -         }
[LUA-print] -         2 = {
[LUA-print] -             "x" = -35.659999847412
[LUA-print] -             "y" = -44.619998931885
[LUA-print] -         }
[LUA-print] -         3 = {
[LUA-print] -             "x" = -103.16000366211
[LUA-print] -             "y" = -44.619998931885
[LUA-print] -         }
[LUA-print] -         4 = {
[LUA-print] -             "x" = -103.16000366211
[LUA-print] -             "y" = 22.880001068115
[LUA-print] -         }
[LUA-print] -     }
[LUA-print] - }
```

**points 是slot debug边框的点阵信息，相对于animation节点的相对偏移量， 可以用来做碰撞检测判断。**

## setAttachment测试代码

```
function MainScene:ctor()
	cachedData = sp.SkeletonData:create("build_yellowlightfinished.json",
				"build_yellowlightfinished.atlas")
	local spineAnimation = sp.SkeletonAnimation:create(cachedData)

	local hero = spineAnimation:pos(display.width / 2, display.height / 2)
		:addTo(self)
	hero:setAnimation(0, "animation", true)
	-- button
	local images = {
		normal = "ButtonPressed.png",
		pressed = "ButtonPressed.png",
		disabled = "ButtonPressed.png",
	}

	cc.ui.UIPushButton.new(images, {scale9 = true})
        :setButtonSize(200, 60) --设置大小
		:onButtonClicked(function(event) -- 按钮的clicked事件处理
			hero:setAttachment("changegun", "pc_gungirl_crossbow3")
			dump(hero:findBone("changegun"))
			dump(hero:findSlot("changegun"))
        end)
        :align(display.CENTER, display.width - 80, 80)
		:addTo(self)
end
```

资源文件：[setAttachment_res.zip](./setAttachment_res.zip)
