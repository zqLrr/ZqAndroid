## Node 环境安装

1.采用官方提供的NVM 下载

```bash
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.40.0/install.sh | bash

# 下载并安装 Node.js（可能需要重启终端）
nvm install 22

# 验证环境中的 Node.js 版本是否正确
node -v # 应该输出 `v22.11.0`

# 验证环境中的 npm 版本是否正确
npm -v # 应该输出 `10.9.0`
```

# Vue

使用优势：

* 组件化
* 声明式编程（需要了解所有的命令）
* 虚拟的Dom+diff算法

## 快速创建

使用vite 创建Vue 应用

```bash
npm create vite@latest vite-vue-project -- --template vue
```

使用官方create-vue 创建Vue应用

```bash
npm create vue@latest
```

联系：

都是采用的vite 构建工具和vue.js框架

区别：

* vite 可以选择多个vue模版来创建应用，也可以自定义vue模版
* create-vue 会提供更多的选项，比如是否使用vue-router，是否使用Ts 等等

工程结构：

```bash
my-vue-project/
├── index.html          # 入口 HTML 文件
├── package.json        # 项目依赖和脚本
├── vite.config.js      # Vite 配置文件
├── src/                # 源代码目录
│   ├── main.js         # 项目入口文件
│   ├── App.vue         # 主 Vue 组件
│   └── assets/         # 静态资源
└── node_modules/       # npm 安装的依赖
```

App.vue 和 main.ts 是主要App的入口

```typescript
import './assets/main.css'

import { createApp } from 'vue'
import { createPinia } from 'pinia'

import App from './App.vue'
//添加router
import router from './router' 

const app = createApp(App)
app.use(createPinia())
app.use(router)
app.mount('#app')
```



## [Vue-Router](https://router.vuejs.org/zh/guide/)

### 创建路由器实例

```typescript
import { createMemoryHistory, createRouter } from 'vue-router'

//跳转的view
import HomeView from './HomeView.vue' 
import AboutView from './AboutView.vue'

//路径
const routes = [
  { path: '/', component: HomeView },
  { path: '/about', component: AboutView },
]

const router = createRouter({
  history: createMemoryHistory(),
  routes,
})
```

### 注册路由器插件

在main.ts 注册路由插件

```typescript
//添加router
import router from './route
createApp(App)
  .use(router)
  .mount('#app')
```

### Vue-Router插件的作用

