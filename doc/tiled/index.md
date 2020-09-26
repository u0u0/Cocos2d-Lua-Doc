# Tiled 地图编辑器

> 文档描述适用于引擎版本3.7.x最新和4.x最新。

## Tiled 版本

Tiled 从0.8开始，进入飞速发展期间，版本号大跃进，功能也越来越多。Cocos2d-x 官方的解析和运行代码只支持到0.8的数据格式，后续新的功能不能适用。故本人对社区版的Tiled数据解析和渲染模块进行了大量重写，以便支持新功能。

目前社区版已支持Tiled tmx格式1.2大部分功能，而Tiled tmx已更新到1.4格式，后续新功能视情况来添加支持，在不使用新功能的情况下，引擎是兼容高版本的tmx数据格式的。

## 社区版Tiled支持情况

### 支持

* 支持Group Layer。
* 支持Image Layer。
* 支持collection of images类型的tileset。
* 支持Tile类型的Object对象（对象层中的图形对象）。
* 支持文本类型的Object对象（对象层中的图形对象）。
* 支持Tile帧动画。
* 支持Tile块碰撞数据的解析。
* 正常、45度、正交、六边形个类型地图上坐标系正确转换。

### 不支持

* Tileset 的自定义属性解析获取。

## 相关视频

[TiledMap 使用介绍–1.2格式新功能](https://www.bilibili.com/video/av81389004)

## 基本用法

加载地图，添加到父节点。

```
local map = cc.TMXTiledMap:create("tiled/desert.tmx")
map:addTo(self)
```

获取对象层

```
local layerObjects = map:getObjectGroup("Objects")
```

获取对象层中的对象数据

```
for _, obj in ipairs(layerObjects:getObjects()) do
    -- ObjectGroup's object's pos had convert to cocos coordinate
    -- obj包含了默认属性和自定义属性
    dump(obj)
end
```

获取tile图层,以及某一块的gid对应的自定义数据

> 注意 Cocos2d-x中的Tile gid值 = Tiled编辑器值 + 1

```
local layer = map:getLayer("Ground")
local gid = layer:getTileGIDAt(cc.p(6, 12))
local prop = map:getPropertiesForGID(gid)
```

## 引擎测试代码

[Test_TMXTiledMap.lua](https://github.com/u0u0/Cocos2d-Lua-Community/blob/master/tests/src/app/views/Test_TMXTiledMap.lua)