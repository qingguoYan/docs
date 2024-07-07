# type 与 interface的区别

    type 和 interface 在 TypeScript 中都用于定义对象类型,但它们之间有一些区别:

    1. 语法:
    type 使用 type MyType = { ... } 的语法定义类型别名
    interface 使用 interface MyInterface { ... } 的语法定义接口

    2.可扩展性:
    interface 可以使用 extends 关键字进行扩展,创建新的接口
    type 可以使用 & 操作符进行交叉类型的组合,创建新的类型

    3. 合并:
    同名的 interface 会自动合并,属性会被合并到一个接口中
    同名的 type 别名会报错,不会自动合并

    4.字面量类型:
    type 可以用于定义字面量类型,如 type Color = 'red' | 'green' | 'blue'
    interface 不能直接定义字面量类型,但可以通过索引签名实现类似的功能

    5. 可重用性:
    interface 更适合定义可重用的对象类型,因为可以通过继承进行扩展
    type 更适合定义一次性的类型别名,如联合类型或交叉类型