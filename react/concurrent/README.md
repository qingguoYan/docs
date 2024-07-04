# React 并发模式

> 并发模式是 React 18推出的一个新特性,它旨在通过优化 React 的渲染过程,提高应用程序的性能和响应性。包括时间切片、可中断渲染、任务优先级机制,通过这些特性,并发模式可以帮助 React 应用程序实现更流畅的用户体验,即使在处理大量数据或复杂 UI 的情况下也能保持良好的交互性

## 1. React 为什么需要并发 ?

- JS是单线程运行,浏览器每一帧React在setState引起一次新的渲染时,如果产生的变化非常巨大,js代码执行时间长,会导致用户UI响应卡顿掉帧,影响用户体验。
- 高优先级任务会需要中断低优先级的任务

## 2. React 给出的解决思路 - 时间切片

时间分片是指将单个的大型渲染任务划分成多个小任务,在每一帧中执行这些小任务,而不是一次性地执行整个大任务。这样可以确保浏览器在每一帧中都有时间来处理用户交互,从而提高用户体验。

## 2.1 任务如何切分成多个小任务的，又是如何中断任务的呢 ？

在源码中有两个任务循环，一个是Schdule包中**异步调度**任务队列的任务循环，一个是单个渲染任务中所有fiber任务的执行。

两个任务循环都通过**shouldYieldToHost**方法判断任务是否中断，让出主线程。**shouldYieldToHost** 本质上会判断任务执行的时间是否超过5ms，超过则中断任务循环。

### 2.1.1 fiber任务循环
并发渲染模式下，如果还存在fiber任务单元没完成，并且还没有超出5ms的时间，任务都会继续执行，否则任务将中断。

```javascript
    function workLoopConcurrent() {
    // Perform work until Scheduler asks us to yield
        while (workInProgress !== null && !shouldYield()) {
            performUnitOfWork(workInProgress);
        }
    }
```

``` javascript
   if (currentTask.expirationTime > currentTime && shouldYieldToHost()) {
      // This currentTask hasn't expired, and we've reached the deadline.
      break;
    }
```
