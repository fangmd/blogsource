---
title: 初学 Vue
date: 2019-02-27 13:18:12
tags: [vue]
category: 前端

---

# npm

install packages to global

```
npm install -g [xxx]
```

# Vue 基本记录

安装 vue 命令行工具

```
npm install -g @vue/cli

vue --version
```

create project

```
vue create hello-world
cd hello-world
npm run serve
```

# Vue 实例

数据
方法
钩子
生命周期

1. 访问根实例

```
// 可以访问 data，computed, methods
this.$root.foo // foo 是 data 里面的数据
this.$root.foo = 2 // foo 是 data 里面的数据
```

>推荐使用 Vuex 管理应用状态



# Vue Component 对象


```
name
props: {}
components: {}
created:(){}
mounted:(){}
updated:(){}
destroyed:(){}
data(){
    return {

    }
}
methods:{
    reversedMessage2: function(){
        return "msg";
    }
}
computed:{
    reversedMessage: function(){
        return 'MSG';
    }
}
```

1. 访问父级组件实例 `$parent` `$emit`

2. 访问子组件

```
<base-input ref="usernameInput"></base-input>

this.$refs.usernameInput
```

3. 强制更新界面 `$forceUpdate`


# 组件

1. 组件必须只有一个根元素
2. 通过 props 给组件传参数，(子控件中不能修改 props 值，如果要修改可以创建一个 data 值降 props 值作为初始化值), props 值如果要修改可以创建一个计算属性

props 验证：可以设置默认值，是否必须，类型，定义验证函数

```
  props: {
    // 基础的类型检查 (`null` 和 `undefined` 会通过任何类型验证)
    propA: Number,
    // 多个可能的类型
    propB: [String, Number],
    // 必填的字符串
    propC: {
      type: String,
      required: true
    },
    // 带有默认值的数字
    propD: {
      type: Number,
      default: 100
    },
    // 带有默认值的对象
    propE: {
      type: Object,
      // 对象或数组默认值必须从一个工厂函数获取
      default: function () {
        return { message: 'hello' }
      }
    },
    // 自定义验证函数
    propF: {
      validator: function (value) {
        // 这个值必须匹配下列字符串中的一个
        return ['success', 'warning', 'danger'].indexOf(value) !== -1
      }
    }
  }
```

>验证失败的时候，控制台会警告

3. 推荐你始终使用 kebab-case 的事件名。
4. v-slot 插槽，向自定义组件中动态插入内容

