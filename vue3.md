# `Vue3`

## 一.`vue3`基础语法

### 1.创建实例的方式

- ```js
  Vue.createApp(App).mount('#app')
  ```

- `template`模板允许多个根元素(`vue2`只允许一个)

### 2.`v-`系列模板指令

- `v-bind`等指令与`vue2`中大多用法相同

1. 非兼容`vue2`

   - **非兼容**：用于自定义组件时，`prop` 和事件默认名称已更改：

     - prop：`value` -> `modelValue`
     - 事件：`input` -> `update:modelValue`

     ```html
     <!-- v-model新语法 -->
     <ChildComponent v-model="pageTitle" />
     
     <!-- 是以下的简写: -->
     
     <ChildComponent
       :modelValue="pageTitle"
       @update:modelValue="pageTitle = $event"
     />
     ```

     

   - **非兼容**：`v-bind` 的 `.sync` 修饰符和组件的 `model` 选项已移除，可在 `v-model` 上加一个参数代替

     ```html
     <!-- v-model参数 -->
     若需要更改model的名称，现在我们可以为v-model传递一个参数，以作为组件内model选项的替代
     <ChildComponent v-model:title="pageTitle" />
     
     <!-- 是以下的简写: -->
     
     <ChildComponent
       :title="pageTitle"
       @update:title="pageTitle = $event" />
     
     <!-- 也可以作为v-bind.sync修饰符的替代 -->
     <ChildComponent :title.sync="pageTitle" />
     <!-- 替换为 -->
     <ChildComponent v-model:title="pageTitle" />
     ```

     

   - **非兼容**：`v-on`的`.native`修饰符已被移除

     - 新增的`emits`选项允许子组件定义真正会被触发的事件
     - 因此，对于子组件中未被定义为组件触发的所有事件监听器，`Vue`现在将把它们作为原生事件监听器添加到子组件的根元素中 (除非在子组件的选项中设置了`inheritAttrs: false`)

     ```html
     <my-component
       v-on:close="handleComponentEvent"
       v-on:click="handleNativeClickEvent"
     />
     
     MyComponent.vue中
     <script>
       export default {
         emits: ['close']
       }
     </script>
     ```

   

   - **非兼容**： 在`vue3`中，如果一个元素同时定义了`v-bind="object"`和一个相同的独立 `attribute`，那么绑定的声明顺序将决定它们如何被合并。换句话说，相对于假设开发者总是希望独立 attribute 覆盖`object`中定义的内容，现在开发者能够对自己所希望的合并行为做更好的控制

     ```html
     <!-- 模板 -->
     <div id="red" v-bind="{ id: 'blue' }"></div>
     <!-- 结果 -->
     <div id="blue"></div>
     
     <!-- 模板 -->
     <div v-bind="{ id: 'blue' }" id="red"></div>
     <!-- 结果 -->
     <div id="red"></div>
     ```

   

   - **新增**：现在可以在同一个组件上使用多个`v-model`绑定

     ```html
     允许在自定义组件上使用多个v-model
     <ChildComponent v-model:title="pageTitle" v-model:content="pageContent" />
     <!-- 是以下的简写: -->
     <ChildComponent
       :title="pageTitle"
       @update:title="pageTitle = $event"
       :content="pageContent"
       @update:content="pageContent = $event"
     />
     ```

     

   - **新增**：现在可以自定义`v-model`修饰符

     ```html
     <!-- v-model现在还支持自定义修饰符 -->
     <ChildComponent v-model.capitalize="pageTitle" />
     ```
     
   
2. **条件渲染**

   - **非兼容**：v-if和v-for的优先级更改

     - 两者作用于同一个元素上时，`v-if` 会拥有比 `v-for` 更高的优先级

   - **非兼容**：`<template v-for>` 的 `key` 应该设置在 `<template>` 标签上 (而不是设置在它的子节点上)

   - **非兼容**：`v-for`中的ref不再注册`ref`数组

     > 在 `Vue 2` 中，在 `v-for` 中使用的 `ref` attribute 会用 ref 数组填充相应的 `$refs` property。当存在嵌套的 `v-for` 时，这种行为会变得不明确且效率低下

     - 在 `Vue 3` 中，此类用法将不再自动创建 `$ref` 数组。要从单个绑定获取多个 ref，请将 `ref` 绑定到一个更灵活的函数上 (这是一个新特性)

       ```html
       <div v-for="item in list" :ref="setItemRef"></div>
       ```

       

   - **新增**：对于 `v-if`/`v-else`/`v-else-if` 的各分支项 `key` 将不再是必须的，因为现在 `Vue` 会自动生成唯一的 `key`

     - **非兼容**：如果你手动提供 `key`，那么每个分支必须使用唯一的 `key`。你将不再能通过故意使用相同的 `key` 来强制重用分支

