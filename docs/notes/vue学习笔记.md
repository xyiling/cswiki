# vue学习笔记

## 一、vue基础
### 1. 基础语法
```vue
<template>
    <div>
        // 访问变量，不需要通过value属性访问
        <h1>{{ prop1 }}</h1>
        <p>{{ prop2 }}</p>
    </div>
</template>
<script setup lang="ts">
// 变量
let prop1 = ref('value1')
let prop2 = ref('value2')
// 修改变量(通过value属性修改)
prop1.value = 'new value1'
// 生命周期
OnMounted(() => {
    console.log('component mounted')
})
// 通过h函数创建元素
h('h1', {}, 'hello vue')
// 渲染元素到页面
mount(h('h1', {}, 'hello vue'), document.body)
</script>
```
### 2. 组件
### 3. 缓存
pinia缓存
localStorage缓存
### 4. 生命周期

## 二、通信机制
### 1. 父子组件通信
1.1 `props`单向数据流
1.2 `model`双向绑定
1.3 `provide`/`inject` 注入
### 2. 兄弟组件通信
## 三、事件处理
### 1. 事件追踪
## 四、自定义指令
## 五、性能优化
### 1. 首屏加载
### 2. 懒加载
### 3. 缓存
### 4. 压缩

### 5. 骨架屏
可以直接使用ui框架提供的骨架屏组件，如el-skeleton，antd的skeleton、shadcn也提供了skeleton组件