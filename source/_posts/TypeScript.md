---
title: TypeScript 极速梳理
toc: true
date: 2024-09-04 16:06:08
tags:
---
`JavaScript`是非常灵活的一门编程语言，但是这种灵活是把双刃剑，一方面使得JavaScript蓬勃发展，无所不能；另一方面也使它的代码质量参差不齐，维护成本高，运行时错误多。

`TypeScript`是具有类型的JavaScript，在很大程度上弥补了它的缺点。
<!-- more -->

### 类型声明

给变量声明类型：

```typescript
let a: string // 变量a只能存储字符串
let b: number // 变量b只能存储数值
let c: boolean // 变量c只能存储布尔值

a = 'hello'
a = 100 // 警告：不能将类型"number"分配给类型"string"

b = 666
b = '你好' // 警告：不能将类型"string"分配给类型"number"

c = true
c = 666 // 警告： 不能将类型"number"分配给类型"boolean"
```

给参数声明类型

```typescript
// 给x和y传递参数时必须传递数字类型，返回值也必须是数字
function demo(x:number,y:number):number{
 return x + y
}

demo(100, 200)
demo(100, '200') // 警告：类型"string"的参数不能赋给类型"number"的参数
demo(100, 200, 300) // 警告：应有 2 个参数，但获得 3 个
```

### 类型推断

在没有声明类型的情况下，ts会自行推断出类型

```typescript
let d = -99  // 此时会自动推断变量d的类型为数字
d = false  // 警告：不能将类型"boolean"分配给类型"number"
```

### 类型总览

`JavaScript`中的数据类型：

{% message color:warning title:JavaScript %}
   `string`、`number`、`boolean`、`null`、`undefined`、`bigint`、`symbol`、`object`
{% endmessage %}

> 备注： 其中`object`包括：`Array`、`Function`、`Date`.....

`TypeScript`中的数据类型：

{% message color:primary title:TypeScript %}

​	· 以上所有

​	· 四个新类型：`void`、 `never`、`unknown`、`any`、`enum`、`tuple`

​	· 自定义类型：`type`&`interface`

{% endmessage %}

> 注意：`JavaScript`中的三个构造函数：`Number`、`String`、`Boolean`，他们只用于包装对象，正常开发时，很少去使用他们，在`TypeScript`中也是同理

总览：

|   类型    |               描述               |              举例              |
| :-------: | :------------------------------: | :----------------------------: |
| `number`  |             任意数字             |        `1`，`-1`，`1.5`        |
| `string`  |            任意字符串            | `'nihao'`，`'你好'`，`'hello'` |
| `boolean` |              布尔值              |         `true`&`false`         |
| `字面量`  |         值只能是字面量值         |             值本身             |
|   `any`   |             任意类型             |  `1`，`'hello'`，`true`......  |
| `unknown` |         类型安全的`any`          |  `1`，`'hello'`，`true`......  |
|  `never`  |           不能是任何值           |              无值              |
|  `void`   |        空 或 `undefined`         |       空 或 `undefined`        |
| `object`  |           任意`JS`对象           |        `{name: '张三'}`        |
|  `tuple`  | 元素，`TS`新增类型，固定长度数组 |            `[3, 4]`            |
|  `enum`   |        枚举，`TS`新增类型        |          `enum{A, B}`          |

### 常用类型

#### 字面量

```typescript
let a: '你好' // a的值只能是字符串'你好'
let b: 100 // b的值只能是数字100	

a = 'hello' // 警告：不能将类型“'hello'”分配给类型“'你好'”
b = 200  // 警告：不能将类型“200”分配给类型“100”

let gender: '男'|'女' // 定义一个变量gender，值只能为字符串“男”或“女”
gender = '男'
gender = '未知' // 警告：不能将类型“'未知'”分配给类型“'男'|'女'”
```

#### any

`any`的含义是：任何类型。 一旦将变量类型限制为`any`，那就意味着放弃了对该变量的类型检查。

```typescript
// 明确表示a的类型是any —— 显式的any
let a: any 
// 以下对a的赋值，均⽆警告
a = 100
a = '你好'
a = false
 
// 没有明确的表示b的类型是any，但TS主动推断了出来 —— 隐式的any
let b 
// 以下对b的赋值，均⽆警告
b = 100
b = '你好'
b = false
```

> 注意： any  类型的变量，可以赋值给任意类型的变量 ：
>
> ```typescript
> let a
> let x: string
> x = a // ⽆警告
> ```

#### unknown