## 二.组件

### 1.组件间通信

1. 非`Prop`的`Attribute`

   - 当我们传递给一个组件某个属性，但是该属性并没有定义对应的`props`或者`emits`时，就称之为非`Prop`的`Attribute`
   - 常见的包括class、style、id属性等

2. `Attribute`继承

   - 当组件有单个根节点时，非`Prop`的`Attribute`将自动添加到根节点的`Attribute`中

     ```html
     <!-- 子组件 -->
     <template>
       <div></div><!-- 渲染后自动添加class="child" -->
     </template>
     
     <!-- 父组件 -->
     <template>
       <div>
         <child-noprop class="child"></child-noprop>
       </div>
     </template>
     ```

   - 如果我们不希望组件的根元素继承`attribute`，可以在组件中设置 `inheritAttrs: false`

     - 禁用`attribute`继承的常见情况是需要将`attribute`应用于根元素之外的其他元素
     - 可以通过`$attrs`来访问所有的 非`props`的`attribute`

     ```html
     <!-- 子组件 -->
     <template>
       <div>
       	<h2 :class="$attrs.class"></h2>
       </div>
     </template>
     
     export default {
     	inheritAttrs: false,
       data () {}
     }
     ```

   - **(`vue3`)多个根节点的`attribute`**
   
     - 多个根节点的`attribute`如果没有显示的绑定，那么会报警告，我们必须手动的指定要绑定到哪一个属性上
   
       ```html
       <!-- 子组件 -->
       <template>
         <!-- 指定哪个根节点继承attribute -->
         <div :class="$attrs.class">根节点1</div>
         <div>根节点2</div>
         <div>根节点3</div>
       </template>
       ```
   
3. 子传父

   - 自定义事件

     ```vue
     <!-- 子组件 -->
     <template>
       <div>
       	<button @click="addA"></button>
         <button @click="addB"></button>
       </div>
     </template>
     export default {
       // 数组写法
     	emits:['add1','add2'],
     	// 对象写法目的是为了参数验证
     	emits: {
     		add1: null,
     		add2: null,
     		addN: (num, name, age) => {
     			if (num > 10) {
     				return true
     			}
     			return false
     		}
     	}
       methods: {
     		addA	() {
     			this.$emit('add1')
     		},
     		addB	() {
     			this.$emit('add2')
     		}
     	}
     }
     
     <!-- 父组件 -->
     <child @add1="add1Fn" @add2="add2Fn"></child>
     
     methods: {
     	add1Fn () {},
     	add2Fn () {}
     }
     ```

4. 非父子组件通信

   - `Provide`和 `Inject`

     ```js
     // 父组件
     export default {
       provide () {
         return {
           name: 'zhangsan',
           age: 18,
           length: this.names.length // 非响应式
           length: computed(() => this.names.length) // 响应式
         }  
       }
     }
     
     // 子组件
     {{name}}-{{age}}-{{lentgh}}
     // 如果使用computed来实现响应式，因为computed返回的是一个ref对象,需要取出其中的value来使用
     {{length.value}}
     inject:['name', 'age', 'length'] // 接收父组件的数据
     ```

   - 全局事件总线`mitt`库

     - `vue3`中移除了`$on`，`$off`，`$once`方法，如要继续使用全局事件总线，则需要借助第三方库`mitt`或`tiny-emitter`

     - `npm install mitt`安装`mitt`库

       ```js
       // 封装一个工具eventbus.js
       import mitt from 'mitt'
       const emitter = mitt()
       // export const emitter1 = mitt() 可以创建多个
       // export const emitter2 = mitt()
       export default emitter
       
       
       import emitter from './eventBus'
       export default {
         addFn() {
           // 发送事件
           emitter.emit('shangsan', { name: 'shangsan', age: 18 })
         },
       }
       
       import emitter from './eventBus'
       export default {
         created() {
           // 接收事件
           emitter.on('shangsan', (info) => {
             console.log('zhangsan event', info)
           }),
           // 一次监听全部事件，type为事件类型
           emitter.on('*', (type, info) => {
             console.log('* event', type, info)
           })
         },
       }
       ```

     - `mitt`事件的取消

       ```js
       // 取消emitter中的所有监听
       emitter.all.clear()
       // 或者定义一个函数
       function onFoo() {}
       emitter.on('foo', onFoo) // 监听
       emitter.off('foo', onFoo) // 取消监听
       ```


