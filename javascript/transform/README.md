# 转为Boolean类型

    0、-0、false、null、""、undefined、NaN转为boolean值都为false
    其余都视为true

# 转为字符串类型

    1. 数字转为字符串表示
    2. 布尔类型转为字符串"true","false"
    3. null 与 undefined转为 "null"、"undefined"
    4. 对象会首先调用 toString() 方法,如果 toString() 方法没有返回原始值,则会调用 valueOf() 方法
       如果两个方法都没有返回原始值,则返回 "[object Object]"
       - Date 对象在转换为字符串时,会返回该对象表示的日期和时间的字符串表示
       - Map 和 Set 对象在转换为字符串时,会返回一个包含其元素的字符串
    5. Symbol转为 "Symbol()"

```js
const date = new Date();
date.toString(); // "Fri Jul 07 2023 21:01:00 GMT+0000 (China Standard Time)"

const map = new Map([['key1', 'value1'], ['key2', 'value2']]);
map.toString(); // "[object Map]"

const set = new Set([1, 2, 3]);
set.toString(); // "[object Set]"
```

# 转为Number类型

    1. 字符串:如果字符串包含有效的数字,则会被转换为相应的数值,如果字符串为空或仅包含空白字符,则会被转换为 0
       如果字符串不能被解析为有效的数字,则会被转换为 NaN
    2. 布尔值: true为1,false为0
    3. null 为0
    4. undefined 为NaN
    5. Symbol 不能转为数值,会报错
    6. 引用类型对象首先会调用 valueOf() 方法,如果返回的值是原始值,则使用该值进行转换,如果 valueOf() 方法返回的不是原始值,则调用 toString() 方法,并使用返回的字符串进行转换,如果上述两个方法都没有返回原始值,则转换结果为 NaN

```js
    Number([]); // 0, [].valueOf返回数组本身,然后toString转为空字符串,空字符转为数字为0
    Number([1, 2, 3]); // NaN
```