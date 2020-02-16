---
title: vuepres-theme-dva文档
date: 2020-02-16
sticky: true !important
author: dva
category: 'vuepress-theme-dva文档'
---
:::tip 前言
我终于把文档肝出来了
:::
<!-- more -->

## 快速开始
首选需要阅读vuepress官网的[快速上手](https://vuepress.vuejs.org/zh/guide/getting-started.html#%E5%BF%AB%E9%80%9F%E4%B8%8A%E6%89%8B)(建议本地安装，全局安装可能会出现未知的问题),创建如下的目录结构：
```
.
├── docs
│   ├── .vuepress 
│   │   └── config.js (vuepress配置文件)
│   │ 
│   ├── _posts(存放文章的文件夹)
│ 
└── package.json
```
:::tip
主题级别的约定：必须在docs下新建`_posts`文件夹，用以存放博客中的文章。同时不建议在`_posts`以及其子文件夹下建立`README.md`或`index.md`，因为vuepress会将其解析为`/_posts/`，而主题会将其作为普通文章来处理
:::
### 安装主题
```shell
yarn add vuepress-theme--dva # 或者 npm install vuepress-theme--dva
```
### 使用
```js
//  docs/.vuepress/config.js

module.exports = {
    theme: 'vuepress-theme-dva',
    ...etc
}
```
### 本地运行
:::warning
在开始之前，确保在`_posts`至少存在一个.md文件
:::

```shell
# 确保在package.json添加了该script

npm run docs:dev 

```
接下来我们需要对主题进行一些配置，来让我们觉得它完美一点

## 基本配置

### 1. vuepress 的基本配置
```js
// .vuepress/config.js
module.exports = {
    base: '/',
    title: 'DVA',
    description: 'a demo',
}
```
更过基本配置参照[vuepress](https://vuepress.vuejs.org/zh/config/#%E5%9F%BA%E6%9C%AC%E9%85%8D%E7%BD%AE)

:::tip
主题的首页右边第一个card，依赖vuepress的基本配置中的title和description，默认是你所看到的
:::

### 2. 主题基本配置
实例如下：
```js
// .vuepress/config.js
module.exports = {
    ...base,
    themeConfig: {
        avatar: 'example.com',
        nav: [
              {text: '主页', link: '/', icon:"iconfont icon-ziyuan"},
              {
                text: '分类',
                items: [
                  {text: '前端', link: '/categories/web/', icon: 'iconfont icon-_qianduankaifa'},
                  {text: 'Node', link: '/categories/node/', icon: 'iconfont icon-node'}
                ],
                icon: 'iconfont icon-fenlei'
              },

    ],
    socialLinks:[
      {url: 'https://github.com/lhj233', icon: 'iconfont icon-github'},
      {url: 'https://space.bilibili.com/427457286', icon: 'iconfont icon-bilibili'}
    ],
    footer: [
      {text: '2018-2020 By Dva',icon:'iconfont icon-copyright'}
    ],
    lastUpdated: 'last update' ,
    // 上次更新时间，依赖git提交的时间具体可看官方插件
    copyright: true, 
    // 是否在文章末尾开启版权信息
    copyrightText: '转载请注明出处'
    //版权信息,默认'转载请注明出处'
    }
}
```
参数说明：

参数 | 说明 | 默认| 是否必须|数据类型
---|---|---|---|---
avatar | 网站头像 |My logo| false|String
nav|导航按钮| ——| false|Array
socialLinks|社交按钮|——|false|Array

其中nav数组中的每一个配置项包括：

参数 | 说明 | 是否必须|数据类型
---|---|---|---
text| 导航文字|true|String
link| 导航链接|true|String
items|导航下拉列表| false|Array
icon| 图标类名|false|String
target|a链接行为(默认'_self')|false|String

socialLinks的每一个配置项包括：

参数 | 说明 | 是否必须|数据类型
---|---|---|---
title| a链接标题|false|String
url| 社交链接|true|String
icon| 图标类名|false|String
target|a链接行为(默认'_target')|false|String

其中footer数组中的每一个配置项包括：

参数 | 说明 | 是否必须|数据类型
---|---|---|---
text| 导航文字|true|String
link| 导航链接|true|String
icon| 图标类名|false|String
target|a链接行为(默认'_self')|false|String

:::tip
文末我会写明本主题内置的图标以及类名
:::


## 进阶配置
### 1. 文章内的fontmatter配置
参数|说明|是否必须|数据类型
---|---|---|---
date|文章日期|true|Date
author|作者|false|String
title|文章标题|true|String
sticky|置顶|false|Boolean
copyright|版权(文章内部的优先级高于themeConfig)|false|Boolean
copyrightText|版权信息|false |String
:::warning
1. 请不要在md内写一级标题，这会导致出现两个标题。因为主题中文章的标题是由vuepress解析得到的，这样有更好的配置性。之前是允许有h1标题，在mounted钩子中hack掉h1，之后部署到线上发现，这样很不友好
2. 主题中多处依赖文章中的`date`，所以请务必添加，但是日期格式需要注意，因为只有前两位用两位数字写的日期(例如)2020-02-16才会被转换，所以其他以一位数字写的日期（例如）2020-1-1将被视为字符串。这会导致不能正常工作
:::

:::tip
1. 首页的文章摘录是在md中使用`<!-- more -->`生成的，所以为了美观，请务必使用它。

2. 允许指定多个置顶，即在多个文章中使用sticky，当需要一个在置顶中的置顶，使用`sitcky: true !important`。不要问我为什么没有置顶中的置顶中的置顶
:::


### 2. 添加时间轴页面
在docs下新建一个md文件，例如`timeline.md`,在该页面中使用`fontmatter`指定布局页面为主题内置的`TimeLine`即可开启时间轴页面
```YAML
---
layout: TimeLine
---
```
:::tip
时间轴是根据每篇文章的`date`这个`frontmatter`生成的，所以建议`_posts`下每篇文章都有`date`
:::
### 3. 添加友链页面
同样是在docs下新建一个md文件，例如`friendlinks.md`,在该页面中使用`fontmatter`指定布局页面为主题内置的`FriendLinks`即可开启时间轴页面
```YAML
---
layout: FrinedLinks
content: true
---
```
:::tip
content指定为true，会渲染该md页面的内容,不指定或为false，会不显示md的内容
:::

**导入友链**

可以在themeConfig对象中直接添加，为了方便管理，推荐再创建一个文件
```js
// friendlinks.js
module.exports = [
  {
    name: 'test', 
    description: '哈哈哈哈',
    url: '#',
    avatar: 'https://image.start-here.cn/avatar.jpg',
  },
  {
    name: 'test',
    description: '哈哈哈哈',
    url: '#',
    avatar: 'https://image.start-here.cn/avatar.jpg',
  },
}

// .vuepress/config.js
module.exports = {
    themeConfig: {
        friendlinks: require('friendlink.js')
    }
}

```
每条友链配置:
参数|说明|是否必须
---|---|---
name|网站名称|true
description| 网站描述|true
url|网站链接|true
avatar|网站头像|true

### 4. 添加关于页面
同样是创建一个md文件，在该页面中使用`fontmatter`指定布局页面为主题内置的`About`即可开启关于页面
```YAML
---
layout: About
---
```
**该页面会将md文件内的内容展示**
:::warning
上面所提到的三个布局，建议不要超过一个页面使用，可能会出现不可预料的问题
:::
### 5. 添加文章分类和标签
这就像和喝水简单

在每篇文章中使用`frontmatter`使用`category`或`categories`来声明分类，使用`tag`,`tags`来声明标签

好了结束了

:::tip
由于主题依赖[@vuepress/plugin-blog](https://vuepress-plugin-blog.ulivz.com/)插件，该插件不能实现层级分类，所以最终我还是实现了。。。，但是需要在`frontmatter`中指明分类层级
:::

例如：想要一个 `web/css`分类，只需要在`category`中声明其父类。
```YAML
// scss.md
---
category: ['web', 'web/css']
---
```
**但是不能超过三层**（我还是个孩子 逃:dash:），超过三层的分类会被截掉，例如: `web/vue/vuepress/配置`，最终输出的是 `web/vue/配置`

:::tip
如果你看了[@vuepress/plugin-blog](https://vuepress-plugin-blog.ulivz.com/)插件，并且想对文章增加新的解析，可以在themeConfig中配置
:::

```js
themeConfig: {
    views: {
        frontmatter: {},
        directory: {}
        // 具体配置项参考插件官网
    }
}
```
### 6. 添加valine评论系统
建议先阅读valine官方[文档](https://valine.js.org/),获得appId和appKey之后，在配置文件中添加如下配置
```js
// .vuepress/config.js
module.exports = {
    themeConfig: {
        valineConfig: {
          appId: 'your id',
          appKey: 'your key',
          placeholder: '干就完了',
          notify: true,
          visitor: true, // 开启文章阅读量统计，默认不开启
          avatar: 'retro'
        }
    }
}
```
## 使用
**markdown内的container**,具体使用可以参考[自定义容器](https://vuepress.vuejs.org/zh/guide/markdown.html#%E8%87%AA%E5%AE%9A%E4%B9%89%E5%AE%B9%E5%99%A8)

```md
::: tip 提示
随便写点啥
:::

::: warning Note
随便写点啥
:::

::: danger 
随便写点啥
:::

::: details 
随便写点啥
:::

::: cutline 分隔
:::
```
如下所示分割线

:::cutline 分割线
:::

更多markdown的拓展见[vuepress官方文档](https://vuepress.vuejs.org/zh/guide/markdown.html#markdown-%E6%8B%93%E5%B1%95)

### 主题色

目前主题不支持配置主题色，以及时间轴和友链的底色，后面我会添加的

### 已知问题
1. 有朋友反映在iPhone上searchbox不能正常使用，这个目前没找到问题所在，主题使用的是vuepress官方的插件。
2. 在我的项目下，pwa插件不能正常弹出，虽然主题没有直接内置pwa模块 hha 


## 展望
1. 看看能不能做图片懒加载
2. 主题色以及时间轴，友链可配置
:::cutline 更新
:::
主题原本通过在templates中添加图标库的链接，但是这会导致编译时出现`Content placeholder not found in template.`,具体可见这个[issue](https://github.com/vuejs/vuepress/issues/1905),
所以如果想用主题内置的icon，需要在config.js中引入
```js
// .vuepress/config.js
module.exports = {
  head: [
    ['link', {rel: 'stylesheet', href: '//at.alicdn.com/t/font_1625681_l6soj9fb0gg.css'}]
  ]
}
```
## 附
### 主题支持的icon

![image](https://image.start-here.cn/icon.png)
:::warning
配置中使用这些icon时都要加上`iconfont`这个类名。
如果需要增加图标，只需要去[iconfont](https://www.iconfont.cn/home/index)添加图标并引入你的链接。
如果需要覆盖templates下的dev.html和ssr.html,请加上这个主题icon链接 //at.alicdn.com/t/font_1625681_l6soj9fb0gg.css， 当然你也可以替换全部
:::

