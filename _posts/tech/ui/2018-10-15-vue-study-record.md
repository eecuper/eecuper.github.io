---
layout: post
title: vue 学习理解
date: 2018-10-15 
categories: [工具]
keywords: vue
---

# [Vue官网](https://cn.vuejs.org/)

## mixin (混入)

## axios 

简单理解为类似 jquery 对ajax 封装; 

```
export function login (parameter) {
  //这里axios 进行过包装,详细建 antd-vue utils/request.js
  return axios({
    url: '/auth/login',
    method: 'post',
    data: parameter
  })
}

--调用
loginParams[!state.loginType ? 'email' : 'username'] = values.username
loginParams.password = md5(values.password)
Login(loginParams)
.then((res) => {
    //调用成功执行
    this.$router.push({ name: 'dashboard' })
      // 延迟 1 秒显示欢迎信息
      setTimeout(() => {
        this.$notification.success({
          message: '欢迎',
          description: `${timeFix()}，欢迎回来`
        })
      }, 1000)
})
.catch(err => {    
    //失败或异常执行
    this.requestFailed(err)
})
.finally(() => {
  //总是执行
  state.loginBtn = false
})

```


## Vuex 

可以简单理解为整个APP各个页面或者组件可能涉及使用的公用信息存放的一个全局JSON对象管理工具

### State 信息存储库

### Getter 信息读取

### Mutation 信息修改

### Action 修改操作()

### Module 

