# 内存泄漏出现的情况

- 事件监听器未被移除

```javascript
button.addEventListener("click", handleButtonClick);
function handleButtonClick() {
  console.log("Button clicked!");
}
```

- 闭包引用了外部变量
  handleClick 函数保持着对 element 的引用，handleClick 执行后，element 不会被释放

```javascript
function addClickListener() {
  const element = document.getElementById("myElement");
  element.addEventListener("click", function handleClick() {
    console.log("Element clicked!");
  });
}
addClickListener();
```

解决方法

```javascript
function addClickListener() {
  const element = document.getElementById("myElement");
  const handleClick = function () {
    console.log("Element clicked!");
  };
  element.addEventListener("click", handleClick);
  return () => {
    element.removeEventListener("click", handleClick);
  };
}

const removeClickListener = addClickListener();
// 当不需要监听器时调用 removeClickListener()
```

- 定时器没有被清除

```javascript
const timer = setTimeout(() => {}, 100);
// 不许要定时器时，清除定时器
clearTimeout(timer);
```

- 不使用的全局变量未置为 null

```javascript
window.onload = () => {
  //页面所有资源加载完成后触发
};
// 执行完毕后，释放内存
window.onload = null;
```

- 游离的 dom 节点

```javascript
let a = document.getElementById("id");
document.body.removeChild(a);
// 虽然元素被移除，但是内存中仍然保留着DOM元素的引用
a = null; // 释放内存
```

# 内存泄漏如何排查

- 通过 chrome 浏览器自带的 Performance 面板，勾选 Memory 开始录制，模拟用户操作，之后手动进行垃圾回收，结束录制，观察内存占用变化，或者在 memory 面板生成快照，对比快照信息
- 使用 Meta 的内存泄漏检测工具 MemLab [https://github.com/facebook/memlab](https://github.com/facebook/memlab)
