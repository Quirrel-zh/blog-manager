---
title: TypeScript 极速梳理
toc: true
date: 2024-09-04 16:06:08
tags: 
    - typescript 
    - javascript
    - web
categories: 
    - TypeScript

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
   let a:Object //a的值必须是Object的实例对象，
   
   // 以下代码，均⽆警告，因为给a赋的值，都是Object的实例对象
   a = {}
   a = {name:'张三'}
   a = [1,3,5,7,9]
   a = function(){}
   a = 1      // 1不是Object的实例对象，但其包装对象是Object的实例
   a = true   // true不是Object的实例对象，但其包装对象是Object的实例
   a = '你好' // “你好”不是Object的实例对象，但其包装对象是Object的实例
   
   // 以下代码均有警告
   a = null       // 警告：不能将类型“null”分配给类型“Object”
   a = undefined  // 警告：不能将类型“undefined”分配给类型“Object”
   ```

3. 实际开发中，限制一般对象，通常使用一下形式

   ```typescript
   // 限制person对象的具体内容，使用','分隔，问号代表可选属性
   let person:{
       name:string,
       age?:number,
   }
   
   // 限制car对象的具体内容，使用';'分隔，必须有price和color属性，其他属性不去限制，有没有都行
   let car:{
       price: number;
       color: string;
       [k:string]:any
   }
   
   // 限制student对象的具体内容，使用'回车'分隔。
   let student:{
       id:string,
       grade:number,
   }
   
   // 以下代码均无警告
   person = {name: '张三', age: 18,}
   person = {name: '李四'}
   car = {price: 100, color: '红色'}
   student = {id: '123', grade: 3}
   ```

4. 限制函数的参数、返回值，使⽤以下形式

   ```typescript
   let demo:(a:number, b:number) => number
   
   demo = function(x, y){
       return x + y
   }
   ```

5. 限制数组，使用以下形式

   ```typescript
   let arr1:string[] // 等价于 let arr1:Array<string>
   let arr2:number[] //等价于 let arr2:Array<number>
   
   arr1 = ['a', 'b', 'c']
   arr2 = [1, 3, 5, 7, 9]
   ```

#### `tuple`

`tuple`就是一个长度固定的数组。

```typescript
let t:[string, number]

t = ['hello', 123]

// 警告：不能将类型“[string, number, boolean]”分配给类型“[string, number]”
t = ['hello', 123, false]
```

#### `enum`

`enum`是枚举

{% codeblock "点击展开" lang:typescript >folded %}

// 定义一个枚举
enum Color {
    Red,
    Blue,
    Blace,
    Gold,
}

// 定义一个枚举，并指定其初始数值
enum Color2{
    Red = 6,
    Blue,
    Black,
    Gold,
}

console.log(Color)
/* 
  {
    0: 'Red', 
    1: 'Blue', 
    2: 'Black', 
    3: 'Gold', 
    Red: 0, 
    Blue: 1, 
    Black: 2, 
    Gold: 3
  }
 */

console.log(Color2)
/* 
  {
    6: 'Red', 
    7: 'Blue', 
    8: 'Black', 
    9: 'Gold', 
    Red: 6, 
    Blue: 7, 
    Black: 8, 
    Gold: 9
  }
 */

// 定义一个phone变量，并对其设置限制
let phone: {
    name:string,
    price:number,
    color:Color,
}

phone = {
    name: 'iPhone',
    price: '6000'
    color: Color.Red
}

if(phone.color === Color.Red){
    console.log('手机是红色的')
}

{% endcodeblock %}

### 自定义类型

⾃定义类型，可以更灵活的限制类型

```typescript
// 性别的枚举
enum Gender {
 Male,
 Female
}

// ⾃定义⼀个年级类型（⾼⼀、⾼⼆、⾼三）
type Grade = 1 | 2 | 3

// ⾃定义⼀个学⽣类型
type Student = {
 name: string,
 age: number,
 gender: Gender,
 grade: Grade
}

// 定义两个学⽣变量：s1、s2
let s1:Student
let s2:Student
s1 = {
    name:'张三',
    age:18,
    gender:Gender.Male,
    grade:1
}
s2 = {
    name:'李四',
    age:18,
    gender:Gender.Female,
    grade:2
}
```

### 抽象类

常规类：

```typescript
class Person {
 name: string
 age: number
 constructor(name:string,age:number){
 	this.name = name
 	this.age = age
 }
}
const p1 = new Person('张三',18)
const p2 = new Person('李四',19)

console.log(p1)
console.log(p2)
```

继承：

```typescript
// Person类
class Person {
    name: string
    age: number
}

// Teacher类继承Person
class Teacher extends Person {
    name: string
    age: number
}

// Student类继承Person
class Student extends Person {
    name: string
    age: number
}

// Person实例
const p1 = new Person('周杰伦', 38)

// Student实例
const s1 = new Student('张同学', 18)
const s2 = new Student('李同学', 20)

// Teacher实例
const t1 = new Teacher('刘⽼师', 40)
const t2 = new Teacher('孙⽼师', 50)
```

抽象类：不能去实例化，但可以被别⼈继承，抽象类⾥有抽象⽅法

```typescript
// Person（抽象类）
abstract class Person {
    name: string
    age: number
}

