---
layout: post
title: '尝试简单封装vue组件并实现按需加载'
date: 2019-07-12 11:21
categories: Vue
permalink: /archivers/component
---

基本参照 vant 操作.

## 1. 初始化项目

vue init webpack-simple my-project

## 2. 目录结构
    |--pulblic
    |--src  // 组件开发目录
    |--fl-header  // 每个组件分别一个目录
    |--style // 组件样式目录
    --index.js // 样式文件入口
    --index.js // 组件入口
    --flHeader.vue // 组件
    |--fl-pay // 又一个组件目录 其他组件依照上述结构
    ......
    --.babelrc // babel配置入口
    --App.vue // 虽然没什么但是可以做测试组件的入口
    --main.js // 同上
    --package.json // 关键配置
    --webpack.config.js // cli3 webpack配置
    --README.md // 文档说明

## 3. 组件代码
以fl-header 为例, 组件目录结构严格按照如下

    |--fl-header  // 每个组件分别一个目录
    |--style // 组件样式目录
    --index.js // 样式文件入口
    --index.js // 组件入口
    --flHeader.vue // 组件

每个组件要有独立的文件夹和入口文件, 满足babel-plugin-import插件的要求, 这个按需加载的插件实际是导入需要的组件文件夹,

假如我最后封装了组件模块包叫做 jqing-ui, 首先安装babel-plugin-impor, 在.babelrc中配置

```json
"plugins": [
          ["import", {"libraryName": "jqing-ui", "libraryDirectory": "es", "style": true}]
          ]
```
我只需要要引入其中的 fl-header 组件
在main.js 中这样表面上是这么引入, import {flHeader} from 'jqing-ui'实际上babel-plugin-import 会处理成  import fl-header from jqing-ui/es/fl-header/index.js 这个后面调试的时候再说, 先提一下, 为什么组件目录要这个样式的原因, 因为在plugin中设置了'style':true, 所以组件文件夹中多了style文件夹 ,实际上目前还么用到, 到后面如果组件复杂了, 样式文件可以会写在style里, 反正先搁这不用理会.

### fl-header.vue  
这是一个活动h5页面头部组件 ,比较简单, 其中name属性是必写
```vue
<template>
  <div>
    <header class="site-header">
      <div class="site-header-left">
        <a class="logo" :href="options.link" :style="'background: url('+ options.logo +') no-repeat 0 45%;background-size: 28px auto;'">{{options.title}}</a>
      </div>
      <div class="hide-in-login site-header-right" v-if="!isLogin">
        <a href="javascript:;" class="login" @click="loginTo">登录</a>
      </div>
      <div class="show-in-login site-header-right" v-if="isLogin">
        <img class="userIco" :src="uicon? uicon: options.defaultIcon">
        <span class="userName">{{unickname? unickname:options.defaultUnickname}}</span>
        <a href="javascript:;" @click="logout" class="logout">[注销]</a>
      </div>
    </header>
  </div>
</template>
<script>
export default {
  name: 'flHeader',   // name 必填
  props: { 
    isLogin: { // 登录状态
      type: [Boolean, String, Number],
      default: false
    },
    uicon:{ // 用户头像
      type:String,
      default:''
    },
    unickname:{ // 用户昵称
      type:String,
      default:''
    },
    options: {
      type: Object,
      default: () => {
        return {
          title: '', // 网站名称
          link: '', // 点击logo跳转的网站主页链接
          logo: '', // 网站logo
          defaultIcon: '', // 默认用户头像
          defaultUnickname: '' //默认用户昵称
        };
      }
    }
  },
  methods:{
    loginTo(){
      this.$emit('loginTo')
    },
    logout(){
      this.$emit('logout')
    }
  }
};
</script>
<style lang="scss" scoped>
 // 样式省略
</style>
```

### index.js 
组件入口文件
vue组件的封装主要就是给这个组价加了个install方法
官方说明：
> "用于安装 Vue.js 插件。如果插件是一个对象，必须提供 install 方法。如果插件是一个函数，它会被作为 install 方法。install 方法调用时，会将 Vue 作为参数传入。
> 当 install 方法被同一个插件多次调用，插件将只会被安装一次。"

```js
import flHeader from './flHeader'
flHeader.install = Vue => Vue.component(flHeader.name, flHeader)
export default flHeader
```

这样我们的组件就封装完了
如果需要全局引入或者以防未配置 babel-plugin-import 插件时报错, 可以下 src 目录下新建一个总入口 index.js
配置如下:
如果配置了 babel-plugin-import, 是不会经过这个文件的
```js
import flHeader from './fl-header'
import flPay from './fl-pay'
import flPayBack from './fl-pay-back'

var components = [flHeader ,flPay, flPayBack];

var install = function install(Vue) {
  components.forEach(function (Component) {
    Vue.use(Component);
  });
};

/* 支持使用标签的方式引入 */
if (typeof window !== 'undefined' && window.Vue) {
  install(window.Vue);
}

console.log('看到这段话就知道按需导入失败了..')

export { 
  flHeader,flPay,flPayBack
}

export default {
   install 
}
```

