## 基础语法

### 1.计算属性：

- `computed`都包含一个`getter`和一个`setter`，`setter`一般不用，只用`getter`进行读取。
- 计算属性会进行缓存，如果多次使用时，计算属性只会调用一次，而`methods`会多次调用，因此计算属性更加高效。

### 2.`v-on`参数：

1. 情况一：

   - 如果该方法不需要额外参数，那么方法后的()可以不添加。

   - 但是注意：如果方法本身中有一个参数或没有参数，那么会默认将原生事件`event`参数传递进去

2. 情况二：

   - 如果需要同时传入某个参数，同时需要`event`时，可以通过`$event`传入事件

### 3.`v-on`修饰符：

1.  `.stop`--调用`event.stopPropagation()`。阻止事件冒泡
2.  `.prevent`--调用`event.preventDefault()`。阻止默认事件
3.  `.{keyCode|keyAlias}`--只当事件是从特定键触发时才触发回调
4.  `.once`--只触发一次回调

### 4.`v-if`, `v-else-if`, `v-else`：

-  `input`输入框--Vue在进行DOM渲染时，出于性能考虑，会尽可能的复用已经存在的元素，而不是重新创建新的元素。
-  解决方案：给对应的`input`添加key，并且key不相同

### 5.`v-show`：

1.  `v-if`当条件为`false`时，不会有对应的元素在DOM中
2. `v-show`当条件为`false`时，是将元素的`display`属性设置为`none`
3.  **如何选择**：
   - 当需要在显示与隐藏之间切片很频繁时，使用`v-show`
   - 当只有一次切换时，通过使用`v-if`

### 6.`v-for`：

- 使用时给对应的元素或组件添加 `:key` 属性，来给每个节点做唯一标识，方便虚拟DOM的Diff算法正确识别节点。
- key的作用是为了高效的更新虚拟DOM

### 7.检测数组更新：

- Vue是响应式的，所以当数据发生变化时，Vue会自动检测数据变化，视图会发生对应的更新
- Vue中包含了一组观察数组编译的方法，使用它们改变数组也会触发视图的更新
- 但并不是所有的方法都是是响应式的

#### 常用的响应式的数组方法：

1.  `push()`：数组最后添加元素，可以添加多个，`push('a', 'b', 'c')`

2.  `pop()`：删除数组最后一个元素

3.  `shift()`：删除数组第一个元素

4.  `unshift()`：在数组最前面添加元素，可以添加多个，`unshift('a', 'b', 'c')`

5.  `splice()`：删除元素/插入元素/替换元素

   ```
   splice(start, count, 'a', 'b', 'c')： 第一个参数截取数组的索引起点
   																			第二个参数截取数组元素的个数
   																			第三个参数要添加的元素
   ```

6.  `sort()`：数组排序

7.  `reverse()`：逆序数组

8.  vue提供的修改方法：

   ```
   Vue.set(arr, index, 'a')，第一个参数要修改的对象
   													第二个参数索引值
   													第三个参数修改后的值
   ```

9. **注意：通过索引值直接修改数组不是响应式的**

### 8.`v-model`：

- 原理：`v-model`其实是一个语法糖，它的背后本质上是包含两个操作：

  ```
  v-bind绑定一个value属性
  v-on指令给当前元素绑定input事件
  <input type="text" v-model="message">
  等同于
  <input type="text" v-bind:value="message" v-on:input="message = $event.target.value">
  ```

- `v-model`中`radio`(单选框的应用)：通过`v-model`绑定相同变量数据，实现单选框的互斥以及双向数据绑定，原来的单选框`name`属性就可以去掉了

- `v-model`中`checkbox`(多选框的应用)：

  ```
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

- `input`框中的值绑定：在`label`中进行for循环遍历，通过`:value`、`:id`等对`input`进行值绑定，其实就是`v-bind`在`input`框中的应用

- `v-model`修饰符：

  ```
  lazy修饰符：v-model.lazy让数据在失去焦点或者回车时才会更新
  number修饰符：v-model.number让在输入框中输入的内容自动转成数字类型
  trim修饰符：v-model.trim过滤内容左右两边的空格
  ```

------

## Vue组件化

### 1.组件的使用分成三个步骤：(底层写法)

```
1.创建组件构造器：const cpnC = Vue.extend({
								template: `
										<div>
                        <h2>xxxx</h2>
                        <p>123456</p>
                   	</div>
                `
             })
2.注册组件：Vue.component('test', cpnC) 全局注册
3.使用组件：<test /> 注意：必须挂载到根组件内
```

### 2.父组件和子组件：

- 组件和组件之间存在层级关系，而其中一种非常重要的关系就是父子组件的关系
- 注意：子组件注册到父组件的`components`时，Vue会编译好父组件的模块，父组件已经有了子组件的内容。子组件出口`<child />`是只能被父组件识别，并且被浏览器忽略，不能在Vue实例中和父组件出口写在一起

### 3.注册组件语法糖：

```
(全局注册)：Vue.component('test', {
							template: `
              		<div>
                  		<h2>xxxx</h2>
                  		<p>545446</p>
                  		<p>45545</p>
                  </div>
              `
          })
(局部注册)：components: {
							'test': template: `
                          <div>
                              <h2>xxxx</h2>
                              <p>545446</p>
                          		<p>45545</p>
              						</div>
              				`
          }
进一步简化，抽离template
					<template id="cpn">
							<div><p>模板的分离</p></div>
					</template>
					
					在组件实例中components: {
												'test': {
														template: '#cpn'
												}
										}
