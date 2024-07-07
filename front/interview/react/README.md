# setState是同步还是异步

>setState本质上是同步执行的. setState的“异步”并不是说内部由异步代码实现，其实本身执行的过程和代码都是同步的，只是合成事件和钩子函数的调用顺序在更新之前，导致在合成事件和钩子函数中没法立马拿到更新后的值，形式了所谓的“异步”

> "异步处理的原因": 1. 保证和props行为一致  2. 启用并发更新

## React 17 setState

```js
// react 17.x
if (executionContext === NoContext) {
  // Flush the synchronous work now, unless we're already working or inside
  // a batch. This is intentionally inside scheduleUpdateOnFiber instead of
  // scheduleCallbackForFiber to preserve the ability to schedule a callback
  // without immediately flushing it. We only do this for user-initiated
  // updates, to preserve historical behavior of legacy mode.
  resetRenderTimer();
  flushSyncCallbackQueue();
}
```

> react 更新时会有一个上下文环境判断,如果没有上下文环境,将以同步的方式更新.

    1. 什么时候“异步”更新呢?
    在合成事件与React的生命周期函数中,有executionContext,所以不会立即更新.

    2. 什么时候“异步”更新呢?
    在定时器,Promise、原生事件中的executionContext === NoContext,所以会立即更新.

## React 18 setState

```js
// 18版本 mode 为 ConcurrentMode,(fiber.mode & ConcurrentMode) === NoMode 为fale,所以永远不会立即更新
if (
  lane === SyncLane && 
  executionContext === NoContext && 
  (fiber.mode & ConcurrentMode) === NoMode && 
  // Treat `act` as if it's inside `batchedUpdates`, even in legacy mode.
  !( ReactCurrentActQueue$1.isBatchingLegacy)) {
  // Flush the synchronous work now, unless we're already working or inside
  // a batch. This is intentionally inside scheduleUpdateOnFiber instead of
  // scheduleCallbackForFiber to preserve the ability to schedule a callback
  // without immediately flushing it. We only do this for user-initiated
  // updates, to preserve historical behavior of legacy mode.
  resetRenderTimer();
  flushSyncCallbacksOnlyInLegacyMode(); // 立即更新
}
```
    因为开启了并发模式,永远不可能走同步更新. react18版本的setState都会批量处理. 将setState创建的update对象缓存维护到一个队列中,在下一次调度更新前,将update对象分配到对应的hook的queue中去,形成一个环状链表. 那么调度更新后,重新执行fiber函数,hooks会消耗自己的queue,得到新的状态值

# useEffect与useLayout区别

    执行时机:
    useEffect: 在浏览器完成布局和绘制之后,在一个延迟的 microtask 中执行
    useLayoutEffect: 在所有的 DOM 突变之后立即同步执行它会在浏览器执行下一次绘制之前完成

    同步/异步:
    useEffect: 异步执行,不会阻塞浏览器的绘制
    useLayoutEffect: 同步执行，会阻塞浏览器的绘制

    性能影响:
    useEffect: 由于异步执行,不会阻塞浏览器的关键渲染路径,对性能影响较小
    useLayoutEffect: 由于同步执行,会阻塞浏览器的绘制,可能会导致掉帧和卡顿,对性能影响较大

# react 18 新增特性

    1. 启用并发模式
    2. 自动批处理,setState调用都不会立即更新,除非使用flushSync(),才会立即更新
    3. 新增并发hooks,如useTranstion、useDefferValue,会启用并发特性,发起一个低优先级的更新在后台运行,高优先级的任务可以打断低优先级的任务
    4. 优化了Suspense, Suspense 不再需要 fallback 来捕获,不再跨越边界组件向上寻找带有fallback的Suspense

# diff算法三个规则

    1. 只进行同层级的比较,如果一个结点diff后时删除,会删除当前结点与子节点
    2. 判断type是否相同,如果结点diff前后type不同,则删除当前结点与子节点,并创建新的结点
    3. 比较同层级结点,使用key作为唯一标识,达到结点复用的效果

# redux三大原则

    1. 单一数据源,整个应用的 全局 state 被储存在一棵 object tree 中，并且这个 object tree 只存在于唯一一个 store 中。
    2. State 是只读的,唯一改变state的方法是触发action
    3. reducer为纯函数,给定相同的输入,必定返回相同的输出