### 2.插槽

1. 具名插槽

   - 动态插槽名

     - 可以通过`v-slot:[dynamicSlotName]`方式动态绑定一个名称

     ```html
     <template v-slot:[name]></template>
     
     data () {
     	return {
     		name: 'left'
     	}
     }
     ```

   - 缩写

     - `v-slot:center`缩写成`#left`

2. 独占默认插槽`v-slot:default="slotProps"`可以简写为`v-slot="slotProps"`

   - 并且如果我们的插槽只有默认插槽时，组件的标签可以被当做插槽的模板来使用，这样我们就可以将`v-slot`直 接用在组件上

3. 多个插槽混用

   - 按照完整的`template`来编写

     ```html
     <child :names="names">
       <template v-slot="slotProps">
         <span>{{ slotProps.item }}</span>
       </template>
       <template v-slot:left>
         <h2>hello</h2>
       </template>
     </child>
     ```

### 3.动态组件

1. 动态组件是使用component 组件，通过一个特殊的`attribute`： `is`来实现

   ```vue
   <component :is="currentTab"></component>
   currentTab的值可以是：
   	1.通过component函数注册的组件
   	2.在一个组件对象的components对象中注册的组件
   ```

2. 动态组件的传值和监听事件

   - 将属性和监听事件放到`component`上来使用

     ```vue
     <component name="zhangsan"
                :age="18"
                @pageClick="pageClick"
                :is="currentTab" />
     ```

3. `keep-alive`

   - include= `"string | RegExp | Array"`。只有名称匹配的组件会被缓

     存

   - exclude="`string | RegExp | Array`"。任何名称匹配的组件都不

     会被缓存

   - max=`"number | string"`。最多可以缓存多少组件实例，一旦达

     到这个数字，那么缓存组件中最近没有被访问的实例会被销毁

### 4.新的组件

1. `Fragment`

   - 在`Vue2`中：组件必须有一个根标签
   - 在`Vue3`中：组件可以没有根标签, 内部会将多个标签包含在一个`Fragment`虚拟元素中
   - **好处：减少标签层级, 减小内存占用**

2. `Teleport`

   - `Teleport` 是一种能够将我们的组件`html`结构移动到指定位置的技术

     ```vue
     <!-- 有遮罩层的弹窗，移动到body，不影响原来的父元素 -->
     <teleport to="移动位置：body">
       <div v-if="isShow" class="mask">
         <div class="dialog">
           <h3>我是一个弹窗</h3>
           <button @click="isShow = false">关闭弹窗</button>
         </div>
       </div>
     </teleport>
     ```

3. `Suspense`

   - 等待异步组件时渲染一些额外内容，让应用有更好的用户体验

   - 使用步骤

     ```js
     // 异步引入组件
     import {defineAsyncComponent} from 'vue'
     const Child = defineAsyncComponent(()=>import('./components/Child.vue'))
     ```

     ```vue
     <!-- 使用Suspense包裹组件，并配置好default与fallback -->
     <template>
     	<div class="app">
     		<h3>我是App组件</h3>
     		<Suspense>
     			<template v-slot:default>
     				<Child/>
     			</template>
     			<template v-slot:fallback>
     				<h3>加载中.....</h3>
     			</template>
     		</Suspense>
     	</div>
     </template>
     ```

     

## 三.`Composition API`

1. `setup`

   - `Vue3.0`中一个新的配置项，值为一个函数
   - `setup`函数的两种返回值
     1. **若返回一个对象，则对象中的属性、方法, 在模板中均可以直接使用**
     2. 若返回一个渲染函数，则可以自定义渲染内容
   - 注意点：
     1. 尽量不要与`Vue2.x`配置混用
        - `Vue2.x`配置（data、methos、computed...）中可以访问到`setup`中的属性、方法
        - 但在`setup`中**不能访问**到`Vue2.x`配置（`data、methos、computed...`）
        - 如果有重名, `setup`优先
     2. `setup`不能是一个`async`函数，因为返回值不再是`return`的对象, 而是`promise`, 模板看不到`return`对象中的属性。（后期也可以返回一个`Promise`实例，但需要`Suspense`和异步组件的配合）
     3. **`setup`执行的时机**
        - **在`beforeCreate`之前执行一次，`this`是`undefined`**
     4. `setup`的参数
        - `props`：值为对象，包含：组件外部传递过来，且组件内部声明接收了的属性
        - `context`：上下文对象
          1. `attrs`：值为对象，包含：组件外部传递过来，但没有在props配置中声明的属性, 相当于`this.$attrs`
          2. `slots`：收到的插槽内容, 相当于`this.$slots`
          3. `emit`：分发自定义事件的函数, 相当于`this.$emit`

