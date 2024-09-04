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
let a:string // 变量a只能存储字符串
let b:number // 变量b只能存储数值
let c:boolean // 变量c只能存储布尔值

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

#### `字面量`

```typescript
let a:'你好' // a的值只能是字符串'你好'
let b:100 // b的值只能是数字100	

a = 'hello' // 警告：不能将类型“'hello'”分配给类型“'你好'”
b = 200  // 警告：不能将类型“200”分配给类型“100”

let gender:'男'|'女' // 定义一个变量gender，值只能为字符串“男”或“女”
gender = '男'
gender = '未知' // 警告：不能将类型“'未知'”分配给类型“'男'|'女'”
```

#### `any`

`any`的含义是：任何类型。 一旦将变量类型限制为`any`，那就意味着放弃了对该变量的类型检查。

```typescript
// 明确表示a的类型是any —— 显式的any
let a:any 
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
> let x:string
> x = a // ⽆警告
> ```

#### `unknown`

`unknown`的含义是：未知类型

> 1. `unknown`可以理解为一个类型安全的`any`
> 2. `unknown`适用于：开始不知道数据的具体类型，后期才能确定数据的类型

```typescript
// 设置a的类型为unknown
let a: unknown

// 以下对a的赋值均无警告
a = 100
a = false
a = '你好'

// 设置x的数据类型为string
let x:string
x = a // 警告： 不能将类型'unknown'分配给类型'string'
```

如果想把a赋值给x， 可以用以下三种写法：

```typescript
// 设置a的类型为unknown
let a:unknown
a = 'hello'

// 1、类型判断
if (typeof a === 'string'){
    x = a
}

// 2、断言
x = a as string

// 3、断言的另外一种写法
x = <string>a
```

`any`后点任何的东西都不会报错，`unknown`则相反

```typescript
let str1:string = 'hello'
str1.toUpperCase() // 无警告

let str2:any = 'hello'
str2.toUpperCase() // 无警告

let str3:unknown = 'hello'
str3.toUpperCase() // 警告："str3"的类型为未知

// 使用断言强制指定str3的类型为string
(str3 as string).toUpperCase() // 无警告
```

#### `never`

`never`的含义是：任何值都不是，简言之就是不能有值，`undefined`、`null`、`''`、`0`都不行！

 1. 几乎不用`never`去限制变量，因为没有意义，例如：

    ```typescript
    // 指定a的类型为never，那就意味着a以后不能存任何的数据
    let a:never
    
    // 以下对a的赋值都会有警告
    a = 1
    a = true
    a = undefined
    a = null
    ```

 2. `never`一般是`TypeScript`主动推断出来的，例如：

    ```typescript
    // 指定a的类型为string
    let a:string
    // 给a设置一个值
    a = 'hello'
    
    if(typeof a === 'string'){
        a.toUpperCase()
    } else{
        console.log(a) // TypeScript会推断出此处的a是never，因为没有任何一个值符合此处的逻辑
    }
    ```

    

3. `never`也可以用于限制函数的返回值

   ```typescript
   // 限制demo函数不需要有任何返回值，任何值都不行，想undefined和null都不行
   function demo():never{
       throw new Error('程序异常退出')
   }
   ```

   

#### `void`

`void`的含义是：`空`或者`undefined`，严格模式下不能加`null`赋值给`void`类型。

```typescript
let a:void = undefined

// 严格模式下，该行会有警告：不能将类型“null”分配给类型“void”
let b:void = null
```

`void`常用于限制函数返回值

```typescript
// 无警告
function demo1():void{
    
}

// 无警告
function demo2():void{
    return
}

// 无警告
function demo3():void{
    return undefined
}

// 警告：不能将类型“number”分配给类型“void”
function demo4():void{
    return 666
}
```

#### `object`

关于`Object`与`object`，直接说结论：在类型限制时，`Object`几乎不用，因为范围太大没有意义。

1. `object`  的含义：任何【⾮原始值类型】，包括：对象、函数、数组等，限制的范围⽐较宽泛，⽤的少

   ```typescript
   let a:object // a的值可以是任何【⾮原始值类型】，包括：对象、函数、数组等
   
   // 以下代码，是将【⾮原始类型】赋给a，所以均⽆警告
   a = {}
   a = {name: '张三'}
   a = [1, 3, 5, 7, 9]
   a = function(){}
    
   // 以下代码，是将【原始类型】赋给a，有警告
   a = null      // 警告：不能将类型“null”分配给类型“object”
   a = 1         // 警告：不能将类型“number”分配给类型“object”
   a = true      // 警告：不能将类型“boolean”分配给类型“object”
   a = undefined // 警告：不能将类型“undefined”分配给类型“object”
   a = '你好'    // 警告：不能将类型“string”分配给类型“object”
   ```

2. `Object`的含义：`Object`的实例对象，限制范围太大了，几乎不用

   ```typescript
   ```

   

   