// Teacher类继承Person
class Teacher extends Person {
    name: string
    age: number
 	// 构造器
	constructor(name: string, age: number){
 		super(name, age)
    }
	// ⽅法
	speak(){
		 console.log('你好！我是⽼师:', this.name)
    }
}

// Student类继承Person
class Student extends Person {
    name: string
    age: number
}

// Person实例
// const p1 = new Person('周杰伦',38) 
// 由于Person是抽象类，所以此处不可以new Person的实例对象
```

### 接口

接口梳理：

 1. 接口用于限制一个类中包含那些属性和方法：

    ```typescript
    // Person 接口
    interface Person {
        // 属性声明
        name: string
        age: number
        // 方法声明
        speak():void
    }
    
    // Teacher实现Person接口
    class Teacher implements Person {
        name: string
        age: number
        // 构造器
        constructor(name: string, age: number){
            this.name = name
            this.age = age
        }
        // 方法
        speak(){
            console.log('你好，我是：', this.name)
        }
    }
    ```

 2. 接口是可以重复声明的：

    ```typescript
    // Person接口 
    interface PersonInter {
        // 属性声明
        name: sting
        age: number
    }
    
    // Person接口
    interface PersonInter {
        // 方法声明
        speak():void
    }
    
    // Person类继承PersonInter
    class Person implements PersonInter {
        name: string
        age: number
        // 构造器
        constructor(name: string, age: number){
            this.name = name
            this.age = age
        }
        // 方法
        speak(){
            console.log('你好，我是：', this.name)
        }
    }
    ```

 3. "接口"与"自定义类型"的区别：

    {% message color:info %}
     接口可以：
    
     ​ 1. 当自定义类型去使用
          2. 可以限制类的结构

    自定义类型：
    
    ​	 1. 仅仅就是自定义类型

    {% endmessage %}

    ```typescript
    // Person接口
    interface Person{
        // 应该具有的属性
        name: string
        age: number
        // 应该具有的方法
        speak():void
    }
    // Person类型
    /* 
      * type Person = {
      *   name: string
      *   age: number
      * }
    */
       
    // 接口当自定义类型去使用
    let person:Person = {
        name: '张三',
        age: 18,
        speak(){
            console.log("你好")
        }
    }
    ```

    

4. "接口"与"抽象类"的区别

   {% message color:info %}
   抽象类：

   ​    1. 以有普通方法，也可以有抽象方法
   ​        2. 用`extends`关键字去继承抽象类
   接口中：

   ​    1. 只能有抽象方法
   ​	    2. 使用`implements`关键字去实现接口
   {% endmessage %}

   抽象类举例：

   ```typescript
   // 抽象类 ——— Person
   abstract class Person{
       // 属性
       name: string
       age: number
       // 构造器
       constructor(name:string, age:number){
           this.name = name
           this.age = age
       }
       // 抽象方法
       abstract speak():void
       // 普通方法
       walk(){
           console.log('我在走')
       }
   }
   // Teacher类继承抽象类Person
   class Teacher extends Person {
       constructor(name:string, age:number){
           super(name, age)
       }
       speak(){
           console.log(`我是${this.name}`)
       }
   }
   ```

   接口举例：

   ```typescript
   // 接口 ——— Person, 只能包含抽象方法
   interface Person {
       // 属性，不写具体值
       name: string
       age: number
       // 方法，不写具体实现
       speak():void
   }
   // 创建Teacher类实现Person接口
   class Teacher implements Person {
       name: string
       age: number
       constructor(name:string, age:number){
           this.name = name
           this.age = age
       }
       speak(){
           console.log('我在走')
       }
   }
   ```


### 属性修饰符

|   修饰符    |   含义   |            解释            |
| :---------: | :------: | :------------------------: |
| `readonly`  | 只读属性 |        属性无法更改        |
|  `public`   |  公开的  | 可以在类、子类和对象中修改 |
| `protected` | 受保护的 |    可以在类、子类中修改    |
|  `private`  |  私有的  |       可以在类中修改       |



### 泛型

定义一个函数或类时，有些情况下无法确定其中要使用的具体类型（返回值、参数、属性的类型不能确定），此时就需要泛型了

举例：`<T>`就是泛型，（不一定非叫`<T>`），设置泛型之后即可在函数中使用`T`来表示该类型：

```typescript
function text<T>(arg: T): T{
    return arg;
}

// 不指明类型，TS会自动推断出来
test(10)

// 指明具体的类型
test<number>(10)
```

泛型还可以写多个：

```typescript
function test<T, K>(a: T, b: K) K{
	return b
}

// 为多个泛型指定具体值
test<number, string>(10, 'hello')
```

类中同样可以使用泛型

```typescript
class MyClass<T>{
    prop: T
    
    constructor(prop: T){
        this.prop = prop
    }
}
```

也可以对泛型的范围进行约束：

```typescript
interface Demo{
    length: number
}

// 泛型T必须是MyInter的子类，即：必须拥有length属性
function text<T extends Demo>(arg: T): number{
    return arg.length
}

test(10) // 类型“number”的参数不能赋给类型“Demo”的参数
test({name: '张三'}) // 类型“test({name: '张三'})”的参数不能赋给类型“Demo”的参数

test('123')
test({name:'张三', length: 10})
```

### 结语

希望以上笔记能对你有帮助，当然如果你在使用TS时弄不清类型，又不想看到警告，完全可以使用`any`声明。这也是为什么`TypeScript`又被大家叫做`AnyScript`🤪