2. `ref`函数

   - 作用:：定义一个响应式的数据
   - 语法： ```const xxx = ref(initValue)``` 
     - 创建一个包含响应式数据的引用对象（`reference`对象，简称ref对象）
     - `JS`中操作数据：`xxx.value`
     - 模板中读取数据: 不需要`.value`，直接：`<div>{{xxx}}</div>`
   - 备注：
     - 接收的数据可以是：基本类型、也可以是对象类型
     - 基本类型的数据：响应式依然是靠``Object.defineProperty()``的```get```与```set```完成的
     - 对象类型的数据：内部求助了`Vue3.0`中的一个新函数`reactive`函数

3. `reactive`函数

   - 作用: 定义一个**对象类型**的响应式数据（基本类型不要用它，要用```ref```函数）
   - 语法：```const 代理对象= reactive(源对象)```接收一个对象（或数组），返回一个代理对象（Proxy的实例对象，简称proxy对象）
   - reactive定义的响应式数据是“深层次的”
   - 内部基于`ES6`的`Proxy`实现，通过代理对象操作源对象内部数据进行操作

4. `reactive`对比`ref`

   - 从定义数据角度对比：
     - `ref`用来定义：**基本类型数据**
     - `reactive`用来定义：**对象（或数组）类型数据**
     - **备注：`ref`也可以用来定义对象（或数组）类型数据, 它内部会自动通过```reactive```转为代理对象**
   - 从原理角度对比：
     - **`ref`通过``Object.defineProperty()``的```get```与```set```来实现响应式（数据劫持）**
     - **`reactive`通过使用`Proxy`来实现响应式（数据劫持）, 并通过`Reflect`操作源对象内部的数据**
   - 从使用角度对比：
     - `ref`定义的数据：**操作数据需要`.value`，读取数据时模板中直接读取不需要`.value`**
     - `reactive`定义的数据：操作数据与读取数据：**均不需要**`.value`

5. 计算属性

   - `computed`函数

     - 与`Vue2.x`中`computed`配置功能一致

       ```js
       import {computed} from 'vue'
       
       setup(){
         ...
         //计算属性——简写
         let fullName = computed(()=>{
           return person.firstName + '-' + person.lastName
         })
         //计算属性——完整
         let fullName = computed({
           get(){
             return person.firstName + '-' + person.lastName
           },
           set(value){
             const nameArr = value.split('-')
             person.firstName = nameArr[0]
             person.lastName = nameArr[1]
           }
         })
       }
       ```

6. `watch`函数

   - 与`Vue2.x`中`watch`配置功能一致

   - **注意点**：

     - 监视`reactive`定义的响应式数据时：`oldValue`无法正确获取、强制开启了深度监视（`deep`配置失效）
     - 监视`reactive`定义的响应式数据中某个属性时：`deep`配置有效

     ```js
     //情况一：监视ref定义的响应式数据
     watch(sum,(newValue,oldValue)=>{
       console.log('sum变化了',newValue,oldValue)
     },{immediate:true})
     
     //情况二：监视多个ref定义的响应式数据
     watch([sum,msg],(newValue,oldValue)=>{
       console.log('sum或msg变化了',newValue,oldValue)
     }) 
     
     /* 情况三：监视reactive定义的响应式数据
     			若watch监视的是reactive定义的响应式数据，则无法正确获得oldValue！！
     			若watch监视的是reactive定义的响应式数据，则强制开启了深度监视 
     */
     watch(person,(newValue,oldValue)=>{
       console.log('person变化了',newValue,oldValue)
     },{immediate:true,deep:false}) //此处的deep配置不再奏效
     
     //情况四：监视reactive定义的响应式数据中的某个属性
     watch(()=>person.job,(newValue,oldValue)=>{
       console.log('person的job变化了',newValue,oldValue)
     },{immediate:true,deep:true}) 
     
     //情况五：监视reactive定义的响应式数据中的某些属性
     watch([()=>person.job,()=>person.name],(newValue,oldValue)=>{
       console.log('person的job变化了',newValue,oldValue)
     },{immediate:true,deep:true})
     
     //特殊情况
     watch(()=>person.job,(newValue,oldValue)=>{
       console.log('person的job变化了',newValue,oldValue)
     },{deep:true}) //此处由于监视的是reactive素定义的对象中的某个属性，所以deep配置有效
     ```

