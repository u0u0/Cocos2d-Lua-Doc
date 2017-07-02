# display 模块

display 模块封装了绝大部分与显示有关的功能，并负责根据 config.lua 中定义的分辨率设定计算屏幕的设计分辨率。

display是一个全局table，你应该使用点(.)来调用其下的方法。

## 常量

分辨率，坐标相关:

* display.sizeInPixels.width，屏幕的像素分辨率宽。
* display.sizeInPixels.height，屏幕的像素分辨率高。
* display.widthInPixels，等同于display.sizeInPixels.width。
* display.heightInPixels，等同于display.sizeInPixels.height。
* display.contentScaleFactor，设计分辨率到屏幕的缩放因子。
* display.size.width，屏幕的设计分辨率宽。
* display.size.height，屏幕的设计分辨率高。
* display.width，等同于display.size.width。
* display.height，等同于display.size.height。
* display.cx，设计分辨率中心点x值。
* display.cy，设计分辨率中心点y值。
* display.left，设计分辨率最左，也就是0。
* display.top，设计分辨率最上，也就是display.height。
* display.right，设计分辨率最右，也就是display.width。
* display.bottom，设计分辨率最右，也就是0。

颜色:

* display.COLOR_WHITE，等于cc.c3b(255, 255, 255)
* display.COLOR_BLACK，等于cc.c3b(0, 0, 0)
* display.COLOR_RED，等于cc.c3b(255, 0, 0)
* display.COLOR_GREEN，等于cc.c3b(0, 255, 0)
* display.COLOR_BLUE，等于cc.c3b(0, 0, 255)

`Node:align()` 的锚点参数：

* display.CENTER，等于(0.5, 0.5)
* display.LEFT_TOP = display.TOP_LEFT，等于cc.p(0, 1)。
* display.CENTER_TOP = display.TOP_CENTER，等于cc.p(0.5, 1)。
* display.RIGHT_TOP = display.TOP_RIGHT，等于cc.p(1, 1)。
* display.CENTER_LEFT = display.LEFT_CENTER，等于cc.p(0, 0.5)。
* display.CENTER_RIGHT = display.RIGHT_CENTER，等于cc.p(1, 0.5)。
* display.BOTTOM_LEFT = display.LEFT_BOTTOM，等于cc.p(0, 0)。
* display.BOTTOM_RIGHT = display.RIGHT_BOTTOM，等于cc.p(1, 0)。
* display.BOTTOM_CENTER = display.CENTER_BOTTOM，等于cc.p(0.5, 0)。

## 函数

`display.newScene(name)`

创建普通场景。

| 参数名 | 类型 |说明|
|----|---|----|
| name | string | 场景名|

返回值：cc.Scene 的实例。

---------------------

`display.newPhysicsScene(name)`

创建带物理世界的场景。

| 参数名 | 类型 |说明|
|----|---|----|
| name | string | 场景名|

返回值：cc.Scene 的实例。

---------------------

`display.replaceScene(newScene, transitionType, time, more)`

切换场景，可指定专场特效。

| 参数名 | 类型 |说明|
|----|---|----|
| newScene | cc.Scene | 场景的实例 |
| transitionType | string | 特效名称见下表[可选参数] |
| time | number | 切换事件[可选参数] |
| more | 不定 | 由第三个参数决定 |

transitionType:

- crossFade 淡出当前场景的同时淡入下一个场景
- fade 淡出当前场景到指定颜色，默认颜色为 cc.c3b(0, 0, 0)，可用 wrapSceneWithTransition() 的最后一个参数指定颜色
- fadeBL 从左下角开始淡出场景
- fadeDown 从底部开始淡出场景
- fadeTR 从右上角开始淡出场景
- fadeUp 从顶部开始淡出场景
- flipAngular 当前场景倾斜后翻转成下一个场景，默认从左边开始翻转，可以指定为：
	- cc.TRANSITION_ORIENTATION_LEFT_OVER 从左边开始
	- cc.TRANSITION_ORIENTATION_RIGHT_OVER 从右边开始
	- cc.TRANSITION_ORIENTATION_UP_OVER 从顶部开始
	- cc.TRANSITION_ORIENTATION_DOWN_OVER 从底部开始
