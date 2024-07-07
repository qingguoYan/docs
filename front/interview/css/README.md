# 如何实现水平垂直居中

> Flex布局

```css
.container {
  display: flex;
  justify-content: center;
  align-items: center;
}
```
> Grid布局

```css
.container {
  display: grid;
  grid-template-columns: 1fr;
  grid-template-rows: 1fr;
}
.item {
  grid-column: 1 / 2;
  grid-row: 1 / 2;
  justify-self: center;
  align-self: center;
}
```
> 绝对定位+负边距
```css
.container {
  position: relative;
}
.item {
  position: absolute;
  top: 50%;
  left: 50%;
  transform: translate(-50%, -50%);
}
```

# 如何实现0.5px的线

> 使用 transform 属性

```css
.line {
  height: 1px;
  background-color: #000;
  transform: scaleY(0.5);
}
```