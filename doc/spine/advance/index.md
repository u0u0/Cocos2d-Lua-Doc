# Spine 进阶接口

> 文档中的接口适用于3.7.x最新版本和4.x最新版本。

Spine 3.6 runtime 由于数据结构改动，  ~~findSlot~~ 的绑定接口暂时去掉。

## findAnimation

```
spineAnimation:findAnimation("animation")
```

检测骨骼动画中是否存在某个名称的动画，返回bool。

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

> 注：只有第一个参数时，表示去掉slot的Attachment，不显示图片。

接口示例：

```
local hero = sp.SkeletonAnimation:create("build_yellowlightfinished.json", "build_yellowlightfinished.atlas")
hero:setAttachment("changegun", "pc_gungirl_crossbow3")
```

## findBone 和 updateBone

findBone 为社区版手动绑定，返回 table 提供给开发者，这样可有效避免内存管理混乱的问题。
目前只暴露了部分有用的信息。

接口示例：

```
dump(hero:findBone("changegun"))
```

updateBone 为社区版手动绑定(3.7.3新增)，用来更新Bone信息，在没有k帧的时候，可以达到某些特殊功能的实现。

接口示例：

```
dump(hero:updateBone("rear-upper-arm", {
	x = 0,
	y = 0,
	rotation = 90.0,
	scaleX = 2,
	scaleY = 1,
	shearX = 10,
	shearY = 10,
}))
```