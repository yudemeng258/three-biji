### vuex初始  

> vuex定义  vuex是一个状态管理的插件，可以解决不同组件之间的数据共享和数据持久化。通俗点的说法就是全局的数据仓库，在组件里面的data是组件里的数据仓库也就是局部的数据仓库，全局数据仓库里面的数据可以在任意组件中共享。
> vuex是一个专门为vue.js设计的集中式状态管理架构。状态？我把它理解为在data中的属性需要共享给其他vue组件使用的部分，就叫做状态。简单的说就是data中需要共用的属性。

## Vuex 的基本结构 

Vuex 是一个个专为 vue.js 应用程序开发的状态管理模式。它采用集中式存储来管理应用
所有组件的状态，并以相应的规则来保证状态以一种可预测的方式发生变化。 Vuex 集成
到了 Vue 官方调试工具 devtools extension 

> 安装vue devtools 

1. 直接谷歌商店安装
2. 

```
git clone git@github.com:vuejs/vue-devtools.git

```

[参考](https://segmentfault.com/a/1190000009682735 "参考网址")

### 安装vuex

1. `cnpm install vuex --save `
2. 在main.js的同级目录新建store文件夹 里面新建一个index.js 
3. 在index.js 里面引入 vuex

```
import Vue from 'vue'
import Vuex from 'vuex'
Vue.use(Vuex)
```

4. 向外暴露vuex实例 

```
export default new Vuex.Store({
  state:{},
  mutations:{},
  getters:{},
  actions:{},
  modules:{},
})
```

+ state 一－ vuex store 实例的根状态对象，用于定义共享的状态变量，就像 Vue 实例中data
+ mutations 一 可以理解为 store的methods(同步操作 )
+ getters 一 读取器，外部程序通过它获取变量的具体值，或者在取值前做一些计算（可以认为是 store 的计算属性）
+ actions  以理解为 store的methods(异步操作 如ajax)
+ modules 以模块化的方式写vuex (如购物车对应一个module 个人中心对应一个module )

5. 挂载倒vue实例类上

```
import store from './store/store'

new Vue({
  router,
  store,
  render: h => h(App)
}).$mount('#app')
```

## 开始 
 
###  获取state里面的值

1. 在state里面放入数据

```
export default new Vuex.Store({
  state: {
    num:1
  },
  mutations: {
    
  },
  actions: {

  }
})
```

2. 在某个组件获取这个数据 用computed来接收这个数据

```
computed: {
list(){
    return this.$store.state.num
}
}
```

### 更改state里面的值 mutations(同步提交)

 更改state里面的值 更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。Vuex 中的 mutation 非常类似于事件：每个 mutation 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)。这个回调函数就是我们实际进行状态更改的地方，并且它会接受 state 作为第一个参数
 "同步模式"就是上一段的模式，后一个任务等待前一个任务结束，然后再执行。

在mutation里面写入更改的函数 函数的第一个参数代表 state 更改vuex中的state是通过更改这个参数实现的 
[Mutation 必须是同步函数](https://vuex.vuejs.org/zh/guide/mutations.html#mutation-%E5%BF%85%E9%A1%BB%E6%98%AF%E5%90%8C%E6%AD%A5%E5%87%BD%E6%95%B0 "Mutation 必须是同步函数")
为什么必须是同步函数呢 因为在devtools日志里面监测不到数据的变化

```
export default new Vuex.Store({
  state: {
    num:1
  },
  mutations: {
    updateNum(state){
      state.num ++   // this.$store.commit("updateNum")
    }
  },
  actions: {

  }
})
```

> 提交载荷（Payload） 其实就是提交的时候附带参数 只能提交一个载荷

```
export default new Vuex.Store({
  state: {
    num:1
  },
  mutations: {
    updateNum(state,val){
      state.num += val;  // this.$store.commit("updateNum",2)
    }
  },
  actions: {

  }
})
```

提交多个载荷 vuex不支持提交多个载荷 提交多个载荷需换种写法

```
export default new Vuex.Store({
  state: {
    num:1
  },
  mutations: {
    updateNum(state,val){
      state.num += val.val;  // this.$store.commit("updateNum",{val:1,val1:2})
    }
  },
  actions: {

  }
})
```

### action(异步更改state)

Action 类似于 mutation，不同在于：

+ Action 提交的是 mutation，而不是直接变更状态。  // action 里面提交数据是提交mutation 在mutation里面是直接修改state
+ Action 可以包含任意异步操作。

```
export default new Vuex.Store({
  state: {
    num:1
  },
  mutations: {
    updateNum(state,val){
        state.num += val; 
      
      
    }
  },
  actions: {
    syncUpdate(context ,val){
      setTimeout(function(){
        context.commit("updateNum",val)  // this.$store.dispatch('syncUpdate',5) 分发 Action
      },400)
      
    },
    syncUpdate({commit} ,val){  // 解构写法
      console.log(context)
      setTimeout(function(){
        commit("updateNum",val)  
      },400)
      
    },
  }
})
```

### getters (相当于计算属性)

```
export default new Vuex.Store({
  state: {
    num:1
  },
  getters:{
    nums(state){
      return state.num + "元"  // this.$store.getters.nums
    }
  },
  mutations: {
    updateNum(state,val){
        state.num += val; 
      
      
    }
  },
  actions: {
    syncUpdate(context ,val){
      console.log(context)
      setTimeout(function(){
        context.commit("updateNum",val)  
      },400)
      
    },
    syncUpdate({commit} ,val){
      console.log(context)
      setTimeout(function(){
        commit("updateNum",val)  
      },400)
      
    },
  }
})

```