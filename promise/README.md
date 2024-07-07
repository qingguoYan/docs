# 面试题

## 处理并发任务

给你一段如下代码，要求我们在调用addTask函数时，要求我们有多个任务队列，使我们可以同时执行多个函数。

例如：我们给addTask添加了五个任务，任务1需要 10s、任务2需要 5s、任务3需要3s、 任务4需要4s、 任务5需要5s。我们现在有两个任务队列去执行这个五个任务，需要在5s执行完任务2， 8s执行完任务3，10秒执行完任务1, 12s执行完任务4, 15s执行完任务5。

```js
function timeout(time) {
    return new Promise((resolve) => {
        setTimeout(() => {
            resolve()
       }, time)
    })
}
const superTask = new SuperTask(2)
function addTask(time, name) {
    superTask
    .add(() => timeout(time))
    .then(() => {
        console.log(`任务${name}完成`);
    })
}
addTask(10000, 1)   // 10000 1
addTask(5000, 2)    // 5000 2  
addTask(3000, 3)    // 8000 3
addTask(4000, 4)    // 12000 4
addTask(5000, 5)    // 15000 5
```

```js
class SuperTask {
    constructor(thread = 2){
        this.paralleCount = paralleCount;  // 任务队列长度
        this.taskQueue = []; // add task,存储task
        this.runingTaskCount = 0;
    }

    private add (task) {
        return new Promise((resolve,reject)=>{
            this.taskQueue.push({task,resolve,reject});
            this.run();
        })
    }

    // 使用任务队列消耗task
    private run () {
        // 有任务需要执行并且正在执行的任务没有超过任务队列的长度
        if(this.taskQueue.length > 0 && this.runingTaskCount < this.paralleCount){
            this.runingTaskCount++;
            const {task,resolve,reject} = this.taskQueue.shift();
            task().then(resolve,reject).finally(res=>{
                this.runingTaskCount--;
                this.run();
            })
        }
    }
}
```

## setTimeout、promise、async await

> await 后面的表达式会同步方式执行,await 下面的代码以微任务的方式执行

```js
async function async1() {
    console.log('async1 start');
    await async2();
    console.log('async1 end');
}
async function async2() {
    console.log('async2');
}
console.log('script start');
setTimeout(function() {
    console.log('setTimeout');
}, 0)
async1();
new Promise(function(resolve) {
    console.log('promise1');
    resolve();
}).then(function() {
    console.log('promise2');
});
console.log('script end');
```

输入结果为

```js
script start
async1 start
async2
promise1
script end
async1 end
promise2
setTimeout
```