## 4.源码转换
现在需要做的是将 src 目录下文件打包成 es5 和 es6 两种样式代码, 分别对应 lib 和 es 两个文件夹,里面目录结构不会变
    |--src // 源代码文件夹
    |--lib // es5 文件夹
    |--es // es6 文件夹
因为公司大佬封装的脚手架里直接封装这个指令我就直接用了
自己转换的话可以借助 babel-cli
```shell
babel src --out-dir es // 如果 presets没有配置es2015 默认还是转成es6代码,然后配置"presets":["es2015"]
// 再运行一次babel src --out-dir lib 就差不多了, 麻烦了点, 可以自己写gulp配置一下
```

## 5.说明文件
README.md 发布 npm 包后默认显示的说明内容, 不写的太不专业,做个样子也要做全
包名纯属虚构如有雷同纯属巧合
```
## 使用指南

// 安装
npm i -S jqing-ui

// 引入 以flHeader为例
import { flHeader } from '@flamingo/ui'
Vue.use(flHeader)

// 在.vue文件中使用
<fl-header />

// 提示: 需要.babelrc中增加 babel-plugin-import配置 按需导入
"plugins": [["import", {"libraryName": "jqing-ui", "libraryDirectory": "es", "style": true}]


##  flHeader

H5活动页头部状态栏

**API**

| 参数      | 说明                             | 类型                    | 默认值 |
| --------- | -------------------------------- | ----------------------- | :----- |
| isLogin   | 用户登录状态                     | Boolean，String，Number | false  |
| unickname | 用户昵称                         | String                  | -      |
| uicon     | 用户头像                         | String                  | -      |
| options   | 网站相关信息,具体配置见options表 | Object                  | -      |

**options**

| 参数             | 说明                       | 类型   | 默认值 |
| ---------------- | -------------------------- | ------ | ------ |
| title            | 网站名称                   | String | -      |
| logo             | 网站logo                   | String | -      |
| link             | 点击logo跳转的网站主页链接 | String | -      |
| defaultIcon      | 默认用户头像               | String | -      |
| defaultUnickname | 默认用户昵称               | String | -      |

**Methods**

| 事件名  | 说明           |
| ------- | -------------- |
| loginTo | 点击登录时触发 |
| logout  | 点击注销时触发 |

```

## 6.发布配置
package.json
```js
{
  "name": "jqing-ui",
  "description": "activities ui",
  "version": "1.0.1",
  "author": "jqing1968 <jianqing2015@outlook.com>",
  "license": "MIT",
  "private": false,
  "main": "lib/index.js", 
  "module": "es/index.js", // 默认优先引入es文件夹下的入口文件
  "scripts": {
    "dev": "cross-env NODE_ENV=development webpack-dev-server --open --hot",
    "build": "cross-env NODE_ENV=production webpack --progress --hide-modules"
  },
  "dependencies": {
    "vue": "^2.5.11"
  },
  "browserslist": [
    "> 1%",
    "last 2 versions",
    "not ie <= 8"
  ],
  "files": [  // 这个是指定发布的文件, 不指定会默认将整个项目发布到npm
    "es",
    "lib",
    "README.md",
    "package.json"
  ],
  "devDependencies": {
    ......
  }
}
```
webpack.config.js 这里配置的主要是这几个

library: 'jqing-ui', // 指定的就是你使用 require 时的模块名
libraryTarget: 'umd', // libraryTarget 会生成不同 umd 的代码,可以只是 commonjs 标准的，也可以是指 amd 标准的，也可以只是通过 script 标签引入的
umdNamedDefine: true // 会对 UMD 的构建过程中的 AMD 模块进行命名。否则就使用匿名的 define

然后就可以发布了
npm login
npm publish 

## 7.本地测试
一般是自己完全检测没问题了再发布

一种方法是边撸边调试, 我是将 App.vue 和 main.js 已处理 src, 以防干扰, 然后直接引入 src 里正在编辑组件入口 npm run dev 实时编写和调试组件

二是转换 src 文件夹的源码后, 在当前路径下 npm link 将这个项目作为 npm 模块链接到本地全局,然后在另一个项目中引用该模块 npm link <你的 npm 模块名称> ,这样在这个项目的 package.json 的 dependencies 中就能看到该依赖了, 后面就是常规操作

本地调试无误后再发布