1. [全局注册](https://cn.vuejs.org/guide/components/registration.html#global-registration) `RouterView` 和 `RouterLink` 组件。
2. 添加全局 `$router` 和 `$route` 属性。
3. 启用 `useRouter()` 和 `useRoute()` 组合式函数。
4. 触发路由器解析初始路由。

### VueRouter 使用

`RouterView`  组件：是一个占位符，用来展示Router路由展示的View，默认展示 `/` 路由的组件

`RouterLink` 组件: 是一个路由展示组件，结合`RouterView`使用。

```vue
  <nav>
      <RouterLink to="/">Home</RouterLink>
      <RouterLink to="/test">About</RouterLink>
    </nav>
    <RouterView />
```

 `$router` 和 `$route`在选项式API 中使用：

```vue
<script>
export default {
  methods: {
    goToAbout() {
      this.$router.push('/about')
    },
  },
}
</script>

<template>
  <h2>HomeView</h2>
  <button @click="goToAbout">Go to About</button>
</template>
```

 `useRouter()` 和 `useRoute()` 组合式函数d的使用

```vue
<script setup lang="ts">
import { useRouter } from 'vue-router' // 导入 useRouter
import { useRoute } from 'vue-router'  // 导入 useRoute

// 获取 router 实例
const router = useRouter()

// 获取当前路由信息（如果需要）
const route = useRoute()

// 跳转到 About 页面
const goToAbout = () => {
  router.push('/')
}
</script>

<template>
  <div class="about">
    <h1>This is an about page</h1>
    <button @click="goToAbout">Go to Home</button>
  </div>
</template>
```





## 模版语法

### 插值语法

文本最常用的是：`{{}}`

动态的实现标签属性的插值： v-bind:property,可以简写为`:property`

Javascript表达式：限制只能是单个表达式

插入原始的html:需要指令：v-html

### 指令语法

* 常用指令：
  - v-once :执行一次性插值，不会改变
  
  - v-bind : 绑定属性  动态绑定参数 v-bind:[attributeName] ="value"  简写` ：`
  
    - Class 与Style的绑定
  
      ```vue
      //根据true/false 显示class style
      <view class="static"
        v-bind:class="{ active: isActive, 'text-danger': hasError }"
      ></view>
      //绑定class数组
      <view v-bind:class="[activeClass, errorClass]"></view>
      ```
  
  - v-on: 绑定事件 动态绑定参数 v-on:[eventName] ="function()"    简写 `@`
  
  - v-if :条件控制语句
  
    ```vue
    <view v-if="awesome === 1">1</view>
    <view v-else-if="type === 2">2</view>
    <view v-else>Other</view>
    ```
  
    同时Vue在渲染的时候，通常会复用已有的元素，如果不想复用，可以加一个`key`来区分。
  
    ```vue
    <template v-if="loginType === 'username'">
      <label>Username</label>
      <input placeholder="Enter your username" key="username-input">
    </template>
    <template v-else>
      <label>Email</label>
      <input placeholder="Enter your email address" key="email-input">
    </template>
    ```
  
  -  v-show: 根据条件确定是否显示，与V-if的功能有些类似
  
    区别：
  
    v-if：切换的开销比较高，适合没有频繁切换的场景
  
    v-show：渲染的开销比较高，但是切换就很快，因为只是css的切换，适合频繁切换的场景
  
  - v-for：循环指令
  
    避免v-if和v-for 显示在一个角度里

## 计算属性

computed : 将复杂的计算逻辑写在此处，减少模版的厚重。

```vue
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>
// vue.js
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // 计算属性的 getter
    reversedMessage: function () {
      // `this` 指向 vm 实例
      return this.message.split('').reverse().join('')
    }
  }
})
```

Computed vs methods

实现的效果可以相同，但是computed是响应式依赖，也就是其中的message值不发生改变，它就会直接返回计算结果，而methods会重新执行函数。

computed  vs  watch

computed 简化了代码量，比watch简便。

computed  具有getter和setter函数，不但可以被动修改，还可以主动修改。

```vue
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

## 侦听器watch

computed在大多数的情况下合适，但是当需要在数据变化时执行异步或开销较大的操作时，watch是最有用的。

## 数据绑定

V-bind: 只能是单向绑定，都可以

v-model: 双向绑定，只能应用于表单交互的标签。 `v-model:value ="data"` 简写为 `v-model ="data"

## Vue数组处理方法

变异方法：在旧数组上更新

- push() 尾部添加

- pop() 尾部删除

- shift() 头部添加

- unshift() 头部删除

- splice()

  Splice(index,len,[item]) 替换指定位置的item

- sort() 排序 

  ```vue
  _this.dataValue.sort((a,b)=>{
  return  a>b;
  });
  ```

- reverse() 反转

替换数组（非变异方法）：返回一个新数组

* filter() 过滤数据
* concat()
* slice()

js 语法

## 原形🌟

在Js中一切皆为对象，方法是对象，方法的原型Function.prototype也是对象，对象具有属性（\__proto__）,称其为隐式原型。方法是一个特殊的对象，它除了具有隐式原型，还具有独特的属性(prototype),指向原型对象原型对象也有一个属性叫constructor，这个属性包含一个指针，指向原构造函数。

注：通过Function.prototype.bind方法构造出来的函数没有prototype属性。

注：Object.prototype为null

它们的指向关系如下图所示：

区别：对象的隐式原型=函数的显示原型

原型链：自己的没有方法可以查看原型有没有，实现了继承。

## 组件

### 组件组册

### 全局注册

## [组合式 API](https://cn.vuejs.org/guide/extras/composition-api-faq.html)

组合式 API (Composition API) 是一系列 API 的集合，使我们可以使用函数而不是声明选项的方式书写 Vue 组件。它是一个概括性的术语，涵盖了以下方面的 API：

- [响应式 API](https://cn.vuejs.org/api/reactivity-core.html)：例如 `ref()` 和 `reactive()`，使我们可以直接创建响应式状态、计算属性和侦听器。
- [生命周期钩子](https://cn.vuejs.org/api/composition-api-lifecycle.html)：例如 `onMounted()` 和 `onUnmounted()`，使我们可以在组件各个生命周期阶段添加逻辑。
- [依赖注入](https://cn.vuejs.org/api/composition-api-dependency-injection.html)：例如 `provide()` 和 `inject()`，使我们可以在使用响应式 API 时，利用 Vue 的依赖注入系统。

组合式 API 是 Vue 3 及 [Vue 2.7](https://blog.vuejs.org/posts/vue-2-7-naruto.html) 的内置功能。对于更老的 Vue 2 版本，可以使用官方维护的插件 [`@vue/composition-api`](https://github.com/vuejs/composition-api)。在 Vue 3 中，组合式 API 基本上都会配合 [``](https://cn.vuejs.org/api/sfc-script-setup.html) 语法在单文件组件中使用。下面是一个使用组合式 API 的组件示例：

```
<script setup>
import { ref, onMounted } from 'vue'

// 响应式状态
const count = ref(0)

// 更改状态、触发更新的函数
function increment() {
  count.value++
}

// 生命周期钩子
onMounted(() => {
  console.log(`The initial count is ${count.value}.`)
})
</script>

<template>
  <button @click="increment">Count is: {{ count }}</button>
</template>
```

和选项式Api的联系和区别

* 联系

  可以两种Api共同使用，选项式 API 的组件中通过 [`setup()`](https://cn.vuejs.org/api/composition-api-setup.html) 选项来使用组合式 API

* 区别

  * 组合式api的优势

    1.更符合typeScript语法的写法

    2.更好的类型推导

    3.更小的生产体积

  * 组合式Api相比于选项式Api的劣势

    1.没有选项式Api的固定模版

# [Vite](https://cn.vitejs.dev/guide/)

前端构建工具

# 开发实战

## Basic工程 使用说明

### 组件注册

src/type/auto-importts.d.ts :全局引入

src/type/components.d.ts : 组件注册



# Vue.js

使用优势：

* 组件化
* 声明式编程（需要了解所有的命令）
* 虚拟的Dom+diff算法

全局配置：Vue官网的API去查看

## 原形🌟

在Js中一切皆为对象，方法是对象，方法的原型Function.prototype也是对象，对象具有属性（\__proto__）,称其为隐式原型。方法是一个特殊的对象，它除了具有隐式原型，还具有独特的属性(prototype),指向原型对象原型对象也有一个属性叫constructor，这个属性包含一个指针，指向原构造函数。

注：通过Function.prototype.bind方法构造出来的函数没有prototype属性。

注：Object.prototype为null

它们的指向关系如下图所示：

![image-20211109191307698](../../../../Library/Application Support/typora-user-images/image-20211109191307698.png)

区别：对象的隐式原型=函数的显示原型

原型链：自己的没有方法可以查看原型有没有，实现了继承。

## 模版语法

### 插值语法

文本最常用的是：`{{}}`

动态的实现标签属性的插值： v-bind:property,可以简写为`:property`

Javascript表达式：限制只能是单个表达式

插入原始的html:需要指令：v-html

### 指令语法

* 常用指令：

  - v-once :执行一次性插值，不会改变

  - v-bind : 绑定属性  动态绑定参数 v-bind:[attributeName] ="value"  简写` ：`

    - Class 与Style的绑定

      ```vue
      //根据true/false 显示class style
      <view class="static"
        v-bind:class="{ active: isActive, 'text-danger': hasError }"
      ></view>
      //绑定class数组
      <view v-bind:class="[activeClass, errorClass]"></view>
      ```

  - v-on: 绑定事件 动态绑定参数 v-on:[eventName] ="function()"    简写 `@`

  - v-if :条件控制语句

    ```vue
    <view v-if="awesome === 1">1</view>
    <view v-else-if="type === 2">2</view>
    <view v-else>Other</view>
    ```

    同时Vue在渲染的时候，通常会复用已有的元素，如果不想复用，可以加一个`key`来区分。

    ```vue
    <template v-if="loginType === 'username'">
      <label>Username</label>
      <input placeholder="Enter your username" key="username-input">
    </template>
    <template v-else>
      <label>Email</label>
      <input placeholder="Enter your email address" key="email-input">
    </template>
    ```

  - v-show: 根据条件确定是否显示，与V-if的功能有些类似

    区别：

    v-if：切换的开销比较高，适合没有频繁切换的场景

    v-show：渲染的开销比较高，但是切换就很快，因为只是css的切换，适合频繁切换的场景

  - v-for：循环指令

    避免v-if和v-for 显示在一个角度里

## 计算属性

computed : 将复杂的计算逻辑写在此处，减少模版的厚重。

```vue
<div id="example">
  <p>Original message: "{{ message }}"</p>
  <p>Computed reversed message: "{{ reversedMessage }}"</p>
</div>
// vue.js
var vm = new Vue({
  el: '#example',
  data: {
    message: 'Hello'
  },
  computed: {
    // 计算属性的 getter
    reversedMessage: function () {
      // `this` 指向 vm 实例
      return this.message.split('').reverse().join('')
    }
  }
})
```

Computed vs methods

实现的效果可以相同，但是computed是响应式依赖，也就是其中的message值不发生改变，它就会直接返回计算结果，而methods会重新执行函数。

computed  vs  watch

computed 简化了代码量，比watch简便。

computed  具有getter和setter函数，不但可以被动修改，还可以主动修改。

```vue
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

## 侦听器watch

computed在大多数的情况下合适，但是当需要在数据变化时执行异步或开销较大的操作时，watch是最有用的。

## 数据绑定

V-bind: 只能是单向绑定，都可以

(微博小程序不支持)v-model: 双向绑定，只能应用于表单交互的标签。 `v-model:value ="data"` 简写为 `v-model ="data"

## Vue数组处理方法

变异方法：在旧数组上更新

- push() 尾部添加

- pop() 尾部删除

- shift() 头部添加

- unshift() 头部删除

- splice()

  Splice(index,len,[item]) 替换指定位置的item

- sort() 排序 

  ```vue
  _this.dataValue.sort((a,b)=>{
  return  a>b;
  });
  ```

- reverse() 反转

替换数组（非变异方法）：返回一个新数组

* filter() 过滤数据
* concat()
* slice()



 
