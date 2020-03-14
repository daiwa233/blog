---
title: 腾讯前端笔试总结
date: 2020-03-14
tag: ['面试总结']
author: dva
category: ['面试总结']
---
题目如下：
1. 数组扁平化实现
2. 对数字添加千位分隔符
3. 解析url的参数
4. 一个闭包函数的实现
5. 快排实现
6. 验证邮件和验证大陆电话号码的函数
7. 在浏览器中显示当前时间：格式：2020年03月08日 20:20:30

## 数组扁平化
当数组中嵌套多层数组，将该数组转换为单层数组的过程就是数组的扁平化。例如：[1,[2,3]] => [1,2,3]

我的实现：
```js
function flatten(arr) {
    return arr.reduce((prev, cur) => {
        const step = Array.isArray(cur) ? flatten(cur) : cur
        return prev.concat(step)
    }, [])
}
```
具体思路是： 遍历数组中的每一项，如果该项是数组，递归调用`flatten`。保存其返回值，最后利用`concat`连接。`concat`不改变原数组，且可以接受非数组元素。

对reduce不熟悉的可以看看我的这篇[文章](https://start-here.cn/_posts/web/ArrayIterator.html#_4-array-reduce-array-reduceright)

其它实现：
1. 如果是只是嵌套一层，可以使用`...`运算符直接扁平化

```js
let arr = [1,[2,5]]
// 该方法只能处理像arr这样的一层嵌套数组
function flatten(arr) {
    let copy = []
    for(const val of arr) {
        if (Array.isArray(val)) {
            copy.push(...val)
        } else {
            copy.push(val)
        }
        
    }
    return copy
}


```
2. 普通的for循环递归，其实思路和`reduce`实现的一样

```js
function flatten(arr) {
    let result = []
    for (const val of arr) {
        const step = Array.isArray(val) ? flatten(val) : val
        result = result.concat(step)
    }
    return result
}
```

## 数字添加千位分隔符
对数字添加千位分隔符：例如 1234 => 1,234

我的答案：
```js
// 错误答案
function addComma(num) {
  let str = num.toString()
  let arr = str.split('').reverse()
  
  for(let i=0; i<arr.length; i++){

    if((i+1) % 3 === 0) {
      
      arr.splice(i+1, 0, ',')
    }

  }
  return arr.reverse().join('')
}
```
当时测试 1234 => 可以得出1,234，但是当使用12345678测试就不能得出正确答案

原因有两点：
1. 忽视了`splice`的副作用
2. 对`splice`的第三个以及之后的参数插入数组中的位置不太清楚。第三个及之后的参数会插入第一个参数的位置之前。
例如：

```js
let arr = [1,2,5]
arr.splice(2, 0, 3,4)
arr => [1,2,3,4,5]
```

如果仍按照上面的实现思路，需要在插入第一个 ',' 后，每四位数之后添加一个','。这样实现太麻烦了。

**正确实现：**
```js
// 真就一行代码实现
function addComma(num) {
    return num.toLocaleString('en-us')
    // 或者更简洁的 return num.toLocaleString()
} // 我已经好几次把toLocaleString()写成toLocalString()了

```
看了一些文章，觉得当时考虑的还是太少，没有考虑小数点，正负数。
```js
function addComma(num) {
  return num && num.toString()
    .replace(/\d+/, function(s){
         return s.replace(/(\d)(?=(\d{3})+$)/g, '$1,')
    })
}
// 前瞻：
exp1(?=exp2) exp1后边是exp2就匹配
// 后顾：
(?<=exp2)exp1 exp1前边是exp2就匹配
// 负前瞻：
exp1(?!exp2) exp1后边不是exp2就匹配
// 负后顾：
(?<!exp2)exp1 exp1前边不是exp2就匹配
```
正则用着就是:cow:。
## 解析url的参数
题目描述：将形如`https://cloud.tencent.com?a=%E4%BD%A0%E5%A5%BD&b=2&c=3#hash`的url解析为json对象`{a: 你好, b: 2, c: 3}`

我的答案：

```js
function parseUrl(urlStr) {
  if (!step.includes('?')) return {}
  let step = urlStr.split('?')[1]
  if (step.includes('#')) step = step.split('#')[0]

  let reg = /(\w*)=(\w*)&?/g,
      obj = {}
  step.replace(reg, function(a, b, c) {
    
    obj[decodeURI(b)] = decodeURI(c)
  })
  return obj
}
// => { a: '', b: '2', c: '3' }
```
当时真的怕极了，我刚开始写的是先对url解码，可是不会写匹配中文的正则，我就想到先匹配，再解码。。最后发现更拉了，我写的正则根本匹配不到`%`

:::warning
在这吐槽下W3CSchool，这篇[文章](https://www.w3cschool.cn/regexp/nck51pqj.html)说到`\w`元字符可以匹配中文。如果是我的无知，麻烦给我提一下。哈哈哈哈
:::

**正解**

```js
function parseUrl2(urlStr) {

  let reg = /\??(\w*)=([\w\u4e00-\u9fa5]*)[&#]?/g,
      obj = {}
      
  decodeURI(urlStr).replace(reg, function(a, b, c) {
    obj[b] = c
  })
  console.log(obj)
  return obj
}
```
找到了匹配中文的正则，又完善了正则表达式，减少了前期不必要的判断。但是出现其它语言可能还是无法正确解析

## 闭包函数的实现
这个没啥说的。

```js
function closure(firstArg) {
  return function() {
    console.log(firstArg)
  }
}
```
当时想写一个自动curry的函数，想想算了，不zb了，快排都写不出来，那必是凉了

## 快排实现
题目描述：快排算法的实现，然后给了一个测试用例。
面试之前我真的没有看过任何算法相关，哎，虽然在学校课程中也学过，但是我还回去了。
```js
function quickSort(arr, left, right) {
    var len = arr.length,
        partitionIndex,
        left = typeof left != 'number' ? 0 : left,
        right = typeof right != 'number' ? len - 1 : right;

    if (left < right) {
        partitionIndex = partition(arr, left, right);
        quickSort(arr, left, partitionIndex - 1);
        quickSort(arr, partitionIndex + 1, right);
    }
    return arr;
}

function partition(arr, left, right) {
    let pivot = left,
        index = pivot + 1
    for (let i = index; i <= right; i++) {
        if (arr[i] < arr[pivot]) {
            swap(arr, i, index)
            index++ // 保证靠近基准值的元素永远小于基准值
        }
    }
    swap(arr, pivot, index - 1)
    return index - 1;
}

function swap(arr, i, j) {
    var temp = arr[i];
    arr[i] = arr[j];
    arr[j] = temp;
}

```


## 验证邮箱和大陆地区手机号码
题目描述：写两个工具函数，判断是否是邮箱或者手机号，是返回true，否则返回false

这两道题目就不放我的答案了，可以判断是可以判断，但是不严谨。

**正解**
```js
function validateMail(mail){
  let reg = /^[a-z0-9!#$%&'*+\/=?^_`{|}~.-]+@[a-z0-9]([a-z0-9-]*[a-z0-9])?(\.[a-z0-9]([a-z0-9-]*[a-z0-9])?)*$/i
  return reg.test(mail)
}


function validatePhone(phoneNum) {
  let reg = /^1[3|4|5|7|8][0-9]{9}$/
  return reg.tests(phoneNum)
}
```

## 浏览器中显示当前时间
这个没什么说的。就是利用Date对象上的方法。可以利用ES6的`padStart`和`padEnd`来快速补齐两位时间。要注意的是这两个方法都是字符串方法。
```js
let date = new Date(),
    month = (date.getMonth()+1).toString().padStart(2, '0')
// 现在是3月份，month就是03
```

## 总结
题目并不难，可是正则和算法我都不行。多刷题，加油加油