7. `watchEffect`函数

   - `watch`是：既要指明监视的属性，也要指明监视的回调

   - `watchEffect`是：不用指明监视哪个属性，监视的回调中用到哪个属性，那就监视哪个属性

   - `watchEffect`有点像`computed`

     - 但`computed`注重的计算出来的值（回调函数的返回值），所以必须要写返回值
     - 而`watchEffect`更注重的是过程（回调函数的函数体），所以**不用写返回值**

     ```js
     //watchEffect所指定的回调中用到的数据只要发生变化，则直接重新执行回调。
     watchEffect(()=>{
       const x1 = sum.value
       const x2 = person.age
       console.log('watchEffect配置的回调执行了')
     })
     ```

8. 自定义`hook`函数

   - `hook`本质是一个函数，把`setup`函数中使用的`Composition API`进行了封装
   - 类似于`vue2.x`中的`mixin`
   - 自定义`hook`的优势: **复用代码**, **让`setup`中的逻辑更清楚易懂**

9. `toRef`

   - 作用：创建一个`ref`对象，其`value`值指向另一个对象中的某个属性
   - 语法：`const name = toRef(person,'name')`
   - 应用:   **要将响应式对象中的某个属性单独提供给外部使用时**
   - 扩展：```toRefs``` 与```toRef```功能一致，但可以批量创建多个`ref`对象，语法：`toRefs(person)`

## 四.其他`Composition API`

1. `shallowReactive` 与 `shallowRef`

   - `shallowReactive`：**只处理对象最外层属性的响应式**（浅响应式）
   - `shallowRef`：**只处理基本数据类型的响应式**, 不进行对象的响应式处理
   - 什么时候使用
     - 如果有一个对象数据，结构比较深, 但变化时只是外层属性变化： `shallowReactive`
     - 如果有一个对象数据，后续功能不会修改该对象中的属性，而是生成新的对象来替换：`shallowRef`

2. `readonly` 与 `shallowReadonly`

   - `readonly`：让一个响应式数据变为只读的（深只读）
   - `shallowReadonly`：让一个响应式数据变为只读的（浅只读）
   - 应用场景: 不希望数据被修改时

3. `toRaw` 与 `markRaw`

   - `toRaw`
     - 作用：将一个由```reactive```生成的**响应式对象**转为**普通对象**
     - 使用场景：用于读取响应式对象对应的普通对象，对这个普通对象的所有操作，不会引起页面更新
   - `markRaw`
     - 作用：标记一个对象，使其永远不会再成为响应式对象
     - 应用场景：
       1. 有些值不应被设置为响应式的，例如复杂的第三方类库等
       2. 当渲染具有不可变数据源的大列表时，**跳过响应式转换可以提高性能**

4. `customRef`

   - 作用：创建一个自定义的`ref`，并对其依赖项跟踪和更新触发进行显式控制

   - 实现防抖效果

     ```vue
     <template>
     	<input type="text" v-model="keyword">
     	<h3>{{keyword}}</h3>
     </template>
     
     <script>
     	import {ref,customRef} from 'vue'
     	export default {
     		name:'Demo',
     		setup(){
     			// let keyword = ref('hello') //使用Vue准备好的内置ref
     			//自定义一个myRef
     			function myRef(value,delay){
     				let timer
     				//通过customRef去实现自定义
     				return customRef((track,trigger)=>{
     					return{
     						get(){
     							track() //告诉Vue这个value值是需要被“追踪”的
     							return value
     						},
     						set(newValue){
     							clearTimeout(timer)
     							timer = setTimeout(()=>{
     								value = newValue
     								trigger() //告诉Vue去更新界面
     							},delay)
     						}
     					}
     				})
     			}
     			let keyword = myRef('hello',500) //使用程序员自定义的ref
     			return {
     				keyword
     			}
     		}
     	}
     </script>
     ```

