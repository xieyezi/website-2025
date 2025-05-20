---
title: VueX的相关使用
tags:
  - Vue
  - JS
---

### VueX 是什么?

Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式.它采用集中式存储管理应用的所有组件的状态,并以相应的规则保证状态以一种可预测的方式发生变化.Vuex 也集成到 Vue 的官方调试工具 devtools extension,提供了诸如零配置的 time-travel 调试、状态快照导入导出等高级调试功能.

  <!-- more -->

总的来说,我们对一个单向的数据流的操作如下图:
![flow](http://wx4.sinaimg.cn/mw690/89296167gy1fuwd8r9apvj20zk0o2gm0.jpg)

我们在视图(view)上对数据进行了类似于点击等操作,就会通过 Action 触发对数据的状态(State)的改变,就形成了这样的单向数据流操作.

### 为什么要用 VueX？

许多组件需要同时共享一个数据的使用和更新,但是组件之间无法全然构成子父级组件的关系,此时针对这个数据就需要一个公共的状态管理器,也就是 VueX.所以当项目组件与组件之间存在着太复杂的共享关系和操作时,就应该使用 VueX.

### VueX 的核心

每一个 Vuex 应用的核心就是 store（仓库）.“store”基本上就是一个容器,它包含着你的应用中大部分的状态 (state).Vuex 和单纯的全局对象有以下两点不同:

1.  Vuex 的状态存储是响应式的.当 Vue 组件从 store 中读取状态的时候,若 store 中的状态发生变化,那么相应的组件也会相应地得到高效更新.

2.  你不能直接改变 store 中的状态.改变 store 中的状态的唯一途径就是显式地提交 (commit) mutation.这样使得我们可以方便地跟踪每一个状态的变化,从而让我们能够实现一些工具帮助我们更好地了解我们的应用.

VueX 其实就是为上图的三个操作增加了流程,如下图:
![VueX](http://wx2.sinaimg.cn/mw690/89296167gy1fuwdllhvgoj20jh0fbwef.jpg)

VueX 增加了 Mutation.在这里,将操作(Action)统一提交到 Mutation,在 Mutation 经过一系列的处理,再改变(状态)State,状态改变之后,再反应到视图(view)上.
就像下面这样:
![VueX流程图](http://wx1.sinaimg.cn/mw690/89296167gy1fuwdzb7lvbj20q903fglk.jpg)
所以 VueX 就很好理解了.就是统一管理公共状态的东西.
不过需要注意一点:
Action 不是必需的操作,如果有异步操作时才用到 Action,否则可以不使用.也就是说,View 可以直接提交到 Mutation,从而改变状态,引起视图的改变.
Action 类似于 mutation,不同在于:

- Action 提交的是 mutation,而不是直接变更状态.
- Action 可以包含任意异步操作.

### 核心概念和使用

1. 核心概念

- State
- Getter
- Mutation
- Action

2. VueX 的基本使用

安装(npm):

```
npm install vuex --save
```

引入:
在项目 src 文件夹下新建 store 文件夹,再到 store 文件夹新建 index.js 文件:

```JS
import Vue from 'vue'
import Vuex from 'vuex'

Vue.use(Vuex)
//创建一个store仓库
export default new Vuex.Store({
  state:{
    count: 11
  }
})
```

注:目前 vue 官方没有关于 vuex 的脚手架工具,所以必须手动 store 文件夹和 index.js 文件.
在 main.js 导入:

```JS
import store from './store'
```

新建 State(在 index.js 文件)

```JS
state:{
  count: 11
}
```

在外部组件中获取 count 的值:

```JS
computed:{
 getCount(){
   return this.$store.state.count;
 }
}
//这里的this.$store.state.count即是通过State获取
```

不过一般都会为 state 添加 Getter:Vuex 允许我们在 store 中定义“getter”（可以认为是 store 的计算属性）.就像计算属性一样,getter 的返回值会根据它的依赖被缓存起来,且只有当它的依赖值发生了改变才会被重新计算.
在这里我们为 count 新建一个 getter(在 index.js):

```JS
getters:{
  getState(state){
    return state.count > 0 ? state.count : 0;
  }
}
```

所以在组件中获取 count 值的方法要通过 getter:

```JS
computed:{
 getCount(){
   // return this.$store.state.count;
   return this.$store.getters.getState;
 }
}
//这里的this.$store.getters.getState即是通过getter获取
```

状态(State)和 getter 建好以后,我们先通过 mutations 改变 count 的值:

```JS
mutations:{
  incres(state){
    state.count++;
  },
  decre(state){
    state.count--;
  }
}
//这里的incres函数即为count作++操作,decre函数为count作--操作
```

在外部组件中使用:

```html
<button type="button" name="button" @click="add">加加</button>
<!-- 这里为template里面的内容 -->
```

```JS
methods:{
  add(){
    this.$store.commit("incres");
  }
}
//在这里通过commit调用了incres函数
```

以上就是通过 mutations 改变 count 的值,接下来我们通过 actions 来改变 count 的值:

```JS
actions: {
  // context 承上启下,在这里可以通过context调用到commit的执行
  incres(context){
    context.commit("incres");
  },
  decre(context){
    context.commit("decre");
  }
}
```

在外部组件中使用:

```html
<button type="button" name="button" @click="min">减减</button>
<!-- 这里为template里面的内容 -->
```

```JS
methods:{
  min(){
    this.$store.dispatch("decre");
  }
}
//在这里通过dispatch调用了decre函数
```

以上就是通过 actions 改变 count 的值.
总结:mutation 像事件注册,需要相应的触发条件.而 Action 就是那个管理触发条件的.mutations 只能同步执行,actions 则可以执行异步操作.
