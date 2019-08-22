---
title: Vue 集成 Mockjs
date: 2019-04-12 13:18:12
tags: [vue]
category: 前端

---

# 0 安装

```
npm install mockjs
```

# 1 创建 Mock

创建文件夹 `src/mock`, `src/mock/json`

创建 json 文件 `src/mock/json/user.json`

```json
{
  "name": "Json",
  "age": 13
}
```

创建文件 `mock/mock.js`

```js
import Mock from 'mockjs'


// 使用正则表达式匹配代参数的 get 请求  /user?xxxxxx
const user = require('./json/user.json')
Mock.mock(/\/user(|\?\S*)$/, 'get', user)
//Mock.mock('/user', user)
```

在 `main.js` 中引入 Mock

```js
import Vue from 'vue'
import App from './App.vue'

// 引入 Mock
process.env.NODE_ENV === 'development' && require('./mock/mock')

Vue.config.productionTip = false

new Vue({
  render: h => h(App),
}).$mount('#app')
```

**上面设置完成后会自动拦截 mock.js 中设置的接口请求**
