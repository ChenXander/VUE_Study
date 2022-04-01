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

       
