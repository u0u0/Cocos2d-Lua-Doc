# Quick-Cocos2d-x spine 新增 sp.SkeletonData 对象使用说明

自社区版引擎 Quick-Cocos2dx-Community 3.6 Beta1更新以来，就更新 Spine runtime 到了 2.3。

Quick 引擎中也新增了 sp.SkeletonData 对象用于创建 SkeletonAnimation，好东西要分享，所以这里我们就来说说 sp.SkeletonData 的好处和用法。

要知道，创建 Spine 动画有两种方法，一种是直接通过文件创建，另一种则是通过预加载的数据来创建。前者通过加载动作数据马上进行创建，如果 Spine 动画中的 json 文件大小超过一定范围时，会出现卡顿现象，如果动画文件偏小，可以使用这个方法来创建动画。

后者是通过预加载，保存动画数据在 Spine 的 C\+\+ runtime 的spSkeletonData结构中，然后通过这个数据来实现创建动画，这个方法可以在 Spine 动画偏大的情况下使用，以便开发者可以用数据缓存提升Spine的加载速度。


早些版本的引擎中，提供的 Spine 接口并不完整，Lua Binding只提供了直接读取Spine数据文件的方式来创建骨骼动画，spSkeletonData 相关的接口并未绑定到 Lua。所以我们只能按如下的代码创建骨骼动画：

	local spineAnimation = sp.SkeletonAnimation:create("dragon.json", "dragon.atlas")
	spineAnimation:pos(display.width / 2, display.height / 2)
    	:addTo(self)
    	:setAnimation(0, "walk", true)

现引擎中已修复这个缺陷，绑定 spSkeletonData 到了 Lua。也就是说，我们在 Quick-Cocos2dx-Community 3.6 Beta1 版本后，已经可以从 sp.SkeletonData 创建骨骼动画了。用法如下：

	cachedData = sp.SkeletonData:create("dragon.json", "dragon.atlas")
	local spineAnimation = sp.SkeletonAnimation:create(cachedData)

	spineAnimation:pos(display.width / 2, display.height / 2)
    	:addTo(self)
    	:setAnimation(0, "animation", true)

	self:performWithDelay(function()
    	spineAnimation:removeFromParent()
    	cachedData = nil
    	collectgarbage("collect")
	end, 5)

通过以上方法创建的骨骼动画，不接管 cachedData 的生命周期，spineAnimation 可以自由销毁，然后再使用 cachedData 来创建新的骨骼动画。我们可以把 cachedData 缓存起来以达到优化性能的目的。