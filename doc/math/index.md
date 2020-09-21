# Cocos2d-x Lua Community 中的 Lua 数学类

> 适用于社区所有版本

## 创建

在Lua中创建的 Vec2、Size、Rect 都是一个table类型。

其中只有相应的成员变量，没有相关的函数运算。

```
cc.p(x, y)                    -- 构造 Vec2
cc.size(width, height)        -- 构造 Size
cc.rect(x, y, width, height)  -- 构造 Rect
```

## 坐标向量运算

Lua中常用的向量运算如下：

1. 返回值为bool

```
-- 直线AB与直线CD是否相交
cc.pIsLineIntersect(pA, pB, pC, pD,float,float)

-- 线段AB与线段CD是否相交
cc.pIsSegmentIntersect(pA, pB, pC, pD)
```

2. 返回值为float

```
cc.pDot(p1, p2)         -- 点积
cc.pCross(p1, p2)       -- 叉积
cc.pProject(p1, p2)     -- 投影: 前point在后point上的投影

cc.pGetLength(p)        -- 向量长度 
cc.pLengthSQ(p)         -- 向量长度平方

cc.pGetDistance(p1, p2) -- 坐标距离
cc.pDistanceSQ(p1, p2)  -- 坐标距离平方

cc.pGetAngle(p1, p2)    -- 向量夹角：弧度
```

3. 返回值为point_table

```
cc.p(x, y)                       -- 构造坐标point
cc.pAdd(p1, p2)                  -- 相加
cc.pSub(p1, p2)                  -- 相减
cc.pMidpoint(p1, p2)             -- 两向量的中点
cc.pNormalize(p1)                -- 标准化向量

cc.pGetClampPoint(minp, maxp, p) -- 将p值限制在[minp,maxp]区间内

cc.pForAngle(float)              -- 返回坐标 x=cos(a) , y=sin(a)

cc.pPerp(p)                      -- 逆时针旋转90度(-y, x)
cc.RPerp(p)                      -- 顺时针旋转90度(y, -x)

-- 绕p1向量旋转
-- 返回向量: 角度 this.getAngle() + other.getAngle()
--         长度 this.getLength() * other.getLength()
cc.pRotate(p1, p2)

-- 绕p1向量旋转前的向量值
-- 返回向量: 角度 this.getAngle() - other.getAngle(); 
--         长度 this.getLength() * other.getLength();
cc.pUnrotate(p1, p2)

-- 直线AB与直线CD的交点
cc.pGetIntersectPoint(pA, pB, pC, pD)
```

## 矩形运算

Lua中常用的矩形运算如下：

```
cc.rectGetMinX(rect)    -- rect.x
cc.rectGetMidX(rect)    -- (rect.x + rect.width) / 2
cc.rectGetMaxX(rect)    -- rect.x + rect.width

cc.rectGetMinY(rect)    -- rect.y
cc.rectGetMidY(rect)    -- (rect.y + rect.height) / 2
cc.rectGetMaxY(rect)    -- rect.y + rect.height


-- 判断是否与rect相同. 原点相同,尺寸相同.
cc.rectEqualToRect(rect1, rect2)

-- 判断point是否包含在矩形内
cc.rectContainsPoint(rect, point)

-- 判断矩形是否相交. 常常用作碰撞检测.
cc.rectIntersectsRect(rect1, rect2)

-- 两矩形合并
cc.rectUnion(rect1, rect2)
```

## 随机数

根据系统时间初始化随机数种子，让后续的 **math.random()** 返回更随机的值

```
math.newrandomseed()
```

对数值进行四舍五入，如果不是数值则返回 0

```
math.round(value)
```

角度转弧度

```
math.angle2radian(angle)
```

弧度转角度

```
math.radian2angle(radian)
```