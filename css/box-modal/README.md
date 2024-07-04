# 什么是盒模型

CSS 盒模型描述了网页元素如何计算大小和位置。每个 HTML 元素都被视为一个矩形的盒子,这个盒子由以下几个部分组成:

内容区域(Content): 元素的实际内容,如文本、图像等。

内边距(Padding): 内容区域和边框之间的空间。

边框(Border): 包围内容区域和内边距的线条。

外边距(Margin): 元素与其他元素之间的空间。

盒模型的宽度和高度计算公式如下:

>总宽度 = 内容宽度 + 左右内边距 + 左右边框 + 左右外边距

>总高度 = 内容高度 + 上下内边距 + 上下边框 + 上下外边距

CSS 提供了两种盒模型:

>标准盒模型(content-box): 元素的宽度和高度仅包括内容区域,不包括内边距和边框。

>IE盒模型(border-box): 元素的宽度和高度包括内容区域、内边距和边框。

可以通过 box-sizing 属性来切换盒模型:

```css
/* 标准盒模型 */
box-sizing: content-box;

/* IE盒模型 */
box-sizing: border-box;

```
# 设置height、width有什么区别

1. 标准盒模型(content-box):

height 属性只包括内容区域的高度。
如果设置了 padding 和 border, 它们会被加到 height 之外,导致元素的实际高度大于设置的 height。

```css
.box {
  width: 200px;
  height: 100px;
  padding: 10px;
  border: 5px solid black;
}
```
此时元素的实际高度为 100px + 10px + 10px + 5px + 5px = 130px。

2. IE盒模型(border-box):

height 属性包括内容区域、内边距和边框的高度。
设置 height 后,内容区域的高度会根据 padding 和 border 的值自动调整。

```css
.box {
  width: 200px;
  height: 100px;
  padding: 10px;
  border: 5px solid black;
  box-sizing: border-box;
}
```
此时元素的实际高度仍为 100px,因为 height 包括了内边距和边框。

宽度同理