5. `provide` 与 `inject`

   - 作用：实现祖与后代组件间通信

   - 父组件有一个 `provide` 选项来提供数据，后代组件有一个 `inject` 选项来开始使用这些数据

   - 具体写法：

     ```js
     // 祖组件
     setup(){
       ......
       let person = reactive({name:'张三',age:18})
       provide('person',person)
       ......
     }
       
     // 后代组件
     setup(props,context){
       ......
       const person = inject('person')
       return {person}
       ......
     }
     ```

6. 响应式数据的判断

   - `isRef`：检查一个值是否为一个`ref`对象
   - `isReactive`：检查一个对象是否是由`reactive`创建的响应式代理
   - `isReadonly`：检查一个对象是否是由`readonly`创建的只读代理
   - `isProxy`：检查一个对象是否是由`reactive`或者`readonly`方法创建的代理

## 五.`Vue3.0`中的响应式原理

1. `vue2.x`的响应式

   - 实现原理：

     - 对象类型：通过```Object.defineProperty()```对属性的读取、修改进行拦截（数据劫持）

     - 数组类型：通过重写更新数组的一系列方法来实现拦截。（对数组的变更方法进行了包裹）

       ```js
       Object.defineProperty(data, 'count', {
           get () {}, 
           set () {}
       })
       ```

   - 存在问题：

     - **新增属性、删除属性, 界面不会更新**
     - **直接通过下标修改数组, 界面不会自动更新**

2. `Vue3.0`的响应式

   - 实现原理：

     - 通过`Proxy`（代理）:  拦截对象中任意属性的变化, 包括：属性值的读写、属性的添加、属性的删除等

     - 通过`Reflect`（反射）:  对源对象的属性进行操作

       ```js
       new Proxy(data, {
       	// 拦截读取属性值
         get (target, prop) {
           return Reflect.get(target, prop)
         },
         // 拦截设置属性值或添加新属性
         set (target, prop, value) {
           return Reflect.set(target, prop, value)
         },
         // 拦截删除属性
         deleteProperty (target, prop) {
           return Reflect.deleteProperty(target, prop)
         }
       })
       
       proxy.name = 'tom'   
       ```

## 六.生命周期

1. `Vue3.0`中可以继续使用`Vue2.x`中的生命周期钩子，但有两个被更名
   - `beforeDestroy`改名为`beforeUnmount`
   - `destroyed`改名为`unmounted`
2. `Vue3.0`也提供了`Composition API`形式的生命周期钩子，与`Vue2.x`中钩子对应关系如下
   - `beforeCreate` ===> `setup()`
   - `created`           ===> `setup()`
   - `beforeMount`   ===> `onBeforeMount`
   - `mounted`           ===> `onMounted`
   - `beforeUpdate` ===> `onBeforeUpdate`
   - `updated`           ===> `onUpdated`
   - `beforeUnmount` ===> `onBeforeUnmount`
   - `unmounted`         ===> `onUnmounted`
3. 如果两种钩子同时存在，`vue3.0`的`Composition API`会优先执行

## 七.`Composition API`的优势

1. `Options API`存在的问题
   - 使用传统`OptionsAPI`中，新增或者修改一个需求，就需要分别在`data`，`methods`，`computed`里修改
2. `Composition API` 的优势
   - 我们可以更加优雅的组织我们的代码，函数。让相关功能的代码更加有序的组织在一起

## 八.全局`API`的转移

1. `Vue 2.x`有许多全局`API`和配置

   - 例如：注册全局组件、注册全局指令等

     ```js
     //注册全局组件
     Vue.component('MyButton', {
       data: () => ({
         count: 0
       }),
       template: '<button @click="count++">Clicked {{ count }} times.</button>'
     })
     
     //注册全局指令
     Vue.directive('focus', {
       inserted: el => el.focus()
     }
     ```

2. `Vue3.0`中对这些`API`做出了调整

   - 将全局的`API`，即：`Vue.xxx`调整到应用实例（`app`）上

     | `2.x` 全局 `API`（`Vue`） | `3.x` 实例 `API` (`app`)      |
     | ------------------------- | ----------------------------- |
     | `Vue.config.xxxx`         | `app.config.xxxx`             |
     | Vue.config.productionTip  | **移除**                      |
     | `Vue.component`           | `app.component`               |
     | `Vue.directive`           | `app.directive`               |
     | `Vue.mixin`               | `app.mixin`                   |
     | `Vue.use`                 | `app.use`                     |
     | `Vue.prototype`           | `app.config.globalProperties` |

     
