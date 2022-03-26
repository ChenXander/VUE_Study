## 认识 `Vue`

### 1.`Vue`的优点？`Vue`的缺点？

* **优点：**
  1. **渐进式**：渐进式指的可以由浅入深的，由简单到困难的一种方式。按需选择要用的部分
   2. **组件化开发**：将复杂的业务拆分为一个个组件，从外部是一个整体，从内部是诸多个体。开发具有较高灵活性
   3. **虚拟 `DOM`**：将 `DOM`抽象成一个 `js`对象，更加轻量级描述 `DOM`，提高了 `DOM`操作和渲染效率，并且实现了==跨平台能力==，不再局限于浏览器的`DOM`，可以是安卓和`IOS`的原生组件，可以是小程序，也可以是各种`GUI`
   4. **响应式数据**：数据发生变化后，会重新对页面渲染
   5. **单页面路由**：[路由](#路由)
   6. **数据与视图分开**：[MVVM](#mvvm)，`View`视图(前端`DOM`)和 Model 数据通过`Viewmodel`这个中间件进行通讯，`Viewmodel`监听双方的动作，及时通知对方进行相应的改变
  
* **缺点：**
  1. 单页面不利于`SEO`
   2. 不兼容 IE
   3. 首屏加载时间长

### 2.`Vue`和 `React`的异同点

* **相同点：**
  
   1. 都是单向数据流
   2. 都使用了虚拟`DOM`的技术
   3. 都支持`SSR`
   4. 组件化开发

* **不同点：**
  
   1. 前者`template`，后者`JSX`
   2. 数据改变，前者响应式，后者手动`setState`
   3. `React`单向绑定，`Vue`双向绑定
   4. `React`状态管理工具`Redux`、`Mobx`，`Vue`状态管理工具`Vuex`

### 3.`MVVM`是什么？和 `MVC`有何区别呢？<a name="mvvm"></a>

* **`MVC`**
  
   1. `Model`：负责从数据库中取数据
   2. `View`：负责展示数据
   3. `Controller`：用户交互的地方，例如点击事件
   4. 思想：`Controller` 将 `Model` 的数据展示在 `View` 上
   
* **`MVVM`**
  
   1. `VM`：就是 `View-Model`，数据双向绑定
   2. `Model`：取数据的地方
   3. `View`：展示数据的地方
   4. 思想：`View` 和 `Model` 实现数据同步，不需要手动更新
   
   > **注意：`Vue` 不严格符合 `MVVM`，因为 `MVVM`规定 `Model`和 `View`不能直接通信，而 `Vue`可以使用 `ref`进行通信**

------



## 基础语法

### 1.计算属性

* `computed`都包含一个`getter`和一个`setter`，`setter`一般不用，只用`getter`进行读取。
* 计算属性会进行缓存，如果多次使用时，计算属性只会调用一次，而`methods`会多次调用，因此计算属性更加高效。

### 2.`v-on`参数：(语法糖：@)

1. 情况一：
   
    * 如果该方法不需要额外参数，那么方法后的()可以不添加。
    * 但是注意：如果方法本身中有一个参数或没有参数，那么会默认将原生事件`event`参数传递进去

2. 情况二：
   
    * 如果需要同时传入某个参数，同时需要`event`时，可以通过`$event`传入事件

### 3.`v-on`修饰符

1. `.stop`：调用`event.stopPropagation()`(绑给子元素)，阻止事件冒泡

2. `.prevent`：调用`event.preventDefault()`，阻止默认事件

3. `.once`：只触发一次回调

4. `.capture`：使用事件捕获模式

5. `.self`：点击事件绑定本身才触发

6. `.passive`：相当于给移动端滚动事件加一个[.lazy](#lazy)

7. `.left、.middle、.right`：鼠标左中右键的触发
* `.native`：绑定事件在自定义组件上时，确保能执行

> **使用修饰符时，顺序很重要；相应的代码会以同样的顺序产生。因此，用 `v-on:click.prevent.self` 会阻止所有的点击，而 `v-on:click.self.prevent` 只会阻止对元素自身的点击。**
> 
> **不要把 `.passive` 和 `.prevent` 一起使用，因为 `.prevent` 将会被忽略，同时浏览器可能会向你展示一个警告。请记住，`.passive` 会告诉浏览器你不想阻止事件的默认行为。**

### 4.`V-bind`修饰符

1. `camel`：确保变量名会被识别成驼峰命名

2. `.sync`：简化子修改父值的步骤

3. `.prop` ：通过自定义属性存储变量，避免暴露数据，防止污染 HTML 结构
   
   > **`.prop` - 作为一个 DOM property 绑定而不是作为 attribute 绑定。**
   > 
   > **在绑定 prop 时，prop 必须在子组件中声明。可以用修饰符指定不同的绑定类型。**

```vue
<!-- 绑定一个全是 attribute 的对象 -->
<div v-bind="{ id: someProp, 'other-attr': otherProp }"></div>

<!-- 通过 prop 修饰符绑定 DOM attribute -->
<div v-bind:text-content.prop="text"></div>

<!-- prop 绑定。“prop”必须在 my-component 中声明。-->
<my-component :prop="someThing"></my-component>

<!-- 通过 $props 将父组件的 props 一起传给子组件 -->
<child-component v-bind="$props"></child-component>
```

### 5.`v-if`, `v-else-if`, `v-else`

* `input`输入框--Vue 在进行 DOM 渲染时，出于性能考虑，会尽可能的复用已经存在的元素，而不是重新创建新的元素。
* 解决方案：给对应的`input`添加 key，并且 key 不相同

### 6.`v-show`

1. ==v-if当条件为false时，不会有对应的元素在 DOM 中==
2. ==v-show当条件为false时，是将元素的display属性设置为none==
3. **如何选择**：
* **当需要在显示与隐藏之间切片很频繁时，使用`v-show`**
* **当只有一次切换时，通过使用`v-if`**

### 7.`v-for`

* 使用时给对应的元素或组件添加`:key` 属性，来给每个节点做唯一标识，方便虚拟 DOM 的 Diff 算法正确识别节点。
* key 的作用是为了高效的更新虚拟 DOM

### 8.检测数组更新

* Vue 是响应式的，所以当数据发生变化时，Vue 会自动检测数据变化，视图会发生对应的更新
* Vue 中包含了一组观察数组编译的方法，使用它们改变数组也会触发视图的更新
* **但并不是所有的方法都是是响应式的**

#### 常用的响应式的数组方法

1. `push()`：数组最后添加元素，可以添加多个，`push('a', 'b', 'c')`

2. `pop()`：删除数组最后一个元素

3. `shift()`：删除数组第一个元素

4. `unshift()`：在数组最前面添加元素，可以添加多个，`unshift('a', 'b', 'c')`

5. `splice()`：删除元素/插入元素/替换元素
   
   ```js
   splice(start, count, 'a', 'b', 'c')：第一个参数截取数组的索引起点
                                        第二个参数截取数组元素的个数
                                        第三个参数要添加的元素
   ```

6. `sort()`：数组排序

7. `reverse()`：逆序数组

8. **vue 提供的修改方法(响应式)：**
   
   ```vue
   增加：
   Vue.set(arr, index, 'a')，第一个参数要修改的对象
                            第二个参数索引值
                            第三个参数修改后的值
   删除：
   Vue.delete(目标, 参数)
   目标：Object | Array
   参数：propertyName/index
   ```
   
9. ==**注意：通过索引值直接修改数组不是响应式的**==

### 9.`v-model`

* 原理：`v-model`其实是一个语法糖，它的背后本质上是包含两个操作：
  
  ```vue
  v-bind绑定一个value属性
  v-on指令给当前元素绑定input事件
  <input type="text" v-model="message">
  等同于
  <input type="text" v-bind:value="message" v-on:input="message = $event.target.value">
  ```

* `v-model`中 `radio`(单选框的应用)：通过 `v-model`绑定相同变量数据，实现单选框的互斥以及双向数据绑定，原来的单选框 `name`属性就可以去掉了

* `v-model`中 `checkbox`(多选框的应用)：
  
  ```vue
  分为两种情况：单个勾选框和多个勾选框
      单个勾选框：v-model即为布尔值，此时input的value并不影响v-model的值。
      多个复选框：当是多个复选框时，因为可以选中多个，所以对应的data中属性是一个数组。当选中某一个时，就会将input的value添加到数组中。
  <div id="app">
    单选框
    <label for="check">
      <input type="checkbox" v-model="checked" id="check" >同意协议
    </label>
    多选框
    <input type="checkbox" v-model="hobbies" value="篮球" >篮球
    <input type="checkbox" v-model="hobbies" value="足球" >足球
    <input type="checkbox" v-model="hobbies" value="台球" >台球
  </div>
  <script>
    let app = new Vue({
      el: '#app',
      data: {
        checked: false,
        hobbies: []
      }
    })
  ```

* `input`框中的值绑定：在 `label`中进行 for 循环遍历，通过 `:value`、`:id`等对 `input`进行值绑定，其实就是 `v-bind`在 `input`框中的应用

* `v-model`修饰符：<a name="lazy"></a>
  
   1. lazy 修饰符：v-model.lazy 让数据在失去焦点或者回车时才会更新
   2. number 修饰符：v-model.number 让在输入框中输入的内容自动转成数字类型
   3. trim 修饰符：v-model.trim 过滤内容左右两边的空格

---

## Vue 组件化

### 1.组件的使用分成三个步骤：(底层写法)

```vue
1.创建组件构造器： const cpnC = Vue.extend({
template: `
  <div>
    <h2>xxxx</h2>
    <p>123456</p>
  </div>`
})
2.注册组件：Vue.component('test', cpnC) 全局注册 3.使用组件：
<test />
注意：必须挂载到根组件内
```

### 2.父组件和子组件

* 组件和组件之间存在层级关系，而其中一种非常重要的关系就是父子组件的关系
* **注意：子组件注册到父组件的`components`时，Vue 会编译好父组件的模块，父组件已经有了子组件的内容。子组件出口`<child />`是只能被父组件识别，并且被浏览器忽略，不能在 Vue 实例中和父组件出口写在一起**

### 3.注册组件语法糖

```vue
(全局注册)：
Vue.component('test', {
  template: `
    <div>
      <h2>xxxx</h2>
      <p>545446</p>
      <p>45545</p>
    </div>`
})
(局部注册)：
components: {
  'test': template: `
            <div>
              <h2>xxxx</h2>
              <p>545446</p>
              <p>45545</p>
            </div>`
}

进一步简化，抽离template
<template id="cpn">
  <div><p>模板的分离</p></div>
</template>

在组件实例中components: { 'test': { template: '#cpn' } }
```

### 4.组件的数据访问

* 组件中不能直接访问 Vue 实例中的 `data`，Vue 组件有自己保存数据的地方：
  
  ```js
  data () {  data属性必须是函数，并且返回一个对象保存着数据！！！
    return {}
  }
  ```

* **为什么 data 是个函数并且返回一个对象**
  
   * 原因：首先如果 data 不是函数是对象，Vue 会直接报错。其次，所有组件共用一个 data 对象地址，组件多次使用会出现数据互相干扰！！！使用函数并返回对象，Vue 能让每个组件对象都返回一个新的对象，每个组件数据则相互隔离
     
      * 简洁来说：==**就是防止组件被多个页面使用时，造成的变量互相污染**==

### 5.父子组件的通信

1. 父组件通过`props`向子组件传递数据：
* 父组件中：`<child :变量名="data数据" />`

* 子组件中：使用选项 `props`来声明需要从父级接收到的数据，`props`有两种方式
  
  ```js
  方式一：字符串数组，数组中的字符串就是传递时的名称。
     props: ['父组件中传来的变量名']
     使用：<div>传来的数据：{{变量名}}</div>
     注意：如果子组件中props的变量名使用了驼峰写法，则父组件中要以-进行分割使用！！！
       即：props: {mySchool: 类型} -> <child :my-school="data数据" />
  方式二：对象，对象可以设置传递时的类型，也可以设置默认值等。
     props: {
       变量名: String (单个),
       变量名: [String, Number, Boolean, Array, Object, Date, Function, Symbol] (多个)
       变量名: {
         type: String/Number...,
         required: true,(必须的),
         default: '默认值',
         当默认值是对象或数组时,default需要使用函数返回对象或数组
         default () {
           return {}/[]
         }
       }
     }
     也可以自定义验证函数
     props: {
       validator: function (value) {
         这个值必须匹配下列字符串中的一个
         retrun ['a', 'b', 'c',...].indexOf(value) !== -1
       }
     }
     有自定义构造函数时，也可以自定义验证的类型
     function Phone() {}
     porps: {
       变量名: Phone
     }
  ```
1. 子组件通过事件 `$emit` `Events`向父组件发送消息

2. 子组件中定义触发的函数，在这个函数中通过 `this.$emit(‘自定义事件名’, 要传递的数据参数item)`

3. 父组件中通过 `@自定义事件名=“定义响应的函数(item)”`，然后在 `methods`中通过响应的函数接收参数并进行具体操作
   
   ```vue
   子组件中：
   <button @click="btnClick(item)"></button>
   methods: { btnClick (item) { this.$emit('btn-event', item) } } 父组件中：
   <child @btn-event="btnResponse" />
   因为是自定义事件，所以不受vue中默认传参event的影响，这里可以省略参数。vue-cli中可以使用驼峰写法
   methods: { btnResponse (item) { 具体操作console.log('btn-event', item) } }
   ```

### 6.父组件直接访问子组件

* 方法一：`$children`通过索引值拿到子组件，但是当子组件过多其索引值不易确定，不推荐使用
* 方法二：`$refs`给子组件绑定`ref`值`<child ref="child1">`，`$refs.child1`就能拿到指定子组件对象，类似于标签中的`id`属性。此方法不受子组件数量影响，推荐使用

### 7.非父子组件通信

* 方案一：事件总线，较为繁琐
* 方案二：[vuex状态管理，推荐使用](#vuex)

### 8.插槽 `slot`

* 具名插槽：
  
  ```vue
  子组件模板：
  <template id="myCpn">
    <div>
      <slot name="变量名1">名字1</slot>
      <slot name="变量名2">名字2</slot>
      <slot name="变量名3">名字3</slot>
    </div>
  </template>
  父组件根组件：
  <div id="app">
    没有传入任何内容，则按子组件中slot默认显示
    <my-cpn></my-cpn>
    传入某一个内容时，替换子组件中对应的插槽，剩下的依然按默认显示
    <my-cpn>
      <span slot="变量名1">我是替换的名字1</span>
    </my-cpn>
    传入所有内容，全部替换
    <my-cpn>
      <span slot="变量名1">我是替换的名字1</span>
      <span slot="变量名2">我是替换的名字2</span>
      <span slot="变量名3">我是替换的名字3</span>
    </my-cpn>
  </div>
  ```

* 作用域插槽：父组件替换插槽的标签，但是内容由子组件提供
  
  ```vue
  子组件中：
  <template id="myCpn">
    <div>
      <slot name="son" :test="arr"></slot>
    </div>
  </template>
  
  <script>
  Vue.component('my-cpn', {
    template: '#myCpn',
    data() {
      return { 子组件定义数据，并且绑定到slot上
        arr: ['a', 'b', 'c']
      }
    }
  })
  </script>
  父组件中：
  <div id="app">
    <my-cpn v-slot:son="father">
      此时就能通过father调用子组件的arr数据
    </my-cpn>
  </div>
  ```

---

## 前端模块化

* `CommonJS`
  
  ```js
  导出module.exports = { test: a }
  导入let { test } = require('moduleA')
  ```

* `ES6`模块化
  
  ```vue
  导出：
    方案一：
      export function test () {}
      export class Person {}
    方案二：
      export test () {}
      export Person {};
      export {test, Person}
    方案三：(test.js)
      export default function () {} import
  
      自定义命名test1 from './test.js' test1()
      注意：export default在同一个模块中，不允许同时存在多个
  导入：
    1.<script src="./test.js" type="module"></script>
    2.import {} from './test.js'
    3.如果我们希望某个模块中所有的信息都导入
      通过*可以导入模块中所有的export变量 但是通常情况下我们需要给*起一个别名，方便后续的使用
      import * as 别名info from './test.js' console.log(info.xxx, info.)
  ```

## `Vue-router` <a name="路由"></a>

* 后端路由阶段：页面需要请求不同的路径内容时, 交给服务器来进行处理, 服务器渲染好整个页面, 并且将页面返回给客户端。
  
   * 这种情况下渲染好的页面, 不需要单独加载任何的 `js`和 `css`, 可以直接交给浏览器展示, 这样也**有利于 SEO的优化**
   
   * 缺点：
     
      1. 整个页面的模块由后端人员来编写和维护的
      2. 前端开发人员如果要开发页面, 需要通过`PHP`和`Java`等语言来编写页面代码
      3. 而且通常情况下`HTML`代码和数据以及对应的逻辑会混在一起, 不利于编写和维护

* 前后端分离阶段：
  
   * 随着 `Ajax`的出现, 有了前后端分离的开发模式
   
   * 后端只提供 `API`来返回数据, 前端通过 `Ajax`获取数据, 并且可以通过 `JavaScript`将数据渲染到页面中
   
   * **优点**：
     
      1. 最大的优点就是前后端责任的清晰, 后端专注于数据上, 前端专注于交互和可视化上
      2. 在移动端(`iOS/Android`)等, 后端不需要进行任何处理, 依然使用之前的一套`API`即可

* 单页面富应用阶段(`SPA`)：
  
   * `SPA`最主要的特点就是在前后端分离的基础上加了一层前端路由
   * 由前端来维护一套路由规则

* ==**前端路由的核心：改变 URL，但是页面不进行整体的刷新**==

### 1.前端路由的规则

* `URL`的 `hash`模式：锚点`#`，本质上是改变 `window.location`的 `href`属性
* `HTML5`的 `history`模式：
  * `pushState()`：可以返回之前页面
  
   * `replaceState()`：替换页面，无法返回
  
   * `go()`：
     
      1. `go(-1)`：后退
      2. `go(1)`：前进
  
   * `back()`：返回，等同于 `go(-1)`
  
   * `forward()`：前进，等同于 `go(1)`
* `abstract`：适用于 `Node`
* **`router.push`、`router.replace`、`router.go`的区别**
  1. `router.push`：跳转，并向 history 栈中加一个记录，可以后退到上一个页面
   2. `router.replace`：跳转，不会向 history 栈中加一个记录，不可以后退到上一个页面
   3. `router.go`：传正数向前跳转，传负数向后跳转

### 2.`Vue-router`基础

1. `vue-router`是 `Vue.js`官方的路由插件，它和 `vue.js`是深度集成的，适合用于构建单页面应用。

2. 基于路由和组件：
   
    * 路由用于设定访问路径, 将路径和组件映射起来
    * 在`vue-router`的单页面应用中, 页面的路径的改变就是组件的切换

3. 使用 `vue-router`的步骤:
   
    1. 创建路由组件
    2. 配置路由映射: 组件和路径映射关系
    3. 使用路由: 通过`<router-link>`和`<router-view>`
    
   ```javascript
   // src/router/index.js
   import Vue from 'vue'
   import VueRouter from 'vue-router'
   // 1.注入插件
   Vue.use(VueRouter)
   // 2.定义路由
   const routes = []
   // 3.创建router实例
   const router = new VueRouter({
     routes
   })
   // 4.导出router实例
   export default router
   
   // src/main.js
   import router from './router'
   new Vue({
     el: '#app',
     router,
     render: (h) => h(App)
   })
   ```
   
   ```vue
   // 步骤一：创建路由组件：Aaa.vue, Bbb.vue
   // 步骤二：/src/router/index.js
            import aaa from'../components/aaa.vue'
            import bbb from '../components/bbb.vue'
            const routes = [{
              path: '/aaa',
              component: Aaa
            },
            {
              path: '/bbb',
              component: Bbb
            }]
   // 步骤三：/src/App.js
            <div id="app">
            // <router-link>: 该标签是一个vue-router中已经内置的组件, 它会被渲染成一个<a>标签
            // <router-view>: 该标签会根据当前的路径, 动态渲染出不同的组件
            // 网页的其他内容, 比如顶部的标题/导航, 底部的一些版权信息等会和<router-view>处于同一个等级.
            // 在路由切换时, 切换的是<router-view>挂载的组件, 其他内容不会发生改变.
              <router-link to="/aaa"></router-link>
              <router-link to="/bbb"></router-link>
              <router-view></router-view>
            </div>
   ```

4. 路由的默认路径：重定向
   
   ```javascript
   const routes = [
     {
       path: '/',
       redirect: '/aaa'
     }
   ]
   ```

5. `URL`不显示 `hash`模式的 `#`，改用 `history`模式
   
   ```javascript
   const router = new VueRouter({
     routes,
     mode: 'history'
   })
   ```

6. 通过事件的方式进行跳转：
   
   ```vue
   <button @click="linkToAaa"></button>
   
   methods: {
     linkToAaa() {
       this.$router.push('/Aaa')
       this.$router.replace('/Aaa')
       }
   }
   ```

7. 动态路由：
   
   ```vue
   {
     path: '/bbb/:id',
     component: Bbb
   }
   
   <div>
     <h2>{{ $route.params.id }}</h2>
   </div>
   
   <router-link to="/bbb/123"></router-link>
   ```

8. 嵌套路由：路由路径中父路由通过 `children: []`添加子路由路径
   
   ```js
   children: [
    {
     path: '', // 重定向设置默认子路由
     redirect: 'ccc'
    },
    {
     path: 'ccc', // 子路由路由不需要加
     component: Ccc
    }
   ]
   ```

### 3.路由懒加载

* 路由懒加载的主要作用就是将路由对应的组件打包成一个个的`js`代码块，只有在这个路由被访问到的时候, 才加载对应的组件

  ```js
  const Aaa = () => import('../components/aaa.vue')
  const routes = [
    {
      path: '/aaa',
      component: Aaa
    }
  ]
  ```

  

### 4.传递参数

* 传递参数主要有两种类型: `params`和 `query`
  
   1. `params`的类型:
      
       * 配置路由格式: `/router/:id`
       * 传递的方式: 在 path 后面跟上对应的值
       * 传递后形成的路径: `/router/123`, `/router/abc`
   
   2. `query`的类型:
      
       * 配置路由格式: `/router`, 也就是普通配置
       * 传递的方式: 对象中使用 query 的 key 作为传递方式
       * 传递后形成的路径: `/router?id=123`, `/router?id=abc`
  
  ```vue
  方式一：
  <router-link
    :to="{
      path: '/aaa/' + 123,
      query: { name: 'zhangsan', age: 18 }
    }"
  ></router-link>
  方式二：
    methods: {
      linkTo() {
        this.$router.push(
          {
            path: '/aaa/' + 123,
            query: {name: 'zhangsan', age: 18}
          }
        )
      }
    }
  ```

### 5.获取参数

* 获取参数通过 `$route`对象获取的
  
   * 在使用了 vue-router 的应用中，路由对象会被注入每个组件中，赋值为 this.\$route ，并且当路由切换时，路由对象会被更新
     
     ```vue
     <p>params: {{ $route.params }}</p>
     <p>query: {{ $route.query }}</p>
     页面展示结果：
     params: {"id":"123"}
     query: {"name": "zhangsan", "age": 18}
     ```

### 6.`$route`和 `$router`

* `$router`为`VueRouter`实例，想要导航到不同`URL`，则使用`$router.push`方法
* `$route`为当前`router`跳转对象里面可以获取`name`、`path`、`query`、`params`等

### 7.路由导航守卫

* 什么是导航守卫?
  
   * `vue-router`提供的导航守卫主要用来监听监听路由的进入和离开的
   * `vue-router`提供了`beforeEach`和`afterEach`的钩子函数, 它们会在路由即将改变前和改变后触发

* 导航钩子的三个参数解析:
  
   1. `to`: 即将要进入的目标的路由对象.
   2. `from`: 当前导航即将要离开的路由对象.
   3. `next`: 调用该方法后, 才能进入下一个钩子

* 全局钩子
  
   * `beforeEach`：跳转路由前
     * ==必须要有next()，否则路由无法进行跳转==
     
   * `afterEach`：路由跳转后
   
* 路由独享钩子
  
  ```js
  routes: [
    {
      path: '/xxx',
      component: xxx,
      beforeEnter: (to, from, next) => {}
    }
  ]
  ```

* 组件内路由钩子
  
   1. `beforeRouteEnter(to, from, next)`：跳转路由渲染组件时触发
   2. `beforeRouteUpdate(to, from, next)`：跳转路由且组件被复用时触发
   3. `beforeRouteLeave(to, from, next)`：跳转路由且离开组件时触发

### 8.组件缓存 `keep-alive`

* `include` - 字符串或正则表达式。只有名称匹配的组件会被缓存。

* `exclude` - 字符串或正则表达式。任何名称匹配的组件都不会被缓存。

* `max` - 数字。最多可以缓存多少组件实例。
  
  > `<keep-alive>` 包裹动态组件时，会缓存不活动的组件实例，而不是销毁它们。和 `<transition>` 相似，`<keep-alive>` 是一个抽象组件：它自身不会渲染一个 DOM 元素，也不会出现在组件的父组件链中。
  >
  > 当组件在 `<keep-alive>` 内被切换，==它的 activated 和 deactivated这两个生命周期钩子函数将会被对应执行。==
  
  ```vue
  <keep-alive>
   <router-view>
     <!-- 所有路径匹配的视图组件都会被缓存 -->
    </router-view>
  </keep-alive>
  ```

------

## `Vuex`<a name="vuex"></a>

### 1.认识`Vuex`

- 定义：`Vuex` 是一个专为 `Vue.js` 应用程序开发的状态管理模式
  - 它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化
  - `Vuex`也集成到`Vue`的官方调试工具`devtools extension`，提供了诸如零配置的`time-travel`调试、状态快照导入导出等高级调试功能

### 2.`Vuex`的基本使用

```vue
1.创建
/src/store/index.js
import Vuex from 'vuex'
import Vue from 'vue'

Vue.use.(Vuex)

const store = new Vuex.Store({
	state: {},
	mutations: {}, // 执行同步
	actions: {}, // 相当于用于执行异步的mutations
	getters; {}, // 相当于"全局的"计算属性computed
	module: {} // 用于将store进行模块化管理
})

2.注册
/src/main.js
import Vue from 'vue'
import App from './App'
import store from './store'

new Vue({
	el: '#app',
	store,
	render: h => h(App)
})

3.使用
在vue组件中通过
this.$store获取store对象
this.$store.state获取state对象
this.$store.commit来修改状态(提交给mutations)
this.$store.dispatch来修改状态(提交给actions)

```

### 3.`State`单一状态树

- 用一个对象就包含了全部的应用层级状态
- 单一状态树能够让我们最直接的方式找到某个状态的片段，而且在之后的维护和调试过程中，也可以非常方便的管理和维护

### 4.`Getters`

- `Vuex` 允许我们在 `store` 中定义“getter”（可以认为是 `store` 的计算属性）。就像计算属性一样，`getter` 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算

### 5.`Mutations`

- 更改`Vuex`的`store`中的状态的唯一方法是提交`mutation`

1. `Mutation`主要包括两部分

   - 字符串的事件类型(type)
   - 一个回调函数(handler),该回调函数的第一个参数就是`state`

2. `Mutation`传递参数

   - 在通过`mutation`更新数据的时候, 有可能我们希望携带一些额外的参数
   - 参数被称为是`mutation`的载荷(Payload)

3. `Mutation`响应规则

   - `Vuex`的`store`中的`state`是响应式的, 当`state`中的数据发生改变时, `Vue`组件会自动更新

   - 这就要求我们必须遵守一些`Vuex`对应的规则

     ```vue
     1.提前在store中初始化好所需的属性
     2.当给state中的对象添加新属性时, 使用下面的方式
     	方式一: 使用Vue.set(obj, 'newProp', 123)
     	方式二: 用新对象给旧对象重新赋值(利用展开运算符)
     				state.obj = { ...state.obj, newProp: 123 }
     ```

4. `Mutation`常量类型

   - 使用常量替代`mutation`事件类型在各种`Flux`实现中是很常见的模式。这样可以使`linter`之类的工具发挥作用，同时把这些常量放在单独的文件中可以让你的代码合作者对整个`app`包含的`mutation`一目了然

   - ```js
     1.创建存储常量的文件
     // mutation-types.js
     export const SOME_MUTATION = 'SOME_MUTATION'
     
     2.替换对应的计算属性
     // store.js
     import Vuex from 'vuex'
     import { SOME_MUTATION } from './mutation-types'
     
     const store = new Vuex.Store({
       state: { ... },
       mutations: {
         // 我们可以使用 ES2015 风格的计算属性命名功能来使用一个常量作为函数名
         [SOME_MUTATION] (state) {
           // mutate state
         }
       }
     })
     ```

   - **一条重要的原则就是要记住`mutation`必须是同步函数**

### 6.`Action`

- `Action`类似于`mutation`，不同在于

  - **`Action`提交的是`mutation`，而不是直接变更状态**
  - **`Action`可以包含任意异步操作**

- `Action`函数接受一个与`store`实例具有相同方法和属性的`context`对象，因此你可以调用`context.commit`提交一个`mutation`，或者通过`context.state`和 `context.getters`来获取`state`和`getters`

- `Action`的分发

  ```js
  在Vue组件中, 如果我们调用action中的方法, 那么就需要使用dispatch
  methods: {
    addFn() {
      this.$store.dispatch('addFn')
    }
  }
  
  同样的, 也是支持传递payload
  methods: {
    addFn() {
      this.$store.dispatch('addFn', {count: 5})
    }
  }
  
  // vuex实例中
  state: {
    count: 0
  }
  mutations: {
    addFn(state) {
      state.count++
    }
  }
  actions: {
    addFn(context, payload) {
      setTimeout(() => {
        context.commit('addFn', payload)
      }, 5000)
    }
  }
  ```

- `Action`返回的`Promise`

  ```js
  actions: {
  	addFn(context) {
      return new Promise((resolve) => {
        setTimeout(() => {
          context.commit('addFn')
          resolve()
        }, 1000)
      })
    }
  }
  ```

### 7.`Module`

- 由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，`store`对象就有可能变得相当臃肿

- 为了解决这个问题，`Vuex`允许我们将`store`分割成模块(Module), 而每个模块拥有自己的`state`、`mutations`、`actions`、`getters`等

  ```js
  const moduleA = {
    state: () => ({ ... }),
    mutations: { ... },
    actions: { ... },
    getters: { ... }
  }
  
  const moduleB = {
    state: () => ({ ... }),
    mutations: { ... },
    actions: { ... }
  }
  
  const store = new Vuex.Store({
    modules: {
      a: moduleA,
      b: moduleB
    }
  })
  
  store.state.a // -> moduleA 的状态
  store.state.b // -> moduleB 的状态
  ```

- 模块的局部状态

  - 对于模块内部的 mutation 和 getter，接收的第一个参数是**模块的局部状态对象**

  ```
  const moduleA = {
    state: () => ({
      count: 0
    }),
    mutations: {
      increment(state) {
        // 这里的state对象是模块的局部状态
        state.count++
      }
    },
  
    getters: {
      doubleCount(state) {
        return state.count * 2
      }
    }
  }
  // 注意：虽然increment，doubleCount都是在对象内部定义的，但是调用的时候是直接通过this.$store调用
  ```

- `actions`的写法

  - 局部状态通过`context.state`暴露出来，根节点状态则为`context.rootState`

- 如果`getters`中也需要使用全局的状态, 可以接受更多的参数

  ```js
  const moduleA = {
    getters: {
      rootCount(state, getters, rootState) {
        return state.count + rootState.count
      }
    }
  }
  ```

### 8.项目结构

```sh
├── index.html
├── main.js
├── api
│   └── ... # 抽取出API请求
├── components
│   ├── App.vue
│   └── ...
└── store
    ├── index.js          # 我们组装模块并导出 store 的地方
    ├── actions.js        # 根级别的 action
    ├── mutations.js      # 根级别的 mutation
    └── modules
        ├── cart.js       # 购物车模块
        └── products.js   # 产品模块
```

