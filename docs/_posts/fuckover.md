---
title: vuepress踩坑
tags: ['vuepress']
category: ['vuepress']
author: dva
date: 2020-02-25
---

分享一下vuepress使用过程中的踩坑经历
<!-- more -->
## 编译过程中报错

 ### 1. `window is not defined`
首先需要知道，vuepress是通过vue SSR编译生成的html文件，我们的组件，数据以及引用的第三方包都是在服务器端渲染的。遇到这个问题首先需要检查自己是否在mounted钩子之前是否操作了dom，然后是引入的第三方包。例如：在我的主题中使用了pubsub进行组件之间传值，pubsub会向window挂载一个全局对象，所以应该在mounted钩子中动态引入
```js

mounted() {
    import ('pubsub-js').then(module => {
        const PubSub = new module()
        ...
    })
}
```
:::warning
`import`动态加载包时只能在最外层
:::
但是可能会遇到一个包依赖另一个包，即需要先引入第一个包才能引入第二个包，此时内层需要使用require来引入
```js
mounted() {
    import ('xx').then(module => {

        const aa = require('xxx')
        ....
    })
}

```
### 2. `Content placeholder not found in template.`
这个应该是vuepress的一个bug，具体原因是当依赖的主题修改了templates中的dev.html和ssr.html时，编译会报这个错误。具体可以查看这个[issue](https://github.com/vuejs/vuepress/issues/1905)

## 代码风格坑
**这个是真的坑**
### 1. 刷新抖动
在我刚入门时，记得应该是看表严肃大佬的视频曾经提到过，写css样式表的时候，定位元素，`display:none`应该写在一个样式规则的最前面，我平时写代码总是忘掉这件事，但是这次让我吃亏吃大了
原因是把代码部署到线上时，刷新页面出现抖动，找了很久的原因，竟然发现是一个fixed定位的隐藏的元素在页面刚渲染时没有固定定位，我开始还以为是css代码解析的太慢了:joy: :joy:。当时的代码
```css
.mask{
    width: 100%;
    height: 100vh;
    display: none;
    position: fixed;
    left: 0;
    top: 0;
    right: 0;
    bottom: 0;
    ...
}
```
就是上面的代码，导致刷新页面出现抖动，这波我的:fried_egg:...。修改为下面的代码就”神奇”的解决了这个问题
```css
.mask{
    position: fixed;
    display: none;
    left: 0;
    top: 0;
    right: 0;
    bottom: 0;
    width: 100%;
    height: 100vh;
    ...
}
```
### 2.布局错乱
在刷新过程中出现的第二个问题，一个隐藏的列表会展示出来一瞬间，导致布局错乱，这个可能是css解析太慢的原因吧，因为vuepress编译之后只产生一个css文件，观察这个css文件发现网页中媒体查询部分在最前面，所以将列表的样式写在媒体查询里，解决了这个问题

持续更新...