[新内容](https://github.com/vuejs/rfcs/blob/master/active-rfcs/0001-new-slot-syntax.md)

v-slot 可以设置默认显示内容

5. 动态组件

通过 `is` 切换组件: 动态重新创建组件

```
<component v-bind:is="currentTabComponent"></component>
```

使用 `<keep-alive>` 切换组件：切换后组件不会重新创建

```
<!-- 失活的组件将会被缓存！-->
<keep-alive>
  <component v-bind:is="currentTabComponent"></component>
</keep-alive>
```

6. 异步组件，组件加载状态



# Template

指令的使用 `v-`

1. 文本插入，插入值

```
<span> Message: {{ msg }}</span>

<!-- 数据只绑定一次 -->
<span v-once> Message: {{ msg }}</span>
```

1. 插入原始 HTML

```
<p>Using v-html directive: <span v-html="rawHtml"></span></p>
```

>注意：动态渲染容易造成 XSS攻击，不要对用户输入的数据使用HTML插值

2. `v-bind` `:` 属性 绑定数据：(data, props 中的数据都可以)

```
<span v-bind:title="msg">...
<span :title="msg">...

<!-- 使用 v-bind 切换 class: isActive=true active 生效-->
<div :class="{ active: isActive }"></div>

<!-- :class 可以与 class 属性共存 -->

<!-- 绑定的数据不必内联 -->
<div v-bind:class="classObject"></div>
data: {
  classObject: {
    active: true,
    'text-danger': false
  }
}

<!-- 也可以绑定 计算属性 -->

```

>如果 msg 值异常的时候，title属性不会渲染进 span

3. `v-if`, `v-else`, `v-else-if`, 控制元素是否显示，条件渲染

```
<p v-if="seen">

<!-- seen: true|false -->

<!-- v-if, v-else -->
<p v-if="seen">III</p>
<p v-else>OOO</p>
```

4. `v-for` 绑定数组的数据

```
<!-- 迭代数组 -->
<li v-for="(item, index) in items">
<li v-for="item in items">

<!-- 迭代对象属性 -->
<div v-for="value in object">
<div v-for="(value, key) in object">
<div v-for="(value, key, index) in object">

<!-- v-for 里面可以添加函数处理 -->
<li v-for="n in evenNumbers">{{ n }}</li>
<li v-for="n in even(numbers)">{{ n }}</li>

<!-- 一段范围取值 -->
<span v-for="n in 10">{{ n }} </span>

<!-- 在组件中使用 v-for, 2.2.0+ 必须添加 key -->
<my-component v-for="item in items" :key="item.id"></my-component>


```

>v-for: 迭代数组，也可以迭代 对象的属性

注意：Vue 不能检测一下变动的数组，由于 JavaScript 的限制

```
当你利用索引直接设置一个项时，例如：vm.items[indexOfItem] = newValue
当你修改数组的长度时，例如：vm.items.length = newLength


```


5. `v-on` 添加点击事件

```
<p v-on:click="reverseMessage">Ha</p>

<p @click="reverseMessage">Ha</p>

<!-- 原生事件获取 -->
<button v-on:click="greet">Greet</button>
  // 在 `methods` 对象中定义方法
  methods: {
    greet: function (event) {
      // `this` 在方法里指向当前 Vue 实例
      alert('Hello ' + this.name + '!')
      // `event` 是原生 DOM 事件
      if (event) {
        alert(event.target.tagName)
      }
    }
  }

<!-- 原生事件获取， js参数 -->
<button v-on:click="warn('Form cannot be submitted yet.', $event)">
  Submit
</button>

methods: {
  warn: function (message, event) {
    // 现在我们可以访问原生事件对象
    if (event) event.preventDefault()
    alert(message)
  }
}

```

6. `v-model` 实现表单输入和应用状态之间的双向绑定

绑定的属性：

text, textarea: value, input
checkbox, radio: checked, change
select: value, change

```
<input v-model="message" placeholder="edit me">
<p>Message is: {{ message }}</p>
```

修饰符:`.lazy`, `.number`, `.trim`


7. 计算属性，结果基于属性值，有缓存比方法更好(在相关值没有改变的时候， 不需要计算直接返回)，不基于属性的计算不要使用这个函数

```
<p>Computed reversed message: "{{ reversedMessage }}"</p>

  computed: {
    // 计算属性的 getter
    reversedMessage: function () {
      // `this` 指向 vm 实例
      return this.message.split('').reverse().join('')
    }
  }
```

>计算属性缓存 VS 方法
>计算属性 VS 侦听属性

计算属性默认只有 get, 添加 set

```
computed: {
  fullName: {
    // getter
    get: function () {
      return this.firstName + ' ' + this.lastName
    },
    // setter
    set: function (newValue) {
      var names = newValue.split(' ')
      this.firstName = names[0]
      this.lastName = names[names.length - 1]
    }
  }
}
```

8. 使用 key 管理可复用的元素

>html 内容改变的时候是否要重新渲染组件，通过设置不同的 key 实现强制重新渲染
>重新渲染后：用户输入的内容会清空

```
<div v-for="item in items" :key="item.id">
  <!-- 内容 -->
</div>
```


9. `v-show` 控制是否显示，和 `display` 一样，隐藏后在 html 源码中还是能找到

>v-show：初始化开销大，切换开销小。适合切换频率高的内容

10. `v-once` 创建低开销的静态组件(通常不使用，除非有性能问题)


## 自定义指令

1. 组册全局指令：

```
// 注册一个全局自定义指令 `v-focus`
Vue.directive('focus', {
  // 当被绑定的元素插入到 DOM 中时……
  inserted: function (el) {
    // 聚焦元素
    el.focus()
  }
})
```

2. 注册局部指令

```
directives: {
  focus: {
    // 指令的定义
    inserted: function (el) {
      el.focus()
    }
  }
}
```

3. 钩子函数：`bind`, `inserted`, `update`, `componentUpdated`, `unbind`

参数：`el`, ...

4. 简写例子: 在 bind, update 时触发

```
Vue.directive('color-swatch', function (el, binding) {
  el.style.backgroundColor = binding.value
})
```

## template 中 元素 操作

1. 使用 refs 获取 template 中元素

```
<div class="el-popover" ref='popover'></div>

// 获取 div 这个元素
this.$refs.popover
```


# 动画

1. 使用 `transition` 包裹需要添加动画的组件

```
  <transition name="fade">
    <p v-if="show">hello</p>
  </transition>

.fade-enter-active, .fade-leave-active {
  transition: opacity .5s;
}
.fade-enter, .fade-leave-to /* .fade-leave-active below version 2.1.8 */ {
  opacity: 0;
}  
```

2. 过渡类名
3. CSS 过渡(常用)
4. CSS 动画
5. 自定义过渡的类名, 结合第三方库 Animate.css

## 状态过渡

对于数据本身的效果：

- 数字和运算
- 颜色的显示
- SVG 节点的位置
- 元素的大小和其他的属性


1. 数字动画

```
<input v-model.number="number" type="number" step="20">
<p>{{ animatedNumber }}</p>

  data: {
    number: 0,
    tweenedNumber: 0
  },
  computed: {
    animatedNumber: function() {
      return this.tweenedNumber.toFixed(0);
    }
  },
  watch: {
    number: function(newValue) {
      TweenLite.to(this.$data, 0.5, { tweenedNumber: newValue });
    }
  }
```

2. 颜色过渡动画
3. 动态状态过渡

# CSS

1. 限制 css 作用范围

```
<style scoped>: Add "scoped" attribute to limit CSS to this component only
```

2. `background-size` 必须在 `background` 之下

```css
    background: url(../../../src/assets/images/promotion/p190227/three.png)
      no-repeat;
    background-size: 100% 100%;
```

3. CSS 定位属性 优先级 left, top > right, bottom

在使用的时候如果使用 right 或 bottom 定位，需要设置 `top: auto; left: auto;`


4. a  标签里面嵌套 img 的时候 如何撑开 a

a: 直接设置 a 的宽高
b: 设置 a display 为 block, 但是会有阴影，设置 a bg 为阴影的时候能看到，需要设置 img block

5. a 标签里面放 span(span 设置背景图)

span 设置 vertical-align: middle， 可以让 a 的位置居中。

6. a 取消跳转, 只使用 js 获取点击事件

```
href="javascript:;"
```

7. vertical-align: middle; 配合 display: inline-block 实现元素垂直居中







# Vue Router

1. 动态路由匹配

```
{path: 'user/:id, component: User'}
```

获取参数id： `$route.params.id`


2. 同路由复用问题，响应路由参数的变化

`user/one - > /user/two` 的时候会复用当前页面，生命周期钩子不会调用。

```
watch: {
  '$route'(to, from ){
    // 变化
  }
}

//or >=2.2
beforeRouteUpdate(to, from, next){
  // don't forget to call next()
}
```

3. 模糊匹配, 使用通配符 `*`

通配符参数获取：`$route.params.patchMatch`

4. 嵌套路由

```
{ path: '/user/:id', 
  component: User,
  children: [
  {
    // 设置默认的渲染组件
    patch: '',
    component: UserHome
    },
        {
          // 当 /user/:id/profile 匹配成功，
          // UserProfile 会被渲染在 User 的 <router-view> 中
          path: 'profile',
          component: UserProfile
        },
        {
          // 当 /user/:id/posts 匹配成功
          // UserPosts 会被渲染在 User 的 <router-view> 中
          path: 'posts',
          component: UserPosts
        }
      ]
}
```

5. js 中路由跳转：给 路由命名后 `this.$router.push({name: 'orderList'})`

6. 命名视图(sidebar + main 等类似模式的时候使用)
7. 重定向，别名
8. 路由时传参数
9. 导航守卫

完整的导航解析流程

```
导航被触发。
在失活的组件里调用离开守卫。
调用全局的 beforeEach 守卫。
在重用的组件里调用 beforeRouteUpdate 守卫 (2.2+)。
在路由配置里调用 beforeEnter。
解析异步路由组件。
在被激活的组件里调用 beforeRouteEnter。
调用全局的 beforeResolve 守卫 (2.5+)。
导航被确认。
调用全局的 afterEach 钩子。
触发 DOM 更新。
用创建好的实例调用 beforeRouteEnter 守卫中传给 next 的回调函数。
```

10. 元信息

常用：设置是否需要登入的界面; 放置标题

在全局导航守卫中检查元数据：

```
if(to.matched.some(record => record.meta.requiresAuth)
```

11. 获取网络数据位置(路由前，路由后)


```js
export default {
  data () {
    return {
      loading: false,
      post: null,
      error: null
    }
  },
  created () {
    // 组件创建完后获取数据，
    // 此时 data 已经被 observed 了
    // //或在 mouted 函数中请求数据
    this.fetchData()
  },
  watch: {
    // 如果路由有变化，会再次执行该方法
    '$route': 'fetchData'
  },
  methods: {
    fetchData () {
      this.error = this.post = null
      this.loading = true
      // replace getPost with your data fetching util / API wrapper
      getPost(this.$route.params.id, (err, post) => {
        this.loading = false
        if (err) {
          this.error = err.toString()
        } else {
          this.post = post
        }
      })
    }
  }
}
```


## 路由模式

history, hash, abstract

区别：hash url 中有 `#`, 页面跳转后对于后端没有影响

>history, hash 都是游览器自身的特性，vue-router 只是利用了这两个特性实现路由

