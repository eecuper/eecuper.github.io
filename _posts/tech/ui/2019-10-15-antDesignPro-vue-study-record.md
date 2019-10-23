---
layout: post
title: Ant Design Pro vue 学习使用
category: 
tags: [ant,vue,蚂蚁金服]
keywords: ant,vue,蚂蚁金服
---

## github 项目

[https://github.com/sendya/ant-design-pro-vue](https://github.com/sendya/ant-design-pro-vue)

[文档](pro.loacg.com/docs/getting-started)

### 目录结构

```
├── public
│   └── logo.png             # LOGO
|   └── index.html           # Vue 入口模板
├── src
│   ├── api                  # Api ajax 等
│   ├── assets               # 本地静态资源
│   ├── config               # 项目基础配置，包含路由，全局设置
│   ├── components           # 业务通用组件
│   ├── core                 # 项目引导, 全局配置初始化，依赖包引入等
│   ├── router               # Vue-Router
│   ├── store                # Vuex
│   ├── utils                # 工具库
│   ├── locales              # 国际化资源
│   ├── views                # 业务页面入口和常用模板
│   ├── App.vue              # Vue 模板入口
│   └── main.js              # Vue 入口 JS
│   └── permission.js        # 路由守卫(路由权限控制)
├── tests                    # 测试工具
├── README.md
└── package.json
```

### 调试运行 

npm run serve 
yarn run serve 



## Ant Design Pro 学习使用

### 理解

#### main.js 引入说明

```
//路由规则引入,如果是静态配置主要查看 @config/router.config.js 文件内容配置
import router from './router' 

//这里主要封装了下vue的store管理了 
//app:程序布局,主题,设备类型,多标签等等默认设置
//user:用户信息,角色,token,登入,登出等方法判断
//permission:权限,主要做了用户是否拥有路由,或者角色判断方法.
//初始化vuex.store  state , mutations , actions , getters 
import store from './store/'

//封装的 vueAxios http数据请求工具方法
import { VueAxios } from './utils/request'

// mock 请求接口模拟数据
// import './mock'

//引入bootstrap组件
import bootstrap from './core/bootstrap'

//ant design pro of vue 主要及时基于 antd脚手架基础上来实现的中台管理示例,use.js包含了antd 等基础主要组件的引入
import './core/use'

//这里主要是控制操作菜单后对当前用户是否登录,路由的解析
import './permission' // permission control

//过滤器封装方法
import './utils/filter' // global filter
```

#### App.vue 

```
//引入国际化配置
import zhCN from 'ant-design-vue/lib/locale-provider/zh_CN'
//引入混入规则 ,布局默认设置,设备等等初始化 
import { AppDeviceEnquire } from '@/utils/mixin'
```

## 使用

ant-design-pro-vue 是已经比较完善的企业级脚手架项目,项目使用主要需要更改的地方完成以下三点即可:

- 动态菜单
- 动态角色/菜单权限
- 添加功能页面

### 动态菜单

[测试数据](https://github.com/sendya/ant-design-pro-vue/blob/feature/dynamic-menu/public/dynamic-menu.json) , 后台建表参考该数据结构做适当个性化修改

### 动态角色/菜单权限 

[文档权限管理](https://pro.loacg.com/docs/authority-management)

### 添加功能页面