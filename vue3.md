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

     

   

   

