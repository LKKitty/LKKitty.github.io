---
title: typescript入门
date: 2020-03-31 15:08:32
tags: 
  - typescript
  - js
---
typescript是javascript的超集~
<!-- more -->
## 接口
TypeScript 中，我们使用接口来描述对象或类的具体结构。接口的概念在 TypeScript 中是至关重要的。它就像是你与程序签订的一个契约，定义一个接口就意味着你答应程序：未来的某个值（或者类）一定会符合契约中所规定的模样，如果不符合，TS 就会直接在编译时报错。
例如：
```js
interface Phone {
    model: string
    price: number
}

let newPhone: Phone = {
    model: 'iPhone XS',
    price: 8599,
}
```
上面的例子中，我们定义了一个接口 Phone，它约定：任何类型为 Phone 的值，有且只能有两个属性：string 类型的 model 属性以及 number 类型的 price 属性。之后，我们声明了一个变量 newPhone，它的类型为 Phone，而且遵照契约，将 model 赋值为字符串，price 赋值为数值。多一些属性和少一些属性都是不允许的。
```
接口一般首字母大写。在某些编程语言会建议使用 I 作为前缀。关于是否要使用 I 前缀，tslint 有一条 专门的规则，请根据团队编码风格自行选择。
```
### 可选属性
对于某个可能存在的属性，我们可以在该属性后加上 ?标记 表示这个属性是可选的。
```js
interface Phone {
    model: string
    price: number
    producer?: string
}

let newPhone: Phone = {
    model: 'iPhone XS',
    price: 8599, // OK
}

let phoneB: Phone = {
    model: 'iPhone XS',
    price: 8599,
    producer: 'Apple', // OK
} 

```
### 任意属性
某些情况下，我们可能只知道接口中部分属性及它们的类型。或者，我们希望能够在初始化后动态添加对象的属性。这时，我们可以使用下面这种写法。
```js
interface Phone {
    model: string
    price: number
    producer?: string
    [propName: string]: any
}

let phoneB: Phone = {
    model: 'iPhone XS',
    price: 8599,
} 
phoneB.storage = '256GB' // OK
```
上面，我们定义任意属性的签名为 string 类型，值为 any 类型。注意：任意属性的值类型必须包含所有已知属性的值类型。 上述例子中，any 包括 string 和 number 类型。
### 只读属性
接口中，我们可以使用 readonly 标记某个属性是只读的。当我们试图修改它时，TS 会提示报错。
```js
interface Phone {
    readonly model: string
    price: number
}

let phoneA: Phone = {
    model: 'iPhone XS',
    price: 8599,
}
phoneA.model = 'iPhone Air' // Error: Cannot assign to 'model' because it is a read-only property.

```
### 函数类型
接口能够描述JavaScript中对象拥有的各种各样的外形。 除了描述带有属性的普通对象外，接口也可以描述函数类型。

为了使用接口表示函数类型，我们需要给接口定义一个调用签名。 它就像是一个只有参数列表和返回值类型的函数定义。参数列表里的每个参数都需要名字和类型。
```js
interface SearchFunc {
  (source: string, subString: string): boolean;
}
```
## 参考资料
<a href="https://juejin.im/post/5ca0a9ae51882568173341bf#heading-16">TypeScript 简明教程：接口、函数与类</a>