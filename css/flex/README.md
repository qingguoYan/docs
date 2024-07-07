# Flex弹性布局常识

## Flex容器与Flex元素
    使用display:flex或者display:inline-flex生命弹性盒子容器,前者是块级盒子,后者是行内盒子
    Flex 元素是 Flex 容器中的直接子元素

## Flex 容器的属性
    flex-direction: 定义 Flex 项目的主轴方向,可选值有 row、row-reverse、column、column-reverse
    flex-wrap: 定义 Flex 项目是否换行,可选值有 nowrap、wrap、wrap-reverse
    justify-content: 定义主轴上的对齐方式,可选值有 flex-start、flex-end、center、space-between、space-around、space-evenly
    align-items: 定义交叉轴上的对齐方式,可选值有 flex-start、flex-end、center、baseline、stretch
    align-content: 定义多根主轴线的对齐方式,可选值有 flex-start、flex-end、center、space-between、space-around、stretch

## Flex 元素的属性
    order: 定义 Flex 项目的排列顺序,值越小越靠前
    flex-grow: 定义 Flex 项目的放大比例,默认为 0,即不放大
    flex-shrink: 定义 Flex 项目的缩小比例,默认为 1,即会缩小
    flex-basis: 定义 Flex 项目在主轴方向上的初始大小
    flex: 是 flex-grow、flex-shrink 和 flex-basis 的简写
    align-self: 定义单个 Flex 项目在交叉轴上的对齐方式,可覆盖 align-items 的值

## flex-shrink 与 width

### flex-shrink 不为0(默认为1,可以缩小):

    当 Flex 容器中的空间不足时,Flex 项目会根据自身的 flex-shrink 属性值进行收缩
    即使 Flex 项目设置了 width 属性,如果 flex-shrink 不为 0,那么 width 属性将会被忽略,Flex 项目会根据 flex-shrink 进行收缩

### flex-shrink 为0:

    如果 Flex 项目的 flex-shrink 属性为 0,那么即使容器空间不足,Flex 项目也不会收缩
    此时 width 属性会生效,Flex 项目会保持设置的宽度,不会被压缩

### min-width 和 max-width 也会影响收缩行为:

    即使 Flex 项目的 flex-shrink 不为 0,但如果设置了 min-width，Flex 项目也不会收缩到小于 min-width 的值
    同理,如果设置了 max-width，Flex 项目也不会收缩到大于 max-width 的值

## flex-grow 与 width

### flex-grow 不为0

    当 Flex 容器中存在剩余空间时,Flex 项目会根据自身的 flex-grow 属性值进行放大
    即使 Flex 项目设置了 width 属性,如果 flex-grow 不为 0,那么 width 属性将会被忽略,Flex 项目会根据 flex-grow 进行放大

### flex-grow 为0(默认为0,不可以放大)

    如果 Flex 项目的 flex-grow 属性为 0,那么即使容器中有剩余空间,Flex 项目也不会放大
    此时 width 属性会生效,Flex 项目会保持设置的宽度,不会被拉伸

### min-width 和 max-width 也会影响放大行为:

    即使 Flex 项目的 flex-grow 不为 0,但如果设置了 min-width，Flex 项目也不会放大到小于 min-width 的值
    同理,如果设置了 max-width，Flex 项目也不会放大到大于 max-width 的值

## flex-basis 与 width的优先级

    flex-basis 优先于 width:
    当 Flex 项目的 flex-basis 属性被设置时,它会优先于 width 属性,决定 Flex 项目在主轴方向上的初始大小
    即使 Flex 项目同时设置了 width 属性,只要 flex-basis 属性被设置,width 属性就会被忽略
    即使 Flex 项目设置了 flex-basis，但如果同时设置了 min-width、max-width，这些属性也会影响 Flex 项目的最终尺寸
    比如 flex-basis: 200px 和 max-width: 150px，那么 Flex 项目的最终宽度就会是 150px