# 浏览器重排与回流

- 重排意味着几何变化，性能影响大
- 回流意味着样式变化，性能影响小
- 使用transform实现动画，不会引起重排，浏览器会单独生成新的渲染层，不影响原有布局，并进行GPU加速，只发生回流

# 浏览器输入URL后发生了什么

![图片说明](./asset/浏览器输入URL发生了什么.drawio.svg)

# async与defer？document.DOMContentLoaded 与 window.load

* defer与async都在后台下载脚本，不会阻塞解析DOM树
* defer在DOM树构建后按照文档顺序执行脚本，但在DOMContentLoaded生命周期前执行
* async脚本之间都是独立的，按照加载顺序执行，可能在DOMContentLoaded前执行也可能在DOMContentLoaded之后
* DOMContentLoaded在完全加载完HTML文件并将**DOM树**构建后执行，此时一些外部资源如样式表、图片可能还没下载完毕
* 外部样式表资源不会阻塞DOM树解析，会阻塞DOM树的渲染，JS需要等待CSS资源下载完毕才能运行，因为JS可能操作DOM的样式
* 如果外部样式表在defer脚本或者正常脚本之前，DOMContentLoaded也要在外部样式表资源加载后执行，因为脚本可能修改样式
* onload时所有资源全部加载完毕
* [参考资料](https://zh.javascript.info/script-async-defer)

![图片说明](./asset/load-document.webp)

# 函数柯里化
> 函数柯里化(Currying)是一种将一个接受多个参数的函数转换成一系列接受单个参数的函数的技术。它是函数式编程中的一个重要概念

[参考资料](https://zh.javascript.info/currying-partials)

```js
function curry (fn){
    return function curried (...args){
        if(args2.length >= fn.length){
           return fn.call(this,...args);
        }else{
            return function(...args2){
                return curried.call(this,...args.concat(args2));
            }
        }
    }
}
```

# 如何实现防抖

* 定时器实现

```javascript
    function debounce(fn: Function, deleay: number, isLeading?: boolean) {
        let timer;
        let isFirst = true;
        return function (...arg) {
                    if(isLeading && isFirst){
                        isFirst = false;
                        fn.apply(this,arg);
                    }
                    if(timer){
                        clearTimeout(timer);
                    }
                    timer = setTimeout(()=>{
                               fn.apply(this,arg);
                            }, deleay)
                }
    }
```

* 时间戳实现,默认第一次直接执行

```javascript
    function debounce(fn: Function, deleay: number) {
        let last = 0;
        return function (...arg) {
            const now = new Date().valueOf();
            if(now - last >= deleay){
                fn.apply(this,arg);
            }
            last = now;
        }
    }
```

# 如何实现节流

```javascript
   function debounce(fn: Function, deleay: number) {
        let last = 0;
        return function (...arg) {
            const now = new Date().valueOf();
            if(now - last >= deleay){
                last = now;
                fn.apply(this,arg);
            }
        }
    }
```

# 实现一个Promise.All

```javascript
function promisAll(promisList){
    return new Promise((resolve,reject) =>{
        const length = promistList.length;
        if(!Array.isArray(promisList) || length === 0){
            resolve([]);
        }
        let count = 0;
        const results = new Array(length);
        promiseList.forEach((promise,index)=>{
            Promise.resolve(promise).then((res)=>{
                count++;
                results[index] = res;
                if(count === length){
                    resolve(results);
                }
            }).catch((error)=>{
                reject(error);
            })
        })
    })
}
```

# 实现一个深拷贝

## JSON.stringfy

- 方便快捷，但是不能拷贝Regex、Symbol、Date、undefined、function，信息会丢失。 
- 拷贝循环引用对象会报错TypeError: Converting circular structure to JSON
```javascript
const obj = {
              name: 'John',
              age: 30
            }
obj.self = obj;
```

## lodash库的deepCopy

```javascript
const _ = require('lodash');
const clonedObj = _.cloneDeep(originalObj);
```

## 递归遍历

```javascript
function deepCopy(obj){
    if (typeof obj !== 'object' || obj === null) {
        return obj;
    }
    let clone;
    if(Array.isArray(obj)){
        clone = [];
        for(let i = 0; i < obj.length ;i++){
            clone[i] = deepCopy(obj[i])
        }
    } else{
        clone = {}
        // key in obj会遍历原型链上继承的属性
        for(let key in obj){
            // 只会判断属于自身可枚举的属性才会true
            if(obj.hasOwnProperty(key)){
                clone[key] = deepCopy(obj[key])
            }
        }
    }
    return clone;
}
```

# 实现私有属性

## 闭包

```js
function Persion(name){
    let _name = name;
    return {
        getName: function() {
            return _name;
        },
        setName: function(newName) {
            _name = newName;
        }
  };
}
```

## symbol

```js
const _name = Symbol('name');

class Person {
  constructor(name) {
    this[_name] = name;
  }

  getName() {
    return this[_name];
  }

  setName(newName) {
    this[_name] = newName;
  }
}

const person = new Person('John');
console.log(person.getName()); // 输出: 'John'
person.setName('Jane');
console.log(person.getName()); // 输出: 'Jane'
console.log(person[_name]); // 输出: 'Jane' (可以访问,但不推荐)
```

## 使用ES新语法 # 定义私有属性和方法

```js
class Person {
  #name;

  constructor(name) {
    this.#name = name;
  }

  getName() {
    return this.#name;
  }

  setName(newName) {
    this.#name = newName;
  }
}

const person = new Person('John');
console.log(person.getName()); // 输出: 'John'
person.setName('Jane');
console.log(person.getName()); // 输出: 'Jane'
console.log(person.#name); // 报错: 无法直接访问私有字段

```

# 数组slice、splice、shift、unshift、pop区别

 slice 截取数组，返回一个截取的数组，不会改变原数组，参数为起始索引、结束索引，不包含结束索引
  ```javascript
    const arr = [1,2,3,4];
    const s = arr.slice(1,2); // 参数为起始索引、结束索引，不包含结束索引
    // s = [2]; arr = [1,2,3,4]
  ```
 splice 删除或者插入元素，第一个参数为起始索引，第二个参数为删除的长度，第三个参数为插入的元素，会改变原数组
  ```js
    const arr = [1,2,3,4];
    arr.splice(2,0,5); // 在索引2插入元素5 => [1,2,5,3,4]
  ```
 shfit 删除原数组第一个元素，返回删除的元素
 ```js
    const arr = [1,2,3,4];
    arr.shfit(); // 1
    // arr 为 [2,3,4]
  ```
 unshift 数组开头插入元素，返回数组的长度
   ```js
    const arr = [1,2,3,4];
    arr.unshift(5,6,7); // 长度为7
    // arr 为 [5,6,7,1,2,3,4]
  ```
 pop 删除数组最后一个元素，返回最后一个元素

# 代码题

## 对象引用

> 连续赋值从左向右进行赋值

```js
var a = {n: 1};
var b = a;
a.x = a = {n: 2}; 
// a.x = a; 得到 a:{n:1 x:{n:1}}
// a = {n:2}; 得到 a:{n:2};
console.log(a.x) // undefined	
console.log(b.x) // {n:2}
```

## IIFE函数

> 立即执行函数自带词法作用域,不能从外部访问函数名. IIFE函数名称不能被修改

```js
var b = 10;
(function b() {
  b = 20; // IIFE函数名称不能被修改,b=20修改失败
  c = 100; // 没有通过var 或者let 、 const修饰的变量,保存到全局对象上
  console.log(b)
})()
console.log(b); // 10
console.log(c); // 100
```

## 对象比较

new String 创建一个object, == 非严格比较会调用toString方法
```js
    String(123) == new String("123"); // true toString() 为 ”123“
    String(123) === new String("123"); // false
```