```

### 4.组件的数据访问：

- 组件中不能直接访问Vue实例中的`data`，Vue组件有自己保存数据的地方：

  ```
  data () {		data属性必须是函数，并且返回一个对象保存着数据！！！
  		return {}
  }
  原因：首先如果data不是函数是对象，Vue会直接报错。
  		其次，所有组件共用一个data对象地址，组件多次使用会出现数据互相干扰！！！
  		使用函数并返回对象，Vue能让每个组件对象都返回一个新的对象，每个组件数据则相互隔离
  ```

### 5.父子组件的通信：

1.  父组件通过`props`向子组件传递数据：

   - 父组件中：`<child :变量名="data数据" />`

   - 子组件中：使用选项`props`来声明需要从父级接收到的数据，`props`有两种方式

     ```
     方式一：字符串数组，数组中的字符串就是传递时的名称。
     			props: ['父组件中传来的变量名']
     			使用：<div>传来的数据：{{变量名}}</div>
     			注意：如果子组件中props的变量名使用了驼峰写法，则父组件中要以-进行分割使用！！！
     					即：props: {mySchool: 类型} -> <child :my-school="data数据" />
     方式二：对象，对象可以设置传递时的类型，也可以设置默认值等。
     			props: {
     					变量名: String (单个),
     					变量名: [String,Number,Boolean,Array,Object,Date,Function,Symbol](多个)
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

     

2.  子组件通过事件`$emit` `Events`向父组件发送消息

   1. 子组件中定义触发的函数，在这个函数中通过`this.$emit(‘自定义事件名’, 要传递的数据参数item)`

   2. 父组件中通过`@自定义事件名=“定义响应的函数(item)”`，然后在`methods`中通过响应的函数接收参数并进行具体操作

      ```
      子组件中：<button @click="btnClick(item)"></button>
      				methods: {
      						btnClick (item) {
      								this.$emit('btn-event', item)
      						}
      				}
      父组件中：<child @btn-event="btnResponse" /> 因为是自定义事件，所以不受vue中默认传参event的影响，这里可以省略参数。vue-cli中可以使用驼峰写法
      				methods: {
      						btnResponse (item) {
      								具体操作console.log('btn-event', item)
      						}
      				}
      ```

### 6.父组件直接访问子组件：

- 方法一：`$children`通过索引值拿到子组件，但是当子组件过多其索引值不易确定，不推荐使用
- 方法二：`$refs`给子组件绑定`ref`值`<child ref="child1">`，`$refs.child1`就能拿到指定子组件对象，类似于标签中的`id`属性。此方法不受子组件数量影响，推荐使用

### 7.非父子组件通信：

- 方案一：事件总线，较为繁琐
- 方案二：<a href="#vuex">vuex</a>状态管理，推荐使用

### 8.插槽`slot`：

- 具名插槽：

  ```
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

- 作用域插槽：父组件替换插槽的标签，但是内容由子组件提供

  ```
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

------

## 前端模块化

- `CommonJS`

  ```
  导出module.exports = { test: a }
  导入let { test } = require('moduleA')
  ```

- `ES6`模块化

  ``` fr
  导出：
  方案一：export function test () {}; export class Person {}
  方案二：export test () {}; export Person {};
  			export {test, Person}
  方案三：(test.js) export default function () {}
  			import 自定义命名test1 from './test.js'
  			test1()
  			注意：export default在同一个模块中，不允许同时存在多个
  
  导入：
  1.<script src="./test.js" type="module"></script>
  2.import {} from './test.js'
  3.如果我们希望某个模块中所有的信息都导入
  	通过*可以导入模块中所有的export变量
  	但是通常情况下我们需要给*起一个别名，方便后续的使用
  	import * as 别名info from './test.js'
  	console.log(info.xxx, info.)
  ```


## `Vue-router`

- 后端路由阶段：页面需要请求不同的路径内容时, 交给服务器来进行处理, 服务器渲染好整个页面, 并且将页面返回给客户端。
  - 这种情况下渲染好的页面, 不需要单独加载任何的`js`和`css`, 可以直接交给浏览器展示, 这样也**有利于`SEO`的优化**
  - 缺点：
    1. 整个页面的模块由后端人员来编写和维护的
    2. 前端开发人员如果要开发页面, 需要通过`PHP`和`Java`等语言来编写页面代码
    3. 而且通常情况下`HTML`代码和数据以及对应的逻辑会混在一起, 不利于编写和维护
- 前后端分离阶段：
  - 随着`Ajax`的出现, 有了前后端分离的开发模式
  - 后端只提供`API`来返回数据, 前端通过`Ajax`获取数据, 并且可以通过`JavaScript`将数据渲染到页面中
  - **优点**：
    1. 最大的优点就是前后端责任的清晰, 后端专注于数据上, 前端专注于交互和可视化上
    2. 在移动端(`iOS/Android`)等, 后端不需要进行任何处理, 依然使用之前的一套`API`即可
- 单页面富应用阶段(`SPA`)：
  - `SPA`最主要的特点就是在前后端分离的基础上加了一层前端路由
  - 由前端来维护一套路由规则
- **前端路由的核心：****改变URL，但是页面不进行整体的刷新**

### 1.前端路由的规则：

- `URL`的`hash`模式：锚点**`#`**，本质上是改变`window.location`的`href`属性
- `HTML5`的`history`模式：
  - `pushState()`：可以返回之前页面
  - `replaceState()`：替换页面，无法返回
  - `go()`：
    1. `go(-1)`：后退；
    2. `go(1)`：前进
  - `back()`：返回
  - `forward()`：前进

### 2.`Vue-router`基础：
