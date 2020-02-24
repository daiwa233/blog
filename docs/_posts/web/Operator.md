---
title: 比较运算符
author: dva
date: 2020-02-24
tags: ['javascript']
category: ['web','web/javascript']
---
javascript中有8种比较运算符，分别是 `>` `<` `<=` `>=` `==` `===` `!=` `!==`
这些比较运算符大致可分为三类：非相等运算、相等运算和不等运算
<!-- more -->
## 非相等运算
对于非相等比较来来说，如果两个运算元都是字符串，则比较每个字符的Unicode码点，不满足这个条件的话，就先将两个运算元先转换为数值，再继续比较

### 两个运算元都是字符串
两个运算元都是字符串,则分别对每个运算元的单个字符做 charCodeAt()运算，再相加，最终得出结果
```js
'a' > `b` // => false
```
### 非字符串的比较
两个运算元至少有一个不是字符串需要分为两种情况
1. 都是原始数据类型
2. 存在引用数据类型（对象）
 

#### 非字符串是原始数据类型
将其转换为数值再比较
```js
5 > '4' // => true
// 相当于 5 > Number('4')

true > false // => true
// 相当于 Number(true) > Number(false) 即 1 > 0

null > undefined // => fales
// Number(null)为0， Number(undefined)为NAN
undefined <= null // => false

// Symbol 不能参与非相等比较，Symbol类型不能转换为数值
// BigInt 和数值宽松相等，但不严格相等
10n == 10  // true 10n就代表10的整数， 
10n === 10 // false
 BigInt(10) === 10n // true
```
值得注意的是NaN和任何类型的值比较都是false

:::tip
最新的ECMAScript标准定义了8种数据类型，其中七种原始数据类型，一种引用数据类型Object
:::

#### 存在引用数据类型
需要将其转换为原始数据类型，算法是先调用valueOf取值,如果得到的还是引用数据类型，再调用toString(),之后就是非字符串的原始数据类型比较
```js
[2,3] > 3 // false
// 相当于[2,3].valueOf().toString > 3 => "2,3" > 3 => NaN > 3 => false

let a = {}
a > 4 // false
// 相当于 a.valueOf().toStirng() > 4 => "[object object]" > 4 => NaN > 4 => false
a.valueOf = () => 5
a > 4 // true
```
## 相等运算

javascript 中有三种相等运算
1. `==` 宽松相等
2. `===` 严格相等
3. `Object.is()`ES6中引入，为了解决`===`对于 `NaN===NaN`为false的疑惑，以及`+0===-0`为true在元编程的不便

> 对于`==`来说，当两个运算元的类型相同时，相当于调用`===`来比较，当两个元算元不同时，将他们转换为相同类型再调用`===`来比较， 这就是隐式类型转换

### 严格相等（`===`）
严格相等很简单，就是类型相等，而且值相等或者内存地址相同（对于引用类型来说）
:::warning
需要注意的是 `NaN === NaN` => false 和 `+0 === -0` => true
:::

### 宽松相等（`==`）
当两个运算元类型相同时，就直接调用`===`来比较

类型不同时，需要通过隐式类型转换为相同类型再使用`===`比较。下面介绍隐式类型转换的算法

**隐式类型转换大体可以分为三类**
1. 原始数据类型（除了null和undefined）
2. null和undefined
3. 一个引用数据类型和一个原始数据类型

**Symbol 类型和任何值都不相等**
```js
Symbol('1') == '1' // false
Symbol('1') == Symbol('1') // false
```
由于本人对Symbol类型不太了解，拿来进行比较不知道是否恰当，如有不合理或错误的地方，请大佬指出

#### 原始数据类型（除了null和undefined）
这种情况将两个运算元都转换为数值（Symbol类型除外），然后使用`===`比较
```js
10 == '10' // true 
// 相当于 10 == Number('10')
false == 0 // true
// 相当于 Number(false) == 0
10n == '10' // true
// 相当于 Number(10n) == Number('10') => true
```

#### null 和 undefined
null和undefined互相宽松相等且与其它所有类型的值都互相不宽松相等,但是存在一种特殊情况，在浏览器中，某些对象效仿undefined的角色，与null或undefeated宽松相等
```js
null == undefined // true
null == 0 // false
undefined == 0 // false
null == {} // false
// document.all 在浏览器中充当undefined的角色
null == document.all // true
undefined == document.all // true
```

#### 一个引用数据类型和一个原始数据类型
算法：
1. 如果原始数据类型是Boolean，将其转换为Number类型
2. 引用数据类型的运算元调用valueOf方法，此时如果得到原始数据类型，则跳到步骤四(跳不跳无所谓)
3. 调用toString方法，得到结果A
4. 此时如果不是相同类型,则使用原始数据类型对应的包装器对象对A进行包装，得到相同类型
5. 对两个运算元使用`===`进行比较

```js
false == [] // true
// 相当于 Number(false) == [] => 0 == [] => 0 == Number([].valueOf().toString()) => true

let x = []
x.valueOf = () => '10'
x == 10 // true

let y = []
y.valueOf = () => 'abc'
'abc' == y // true

'0' == [] // false 
```
:::tip
当然，以上所说的原始数据类型不包括 null undefeated 和 Symbol
:::

## 不等运算
`!=` 和 `!==` 这两个运算符的算法就是`==`或`===`的否定

:::warning
相等运算和不等运算满足交换律
但需要注意的是 `{}`作为第一个运算元时会报错`Uncaught SyntaxError: Unexpected token '=='`
:::
例如
```js
[] == {} // false 相同类型，相当于 [] === {} => false
{} == [] // 报错
```

最后，文中所说的“直接使用`===`来比较”并不准确，应该是使用 `===` 的算法更为恰当。本人能力有限，如有错误或不合适的地方，请大佬指正

