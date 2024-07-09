# 什么是事件循环机制

    JavaScript的事件循环机制是理解JavaScript运行机制的核心概念之一。事件循环是JavaScript实现异步编程的基础。
    它负责监视Call Stack(调用栈)和任务队列(Task Queue),并协调它们的工作。

# 事件循环的工作原理

    1. Call Stack(调用栈):
    这是一个先进后出(LIFO)的栈结构,用于存储正在执行的函数调用。
    当一个函数被调用时,它会被压入调用栈,当函数执行完毕后,它会从调用栈中弹出。

    2. Task Queue(任务队列):
    这是一个先进先出(FIFO)的队列,用于存放异步任务(如setTimeout、DOM事件、Ajax请求等)的回调函数。
    当异步任务完成时,它的回调函数会被添加到任务队列中等待执行。

    3. 事件循环的运行过程:
    首先,JavaScript引擎会执行Call Stack中的同步任务。
    当遇到异步任务时,它会将该任务的回调函数添加到Task Queue中。
    事件循环会不断检查Call Stack是否为空。当Call Stack为空时,它会检查Task Queue,如果有待执行的任务,就将其取出并压入Call Stack执行。
    这个过程会一直重复,直到所有任务都执行完毕。
    通过这种事件循环机制,JavaScript可以实现异步编程,避免因长时间运行的任务而阻塞主线程。开发者需要理解事件循环的工作原理,以便编写出高效、响应迅速的JavaScript代码。

# 任务队列的分类

    1.宏任务队列(Macrotask Queue):
    宏任务队列用于存放一些较为耗时的异步任务,如setTimeout、setInterval、I/O操作、UI渲染等。
    这些任务的回调函数会被添加到宏任务队列中,等待事件循环取出执行。

    2. 微任务队列(Microtask Queue):
    微任务队列用于存放一些较为轻量的异步任务,如Promise的then/catch/finally回调、queueMicrotask()、MutationObserver(监听DOM的变化)、
    MessageChannel、process.nextTick() (Node.js)。
    这些任务的回调函数会被添加到微任务队列中,优先于宏任务队列被事件循环取出执行。

    3. 事件循环的工作原理如下:
    首先,JavaScript引擎会执行Call Stack中的同步任务。
    当遇到异步任务时,它会将该任务的回调函数添加到相应的任务队列中(宏任务或微任务)。
    当Call Stack为空时,事件循环会检查微任务队列,如果有待执行的任务,就将其取出并压入Call Stack执行。
    在微任务队列为空的情况下,事件循环会检查宏任务队列,取出第一个任务压入Call Stack执行。
    这个过程会一直重复,直到所有任务都执行完毕。