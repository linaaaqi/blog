---
title: Vue 2 升级 TypeScript 简单食用方式
date: 2019-12-02T00:00:00
lastMod: 2019-12-02T00:00:00
tags: [教程, Vue, TypeScript]
category: 笔记
summary: ~~Vue 3 都拥抱 TypeScript 了，你说呢？\[狗头]~~
---

## 为什么？

引言：
~~Vue 3 都拥抱 TypeScript 了，你说呢？\[狗头]~~

随着应用的增长，静态类型系统可以帮助防止许多潜在的运行时错误，这就是为什么 Vue 3 是用 TypeScript 编写的。这意味着在 Vue 中使用 TypeScript 不需要任何其他工具——它具有一流的公民支持。

## 如何操作

Vue 3 使用 TypeScript 很简单，CLI 创建项目时选取下即可  
已有Vue2项目不配吗?  
当然不是，Vue 官方通过 ts 的 d.ts 文件申明了Vue 2的静态类型  
也实现了Vue 2使用 TypeScript 的愿望

### 添加 TypeScript 支持

#### 安装 NPM 包
```shell
npm install -D typescript
```

#### 配置 typconfig.json 文件

```json
// tsconfig.json
{
  "compilerOptions": {
    // 与 Vue 的浏览器支持保持一致
    "target": "es5",
    // 这可以对 `this` 上的数据 property 进行更严格的推断
    "strict": true,
    // 如果使用 webpack 2+ 或 rollup，可以利用 tree-shake:
    "module": "es2015",
    "moduleResolution": "node"
  }
}
```

安装工作到此已经完成

### 基本用法

之前直接使用对象定义组件的方式行不通了  
要让 TypeScript 正确推断 Vue 组件选项中的类型  
您需要使用 Vue.component 或 Vue.extend 定义组件

```ts
import Vue from 'vue'
const RightComponent = Vue.extend({
  // 类型推断已启用
})

const ErrorComponent = {
  // 这里不会有类型推断，
  // 因为 TypeScript 不能确认这是 Vue 组件的选项
}
```

### 高级用法

请参见 [VueJS 官方文档](https://cn.vuejs.org/v2/guide/typescript.html#%E5%A2%9E%E5%BC%BA%E7%B1%BB%E5%9E%8B%E4%BB%A5%E9%85%8D%E5%90%88%E6%8F%92%E4%BB%B6%E4%BD%BF%E7%94%A8)


### 基于类的 Vue 组件

如果你说我不喜欢 `Vue.extend` 或 `Vue.component` 定义出来的组件，想使用 Class 定义  
也是可以的

可以使用官方维护的 [vue-class-component](https://github.com/vuejs/vue-class-component) 装饰器:

```ts
import Vue from 'vue'
import Component from 'vue-class-component'

// @Component 修饰符注明了此类为一个 Vue 组件
@Component({
  // 所有的组件选项都可以放在这里
  template: '<button @click="onClick">Click!</button>'
})
export default class MyComponent extends Vue {
  // 初始数据可以直接声明为实例的 property
  message: string = 'Hello!'

  // 组件方法也可以直接声明为实例的方法
  onClick (): void {
    window.alert(this.message)
  }
}
```

#### 与 TSX 搭配使用

既然都用上了 Class 组件，干脆再 "React" 点，来人，上 `TSX`

```tsx
import AnchoredHeading from './AnchoredHeading.vue'

new Vue({
  el: '#demo',
  render: function (h) {
    return (
      <AnchoredHeading level={1}>
        <span>Hello</span> world!
      </AnchoredHeading>
    )
  }
})
```

## 组件定义方式

### Vue2 + JS
#### 方法一：使用对象定义
```js
export default {
  // type inference disabled
}
```
#### 方法二：使用 Composition API 方式定义
此方式可以大部分兼容 Vue3 写法，升级Vue3时只需要修改导入即可
```js
import { defineComponent } from '@vue/composition-api'

export default defineComponent({
  // type inference disabled
})
```

### Vue3 + JS
与 Vue2 + JS 方法二 基本一致，导入差异罢了
```ts
import { defineComponent } from 'vue'

export default defineComponent({
  // type inference disabled
})
```

### Vue2 + TS
#### 方法一: 使用 Vue.extend 定义
基本兼容Vue2对象定义写法

:::danger  
注意 `$ref` 不兼容  
:::

```ts
import { Vue } from 'vue-property-decorator'

export default Vue.extend({
  // type inference enabled
})
```

#### 方法二：使用 Vue.component 定义
同上
```ts
import { Vue } from 'vue-property-decorator'

export default Vue.component({
  // type inference enabled
})
```

#### 方法三
出现了，React "邪教" 成员
```tsx
<template>
  <div>
    ...
  </div>
</template>
<script lang="ts">
import { Vue, Component } from 'vue-property-decorator'

@Component
export default class App extends Vue ({
  // type inference enabled
})
</script>
```

#### 方法四
浓厚味道的 React 写法
```tsx
import { Vue, Component } from 'vue-property-decorator'

@Component
export default class App extends Vue ({
  @Prop({
    type: String,
    required: true
  }) readonly msg!: string
  
  render () {
    return (
      <div>
        <div>
          { this.msg }
        </div>
        <input type="text" v-model={this.msg}/>
      </div>
    )
  }
})
```

## 参考文献

[Vue.js 官方文档](https://cn.vuejs.org/v2/guide/)  
[Vue Class Component 官方文档](https://class-component.vuejs.org/)  
[Vue Composition Api 官方文档](https://github.com/vuejs/composition-api)  