- flipX 水平翻转，默认从左往右翻转，可用的附加参数同上
- flipY 垂直翻转，默认从上往下翻转，可用的附加参数同上
- zoomFlipAngular 倾斜翻转的同时放大，可用的附加参数同上
- zoomFlipX 水平翻转的同时放大，可用的附加参数同上
- zoomFlipY 垂直翻转的同时放大，可用的附加参数同上
- jumpZoom 跳跃放大切换场景
- moveInB 新场景从底部进入，直接覆盖现有场景
- moveInL 新场景从左侧进入，直接覆盖现有场景
- moveInR 新场景从右侧进入，直接覆盖现有场景
- moveInT 新场景从顶部进入，直接覆盖现有场景
- pageTurn 翻页效果，如果指定附加参数为 true，则表示从左侧往右翻页
- rotoZoom 旋转放大切换场景
- shrinkGrow 收缩交叉切换场景
- slideInB 新场景从底部进入，现有场景同时从顶部退出
- slideInL 新场景从左侧进入，现有场景同时从右侧退出
- slideInR 新场景从右侧进入，现有场景同时从左侧退出
- slideInT 新场景从顶部进入，现有场景同时从底部退出
- splitCols 分成多列切换入新场景
- splitRows 分成多行切换入新场景，类似百叶窗
- turnOffTiles 当前场景分成多个块，逐渐替换为新场景

返回值：无。

--------------------

`display.getRunningScene()`

返回当前正在运行的场景对象。

参数：无。

返回值：cc.Scene 的实例。

--------------------

`display.pause()`

暂停游戏。

参数：无。
返回值：无。

--------------------

`display.resume()`

恢复游戏。

参数：无。

返回值：无。

--------------------

`display.newLayer()`

创建层。

> 注意：3.7中，层和节点已经没有区别。

参数：无。

返回值：无。

--------------------

`display.newColorLayer(color)`

创建颜色层。

| 参数名 | 类型 |说明|
|----|---|----|
| color | cc.c4b | RGBA颜色值 |

返回值：无。

--------------------

`display.newNode()`

创建节点。

参数：无。

返回值：cc.Node的实例。

--------------------

`display.newClippingRectangleNode(rect)`

创建矩形裁剪节点。

| 参数名 | 类型 |说明|
|----|---|----|
| rect | cc.rect | 矩形区域 |

返回值：cc.ClippingRegionNode的实例。

--------------------

`display.newSprite(filename, x, y, params)`

创建精灵。

| 参数名 | 类型 |说明|
|----|---|----|
| filename | string | 文件名 |
| x | number | x坐标[可选] |
| y | number | y坐标[可选] |
| params | table | [可选] |

返回值：cc.Sprite的实例。

--------------------

`display.newScale9Sprite(filename, x, y, size, capInsets)`

创建Scale9精灵。

| 参数名 | 类型 |说明|
|----|---|----|
| filename | string | 文件名 |
| x | number | x坐标[可选] |
| y | number | y坐标[可选] |
| size | cc.size | 拉申后的大小[可选] |
| capInsets | cc.rect | 图片截选区域[可选] |

返回值：cc.Sprite的实例。

--------------------

`display.newTilesSprite(filename, rect)`

创建平铺的精灵。

| 参数名 | 类型 |说明|
|----|---|----|
| filename | string | 文件名 |
| rect | cc.rect | 平铺的区域 |

返回值：cc.Sprite的实例。

--------------------

`display.newDrawNode()`

创建几何绘图节点。

参数：无。

返回值：cc.DrawNode的实例。

--------------------

`display.newSolidCircle(radius, params)`

创建实心圆绘图节点。

| 参数名 | 类型 |说明|
|----|---|----|
| radius | number | 半径 |
| params | table | x,y坐标，color为cc.c4f的颜色值 |

返回值：cc.DrawNode的实例。

--------------------

`display.newCircle(radius, params)`

创建圆形绘图节点。

| 参数名 | 类型 |说明|
|----|---|----|
| radius | number | 半径 |
| params | table | x,y坐标，fillColor填充色cc.c4f，borderColor线颜色cc.c4f，borderWidth线宽 |

返回值：cc.DrawNode的实例。

--------------------

`display.newRect(rect, params)`

创建矩形绘图节点。

| 参数名 | 类型 |说明|
|----|---|----|
| rect | cc.rect | 矩形描述 |
| params | table | x,y坐标，fillColor填充色cc.c4f，borderColor线颜色cc.c4f，borderWidth线宽 |

