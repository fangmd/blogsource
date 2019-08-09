---
title: Vue 实战课笔记
date: 2019-03-19 13:18:12
tags: [vue]
category: 前端

---

课程地址:[https://time.geekbang.org/course/detail/163-86419](https://time.geekbang.org/course/detail/163-86419)

# 04 开发环境搭建

## Vue.component 创建组件

```js
Vue.component('todo-item', {
    props: ['todo'],
    template: '<li class="text"> {{ todo.text }} </li> '
})
```

缺点：

1. 全局定义，需要每个 component 中的命名不能重复
2. 字符串模版，缺乏语法高亮，html 多行格式丑陋
3. 不支持 CSS
4. 没有构建步骤：只能使用 HTML, ES5, 不能使用预处理 Babel

# 05 06 07 Vue 组件核心概念

Vue 组件 = Vue 实例 = new Vue(options)

三大核心概念：属性，事件, 插槽


## 属性

1. 自定义属性(props): 组件声明的属性
2. 原生属性(attrs): 没有声明的属性，默认自动挂载到组件根元素上
3. 特殊属性(class style ref key): 挂载到组件根元素

```js
export default {
  name: "PropsDemo",
  // 取消原生属性自动挂载
  // inheritAttrs: false, 
  props: {
    name: String,
    type: {
      validator: function(value) {
        // 保证值包含在数组中
        return ["success", "warning", "danger"].includes(value);
      }
    },
    list: {
      type: Array,
      default: () => []
    },
    isVisible: {
      type: Boolean,
      // 设置默认值
      defalut: false
    },
    onChange: {
      type: Function,
      default: () => {}
    }
  }
};
```

props: 里面的数据是单向数据流，不能修改。(可以通过父组件传入的回调函数实现修改)

## 事件

普通事件: @click, @input, @change, @xxx; 通过 this.$emit('xxx', ...) 触发

修饰符事件: @input.trim @click.stop 一般同于元素 html 元素

### 事件冒泡
比如下面代码中点击 `重置成功` 触发 `handleClick`, `handleDivClick` 两个函数。

```html
    <div @click="handleDivClick">
      <button @click="handleClick">重置成功</button>
      <button @click.stop="handleClick">重置失败</button>
    </div>
```

> @ 代替 v-on, : 代替 v-bind

阻止冒泡的两种方法：

1. `@click.stop`
2. 在函数内部调用 `e.stopPropagation()`

## 插槽

父组件：

```
    <SlotDemo>
      <p>Defalut Slot</p>
      <template v-slot:title>
        <div>
          <p>Title Slot1</p>
          <p>Title Slot2</p>
        </div>
      </template>
      <template v-slot:item="props">
        <p>item slot-scope {{ props }}</p>
      </template>
    </SlotDemo>
```

子组件

```
<template>
  <div>
    <slot name="title"/>
    <slot name="item" v-bind="{ value: 'vue' }" />
  </div>
</template>
```

# 08 双向绑定和单向数据流

双向绑定：model 修改触发 view 改变，view 修改触发 model 修改

vue 中使用双向绑定：`v-model`, 在表单元素中使用。

## 自定义组件中使用 `v-model`

使用 `v-model` 的时候默认会使用 prop: `value` 和 事件 `input`。

通常都会去自定义这个值:

```js
model:{
    prop: 'myModelProp',
    event: 'myEvent'
},
props:{
    myModelProp: Object
}
```


自定义组件中：

```js
<template>
  <div>
    <input type="number" placeholder="手机号" :value="myModelProp.phone" @input="handlePhoneChange">
  </div>
</template>

<script>
export default {
  name: "PersonalInfo",
  model: {
    prop: "myModelProp",
    event: "myEvent"
  },
  props: {
    myModelProp: Object,
  },
  methods: {
    handlePhoneChange(e) {
      this.$emit("myEvent", {
        ...this.myModelProp,
        phone: e.target.value
      });
    },
  }
};
</script>
```

父控件:

```js
<PersonalInfo v-model="phoneInfo"></PersonalInfo>

data(){
    return{
        phoneInfo:{
            phone: 123123
        }
    }
}
```

## sync 实现自定义组件的数据双向绑定

>写法简单

自定义控件：

```js
<template>
  <div>
    <input type="number" placeholder="邮编" :value="zipCode">
  </div>
</template>

<script>
export default {
  name: "PersonalInfo",
  props: {
    zipCode: Number
  }
};
</script>
```

父控件：

```js
<PersonalInfo :zipCode.sync="zipCode"></PersonalInfo>
```

# 09 虚拟 DOM 及 key 属性

JQuery 简化操作 DOM 的方法。

Vue: 没有直接操作 DOM，而是操作虚拟 DOM，由虚拟 DOM 生成实际 DOM。

Virtual DOM: state + template

Virtual DOM Diff: 虚拟 DOM 修改后生成差异数据更新实际DOM, 使用了同层节点算法，配合 key 实现复用

>不要使用 index 作为 key

# 10 如何触发组件更新

数据来源：父元素的属性，组件自身 data, 来自状态管理器 vuex, Vue.observable

触发更新：组件调用 `update` 生命周期函数

>data 中实际使用到的数据才会触发更新

# 11 合理应用计算属性和侦听器

computed 能做的，watch 都能做，但是 watch 能做的 computed 不一定能做。

1. 能用 computed 就用 computed

## 计算属性

计算属性：computed

特征：

1. 减少模版中计算逻辑
2. 数据缓存
3. 依赖固定的数据类型（响应式数据）

## 侦听器

watch: 监听响应式数据的改变

简单形式：

```
// a: 1

watch:{
  a: function(val, oldVal){
    //...
  }
}
```

deep:

```
// data
e:{
  f:{
    g: 4
  }
}

e:{
  handler: function(val, oldVal){
    //....
    //this.e.f.g
  },
  deep: true;
}
```

>深度监听，耗费性能，this.e = { ...this.e };

# 13 生命周期的应用场景和函数式组件


## 生命周期

创建阶段:

```
beforeCreate
created
beforeMount
render: 生成虚拟DOM, 挂载DOM
mounted: ** 异步请求，操作 DOM， 定时器
```

更新阶段:(会重复调用, $forceUpdate 强制刷新)

```
beforeUpdate: 移除已经添加的事件监听器 (不可以更改依赖数据，会导致死循环)
render
updated: 操作 DOM 添加事件监听器, (不可以更改依赖数据，会导致死循环)
```

销毁阶段：

```
beforeDestory: ** 移除监听
destoryed
```

## 函数式组件

无状态，无生命周期

函数式组件实现临时变量:

TempVar.js

```jsÏ
export defaul{
  functional: true,
  render: (h, ctx) => {
    return ctx.scopedSlots.default && ctx.scopedSlots.default(ctx.props || {});
  }
}
```

# 14 指令的本质是什么

内置指令：

```
v-text: 替换元素内的文本内容
v-html
v-show
v-if,v-else,v-else-if
v-for
v-on: 使用 `@` 简写
v-bind: 使用 `:` 简写
v-model
v-slot
v-pre
v-cloak
v-once: 里面的元素只渲染一次 `{{number}}` 后面 number 修改也不会触发渲染
```

自定义指令

指令的生命周期函数:

```
bind
inserted
update
componentUpdated
unbind
```

# 15 常用高级特性 provide/inject

解决组件间通讯问题

>写封装库的时候会用到

# 16 如何优雅地获取跨层级组件实例

>理想状态下不需要获取实例，是需要关注数据

## 使用 `this.$refs` 获取 DOM node.

```
例子：

<!-- this.$refs.pnode 获取 DOM node -->
<p ref="pnode">hello</p>

<!-- this.$refs.child 是 component 实例 -->
<child-component ref="child">hello</child-component>

```

## callback ref

对于多层级可以使用 callback ref

主动通知(setXxxRef)，主动获取(getXxxRef)

实现 callback ref:

```
... 结合 provide inject
```

# 17 template 和 JSX 的对比以及它们的本质



# 20 为什么需要 Vuex

Vuex: 一种状态管理模式

```
view 
action
state
```

# 21 22 Vuex 核心概念和底层原理


```
state: 提供一个响应数据
getter: 借助vue的计算属性computed实现缓存, state 中的计算属性， 通过属性访问 store.getters.xxxx 通过方法访问 store.getters.xxx(xx), 不会缓存结果
mutation: 更改state方法; 修改 store 中的状态的唯一方法是提交 mutation。store.commit('xxx')
action: 触发 mutation 方法; 可以包含异步操作，类似 mutation. `store.dispatch(xxx)`
module: Vue.set 动态添加到 state 到响应式数据中
```

```
import store from '@/store';

触发 mutations: store.commit('setOpenId', res.data.data.content);

vue 获取 state: 

  computed: {
    platCode: function () {
      return this.$store.state.platCode;
    }
  },

```



# 23 Vuex 最佳实践

基本使用：

```
import store from "@/store";
// actions
store.dispatch("getOpenId", { code, state });
// mutations
store.commit('setOpenId', res.data.data.content);

```

1. Mutation 使用常量
2. Module 启用命名空间, createNamespacedHelpers
3. 使用 mapXXX 函数

map:

```js
  // state 1
  // computed: {
  //   count: function() {
  //     return this.$store.state.count;
  //   }
  // }
  // state 2 mapState
  // computed: mapState({
  //   count: state => state.count
  // })
  // state 3 ...mapState
  computed: {
    ...mapState({
      count: state => state.count
    })
  }
```

>官方例子里面有 logger demo

官方例子：[https://github.com/vuejs/vuex/blob/dev/examples/shopping-cart/store/index.js](https://github.com/vuejs/vuex/blob/dev/examples/shopping-cart/store/index.js)

# 24 25 Vue Router 的使用场景， 选择何种模式的路由及底层实现


路由模式：

1. hash 模式: 丑，无法使用锚点定位
2. history 模式: 需要后端配合，IE9不兼容



# 26, 27 Nuxt 解决了哪些问题, Nuxt 核心原理

SPA 缺点：不利于 SEO; 性能问题 首页渲染慢

缺点解决：SSR 后端渲染; 预渲染 Prerendering

预渲染 Prerendering: 适用于静态站点

SSR: 动态渲染，配置繁琐

Nuxt: 生成静态站点，动态渲染，简化配置

# 28 Vue 组件库对比(ElementUI, Ant Design, IView)

>看源码学习如何实现封装库

# 29 提升开发效率和体验常用工具

1. Vetur: 语法高亮，标签补全，模版生成， 格式化 ....
2. ESLint: 代码规范，代码错误检查
3. Prettier: 代码格式化
4. Vue DevTools: 调试工具

# 30 单元测试

1. jest 或 mocha: 测试运行器
2. @vue/test-utils: 测试工具库
3. sinon: 辅助库

# 33 Ant Design Pro 介绍

# 34 使用 Vue CLI 3 快速创建项目


# 39 如何使用路由管理用户权限管理

`/src/utils/auth.js`:

```js
export function getCurrentAuthority() {
  // 模拟
  return ["admin"];
}

export function check(authority) {
  const current = getCurrentAuthority();
  return current.some(item => authority.includes(item));
}

export function isLogin() {
  const current = getCurrentAuthority();
  return current && current[0] !== "guest";
}
```

`router.js`:

```
import findLast from "lodash/findLast";
import { check, isLogin } from "./utils/auth";
router.beforeEach((to, from, next) => {
  if (to.path !== from.path) {
    NProgress.start();
  }
  const record = findLast(to.matched, record => record.meta.authority);
  if (record && !check(record.meta.authority)) {
    if (!isLogin() && to.path !== "/user/login") {
      next({
        path: "/user/login"
      });
    } else if (to.path !== "/403") {
      // 没有权限
      next({
        path: "/403"
      });
    }
    NProgress.done();
  }
  next();
});
```

# 40 更加精细化的权限设计(权限组件，权限指令)

>例子：控制当个按钮的权限

## 组件式权限控制

`/src/components/Authorized.vue`:

```js
<script>
import { check } from "../utils/auth";
export default {
  functional: true,
  props: {
    authority: {
      type: Array,
      required: true
    }
  },
  render(h, context) {
    const { props, scopedSlots } = context;
    return check(props.authority) ? scopedSlots.default() : null;
  }
};
</script>
```

```
<Authorized :authority="['admin']">
  <SettingDrawer />
</Authorized>
```

>不是管理员，SettingDrawer 就不会显示到界面上

## 指令

`/src/directives/auth.js`:

```js
import { check } from "../utils/auth";

function install(Vue, options = {}) {
  Vue.directive(options.name || "auth", {
    inserted(el, binding) {
      if (!check(binding.value)) {
        el.parentNode && el.parentNode.removeChild(el);
      }
    }
  });
}

export default install;

```

`main.js`:

```
import Auth from './directives/auth';

Vue.use(Auth);
```

使用:

```js
<button 
  v-auth="['admin']"
></button>
```

# 41 组件中如何使用图标库 ECharts Antv

其他辅助库：`resize-detector`, `lodash`

`lodash` 防抖:

```
import debounce from 'lodash/debounce'
this.resize = debounce(this.resize, 300);
```

# 42 如何高效使用 Mock

# 43 如何与服务器端进行交互

使用参数配置 mock 环境

```
"serve:mock": "vue-cli-service serve --mode mock",
```

```
process.env.VUE_APP_MOCK === 'true' ? require('./mock') : null,
```

`.env.mock`

```
VUE_APP_MOCK=true
```

# 44 创建一个普通表单 45 初始数据，自动校验，动态赋值

# 47 自己封装一个支持自动校验的组件

# 48 如何管理系统中使用的图标

1. 雪碧图
2. 字体方式管理图标
3. SVG

查看 vue cli 配置:

```
vue inspect > output.js
```

# 49 如何定制主题及动态切换主题

动态编译

>在用户做设置后，后台生成新 js，动态加载

# 50 国际化

`monent.js`: 默认全部语言包都在，可以通过配置去除不用的语言包.

`VueI18n`: 国际化工具

`queryString`: 解析 URL

```
queryString.pase(localtion.search) // url => object
```

# 51 如何高效地构建打包发布

获取打包报告：

```
npm run build -- --report
```

关键词：**按需加载**

# 52 如何构建可交互的组件文档

`raw-loader`, `vue-highlightjs`

一个组件加载两次，一次用 `vue-loader`, 一次用 `raw-loader`.

`raw-loader`:

```js
<tempalte>
  <div>
    <pre v-highlightjs="chartCode"><code class="html"></code></pre>
  </div>
</tempalte>
import charCode from '!!raw-loader!../../components/Chart';

data(){
  return {
    chartCode
  }
}
```

# 53 单元测试
# 54 发布 npm 包
# 55 Github 相关生态应用

1. CI 持续集成

```
https://travis-ci.org
https://circleci.org
```

2. 单测覆盖率

```
https://codecov.io
https://coveralls.io
```

3. 文档管理

```
github.io
gitee.io
https://www.netlify.com
```

4. issue 管理

```
https://github.com/apps/close-issue.app
issue-helper
```


















