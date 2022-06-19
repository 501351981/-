# zrender.init

## init实现
位置：src/zrender.ts  中init函数

使用方法：
init返回一个zrender实例
```javascript
 let zr = zrender.init(document.getElementById('main'), {
        renderer:'canvas'
    });
```

源码实现

```typescript
export function init(dom?: HTMLElement | null, opts?: ZRenderInitOpt) {
    const zr = new ZRender(zrUtil.guid(), dom, opts);
    instances[zr.id] = zr;
    return zr;
}
```
创建一个ZRender实例，存储到instances对象中，以实例id为key，并返回创建的实例。

为什么不直接返回，而是存储到数组中呢，存储下拉可以以备后续使用，比如批量销毁或者根据实例id查询实例。

```typescript
/**
 * Dispose all zrender instances
 */
export function disposeAll() {
    for (let key in instances) {
        if (instances.hasOwnProperty(key)) {
            instances[key].dispose();
        }
    }
    instances = {};
}

/**
 * Get zrender instance by id
 */
export function getInstance(id: number): ZRender {
    return instances[id];
}

```

guid()是获取一个唯一的id，实现也非常简单，很好奇为什么从2311开始。

```typescript
let idStart = 0x0907;  //2311
/**
 * Generate unique id
 */
export function guid(): number {
    return idStart++;
}
```

## 创建ZRender实例

### 属性
重要的是四个类
- storage：数据管理
- painter：渲染
- handler：事件交互
- animation：动画

```typescript
    dom?: HTMLElement

    id: number

    storage: Storage
    painter: PainterBase
    handler: Handler
    animation: Animation

    private _sleepAfterStill = 10;

    private _stillFrameAccum = 0;

    private _needsRefresh = true
    private _needsRefreshHover = true

    /**
     * If theme is dark mode. It will determine the color strategy for labels.
     */
    private _darkMode = false;

    private _backgroundColor: string | GradientObject | PatternObject;
```