返回值：cc.DrawNode的实例。

--------------------

`display.newLine(points, params)`

创建线段绘制节点。

| 参数名 | 类型 |说明|
|----|---|----|
| points | table | 形如`{{10, 10}, {100,100}}`的点集合 |
| params | table | borderColor线颜色cc.c4f，borderWidth线宽 |

返回值：cc.DrawNode的实例

--------------------

`display.newPolygon(points, params, drawNode)`

创建多点绘制节点。

| 参数名 | 类型 |说明|
|----|---|----|
| points | table | 形如`{{10, 10}, {100,100}}`的点集合 |
| params | table | fillColor填充色cc.c4f，borderColor线颜色cc.c4f，borderWidth线宽 |
| drawNode | cc.DrawNode | [可选] |

返回值：cc.DrawNode的实例。

--------------------

`display.addImageAsync(imagePath, callback)`

异步加载纹理。

| 参数名 | 类型 |说明|
|----|---|----|
| imagePath | string | 图片路径 |
| callback | funtion | 回调函数 |

返回值：无。

--------------------

`display.addSpriteFrames(plistFilename, image, handler)`

加载精灵表单。

| 参数名 | 类型 |说明|
|----|---|----|
| plistFilename | string | plist路径 |
| image | string | 图片路径 |
| handler | function | 开启异步加载，完成的回调函数[可选] |

返回值：无。

--------------------

`display.removeSpriteFramesWithFile(plistFilename, imageName)`

根据plist，批量删除缓存的帧。

| 参数名 | 类型 |说明|
|----|---|----|
| plistFilename | string | plist路径 |
| imageName | string | 图片路径 |

返回值：无。

--------------------

`display.removeSpriteFrameByImageName(imageName)`

通过图片名删除精灵帧缓存。

| 参数名 | 类型 |说明|
|----|---|----|
| imageName | string | 图片路径 |

返回值：无。

--------------------

`display.newSpriteFrame(frameName)`

创建精灵帧。

| 参数名 | 类型 |说明|
|----|---|----|
| frameName | string | 图片路径 |

返回值：cc.SpriteFrame。

--------------------

`display.newFrames(pattern, begin, length, isReversed)`

创建序列帧数组。

| 参数名 | 类型 |说明|
|----|---|----|
| pattern | string | 字符串自动生成匹配模式 |
| begin | number | 图片起始编号 |
| length | number | 图片数量 |
| isReversed | bool | 是否倒序 |

返回值: table。

--------------------

`display.newAnimation(frames, time)`

从序列帧数组创建 cc.Animation 动画对象。

| 参数名 | 类型 |说明|
|----|---|----|
| frames | table | 帧序列 |
| time | number | 帧之间的时间间隔 |

返回值: cc.Animation。

--------------------

`display.setAnimationCache(name, animation)`

缓存 cc.Animation 动画对象。

| 参数名 | 类型 |说明|
|----|---|----|
| name | string | 缓存名 |
| animation | cc.Animation | 被缓存的对象 |

返回值: 无。

--------------------

`display.getAnimationCache(name)`

获取缓存的 cc.Animation 动画对象。

| 参数名 | 类型 |说明|
|----|---|----|
| name | string | 缓存名 |

返回值: cc.Animation。

--------------------

`display.removeAnimationCache(name)`

删除缓存的 cc.Animation 动画对象。

| 参数名 | 类型 |说明|
|----|---|----|
| name | string | 缓存名 |

返回值: 无。

--------------------

`display.removeUnusedSpriteFrames()`

从缓存器删除引用计数为1的精灵帧。

| 参数名 | 类型 |说明|
|----|---|----|
| name | string | 缓存名 |

返回值: 无。

--------------------

`display.newProgressTimer(image, progresssType)`

创建一个进度条的节点。

| 参数名 | 类型 |说明|
|----|---|----|
| image | string | 图片名称 |
| progresssType | number | display.PROGRESS_TIMER_BAR条形，display.PROGRESS_TIMER_RADIAL圆形 |

返回值: cc.ProgressTimer。

--------------------

`display.captureScreen(callback, fileName)`

截取当前屏幕。

| 参数名 | 类型 |说明|
|----|---|----|
| callback | function | `function (bSuc, filePath)`，bSuc是否成功，filePath是绝对路径 |
| fileName | string | 文件名 |

返回值: 无。