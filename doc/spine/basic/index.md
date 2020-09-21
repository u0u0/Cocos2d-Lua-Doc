# Spine 基本用法

> 文档中的接口适用于3.7.x最新版本和4.x最新版本。
> 
> Quick-Cocos2dx-Community 3.7.3 更新 Spine runtime，支持 Spine 3.6 版本导出的数据格式。由于 Spine runtime 各版本之间不兼容，请切换Spine设置到对应的版本使用。

## 创建骨骼动画

Spine 3.6 新增二进制导出格式，对应的Spine动画创建接口也有变动。Lua接口改为3种使用方式：

1. 直接从json创建。

	```
	local spineSP = sp.SkeletonAnimation:createWithJsonFile(
			"tank/tank-pro.json", "tank/tank-pma.atlas")
	spineSP:pos(display.width / 2, display.height / 2):addTo(self)
	spineSP:setAnimation(0, "drive", true)
	```

2. 从二进制创建

	```
	local spineSP = sp.SkeletonAnimation:createWithBinaryFile(
		"tank/tank-pro.skel", "tank/tank-pma.atlas")
	spineSP:pos(display.width / 2, display.height / 2):addTo(self)
	spineSP:setAnimation(0, "drive", true)
	```
	
	Spine 3.6 runtime新增接口，用于优化json缓慢的加载，提升性能。
3. 从sp.SkeletonData创建

	```
	-- 创建缓存，全局变量以便于不被释放
	cachedData = sp.SkeletonData:create("tank/tank-pro.json", "tank/tank.atlas")

	local spineSP = sp.SkeletonAnimation:createWithData(cachedData)
	spineSP:pos(display.width / 2, display.height / 2):addTo(self)
	spineSP:setAnimation(0, "drive", true)

	-- 测试从一个 sp.SkeletonData 多次创建
	self:performWithDelay(function()
		local spineSP = sp.SkeletonAnimation:createWithData(cachedData)
		spineSP:pos(display.width / 2, 100):addTo(self)
		spineSP:setAnimation(0, "drive", true)
	end, 1)
	```

	sp.spSkeletonData 在 Quick-Cocos2dx-Community 3.6 Beta1 及以后版本可用。sp.spSkeletonData 是手动封装的Lua，基于Lua本身的内存管理机制。
通过以上方法创建的骨骼动画，不接管 cachedData 的生命周期，spineAnimation 可以自由销毁，然后再使用 cachedData 来创建新的骨骼动画。我们可以把 cachedData 缓存起来以达到优化性能的目的。

## 监听事件

Spine 3.6 runtime 新增了 ANIMATION_INTERRUPT 和 ANIMATION_DISPOSE 两种事件类型，总共6种事件类型：

```
sp.EventType =
{
    ANIMATION_START = 0,
    ANIMATION_INTERRUPT = 1,
    ANIMATION_END = 2,
    ANIMATION_COMPLETE = 3,
    ANIMATION_DISPOSE = 4,
    ANIMATION_EVENT = 5,
}
```

老项目移植，需要更新项目下的 cocos/spine/SpineConstants.lua 文件。

注册事件用法如下：

```
spineSP:registerSpineEventHandler(function(event)
	dump(event)
end, sp.EventType.ANIMATION_COMPLETE)
```

建议在 setAnimation 之前注册，以便正确收到所需事件。