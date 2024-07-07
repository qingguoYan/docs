# Grid弹性布局常识

## Grid 容器和 Grid 元素

    Grid 容器是使用 display: grid 或 display: inline-grid 声明的容器
    Grid 元素是 Grid 容器中的直接子元素

## Grid 容器的属性

    grid-template-columns 和 grid-template-rows: 定义网格的列和行
    grid-gap: 定义网格线之间的间距,包括 grid-column-gap 和 grid-row-gap
    justify-content, align-content, justify-items, align-items: 定义网格容器内部的对齐方式

## Grid 元素的属性

    grid-column 和 grid-row: 定义 Grid 元素占用的列和行
    grid-area: 是 grid-row-start, grid-column-start, grid-row-end, grid-column-end 的简写
    justify-self 和 align-self: 定义单个 Grid 项目在所在单元格内的对齐方式