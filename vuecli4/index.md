# Vue CLI4配置


# Vue简介

Vue 是一套用于构建用户界面的渐进式框架。Vue对于初学者来说容易上手，官方教程文档也很全面且清楚，是快速建立项目不错的选择。

在学习完Vue的基础后，可以通过使用Vue CLI快速搭建一个属于自己的项目！

# CL4配置

`Vue `提供了一个`CLI`，Vue CLI 是一个基于 Vue.js 进行快速开发的完整系统，为单页面应用 (SPA) 快速搭建繁杂的脚手架，CLI4的配置流程大致总结了流程如下：

## 安装node.js

进入[node.js官网](https://nodejs.org/en/)根据系统安装`node.js`。安装完node.js之后，npm也会自动安装。查询是否安装成功的命令：

```
node -v
npm -v
```

## npm换源

 使用`npm`安装一般比较慢，可以安装[cnpm淘宝镜像](https://developer.aliyun.com/mirror/NPM?from=tnpm)来替换`npm`

```
cnpm -v
```

## 安装Vue CLI4

进入终端，全局安装脚手架工具`vue-cli`，命令如下：

```
npm install --global @vue-cli
# OR
yarn global add @vue/cli
```

若你已经安装过久版本，输入如下命令卸载Vue CLI：

```
npm uninstall vue-cli -g
```

 查询是否安装成功及查询版本的命令：

```
Vue -V
# OR
vue --version
```

## 开始创建一个项目

在终端输入vue create加项目名称创建一个新的项目：

```
vue create project-name
```

选择预置，选择`Manually select features`自定义配置：

至少选择`Babel` `Router` `Vuex` `Linter`，其他根据需求自主选择：

选择语法检查器，可以默认，也可以选ESlint(语法检查非常严格)

选择`In package.json` ，使用`package.json` 项目结构比较简单：

进入项目根目录终端，运行项目：`npm run serve`，我们发现项目已经运行在本机服务器8080端口

## 安装axios

在项目中可以选择使用原生`ajax`发起请求，也可以使用`axios`，终端进入项目，安装axios：

```
npm --save install axios
# OR
npm install --save axios vue-axios
```

将下面代码加入入口main.js文件即可使用：

```
import Vue from 'vue'
import axios from 'axios'
import VueAxios from 'vue-axios'

Vue.use(VueAxios, axios)
```

# 项目目总览

```
├── src
│   ├── App.vue
│   ├── assets
│   │   └── logo.png
│   ├── components  # 组件
│   │   └──Helloworld.vue
│   ├── views
│   │   ├── About.vue
│   │   └── Home.vue
│   ├── main.js
│   ├── router  # 路由配置文件
│   │   └── router.js  
│   └── store
│       └── store.js  
├── README.md
├── babel.config.js
├── package-lock.json
├── package.json
└── babel.config.js
```

# 目录简介

## public

public用于存放静态文件如index.html和favicon.ico
public/index.html是项目生成的入口文件，webpack打包的js,css也会自动注入到该页面中。

## src

src是存放各种vue文件和静态资源的地方

1. src/assets用于存放各种静态文件，如图片
2. src/compnents：用于存放公共组件
3. src/views：用于存放写好的各种页面，如login等
4. src/App.vue：项目主体vue模块，可以引入其他模块。它是项目的主组件，所有页面都是在app.vue下渲染的
5. src/main.js：项目的入口文件，主要作用是初始化vue实例，同时可以在此文件中引用某些组件库或者全局挂在一些变量
6. src/router.js：路由文件，简单来说就是各个页面的地址路径，同时可以直接在里边编写路由守卫
7. src/store.js：主要用于保存项目的状态

## package.json

package.json文件包括：项目的基本信息、项目开发所需要模块、版本、项目名称等




























