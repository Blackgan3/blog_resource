vuex是vue中单向数据流的一个状态管理模式，它可以集中存储管理应用中所有组件的状态，并且有一套相应的规则可以去预测数据的变化。类似与此的还有react中的redux,dva等状态管理模式。

<!--more-->

一般我们的状态管理包含以下几个部分：
* state 这是驱动页面变化的数据源
* view  state数据展示的视图
* action 在view层用户操作数据变化的响应

vue中的数据流为单向数流

![数据流](https://vuex.vuejs.org/zh-cn/images/flow.png)

单向数据流在兄弟组件需要传参或者多个组件需要使用同一个状态并且多个组将都可以改变该状态时不易进行维护。

因此，我们采取的是将多个共用的状态抽离到一个全局单例中（实际上就是将组件的状态抽离出来进行单独管理），其实在redux和dva中，是将每个组件的状态抽离到它自己的单例状态中，并且这些单例状态之间是互通的。

>vuex中数据流的一个大概的流程是，我们再视图层通过触发一个一个的action到mutations中，mutataions中改变对应的state,然后该state的变化会去影响所对应的视图层的html结构。

当然正如其他状态机模式一样，如果你不打算开发大型单页应用，也是没必要去使用vuex。

#### 核心概念
vuex应用的核心就是store,store中包含着我们应用中的大部分状态，vuex的状态存储是快速响应的，当store中的state有变化时，相应的组件也会快速的更新。并且我们需要遵循vuex中的规则，无法直接去改变state,改变store中的状态的唯一途径就是通过commit.

通过每次的commit中所含的信息，我们可以轻松明确的去看到我们每次改变state的意图，这样也方便我们将来对数据的追踪。

##### state
由于vuex使用单一状态树，也就是一个应用中的state你可以全部放到这一个store中，但是如果你放置的状态过多的时候，这也是挺鸡肋的不是？我还没有去看vuex如何将状态和状态变更的事件分布到各个子模块中去。

mapState是vuex提供的简化数据访问的辅助函数，mapState函数返回的是一个对象，通常，我们需要使用一个工具函数将多个对象合并为一个，以使我们可以将最终对象传给computed属性。

当然，使用vuex并不意味着我们将所有的状态放入vuex，虽然将所有的状态放到vuex会使状态变化更显示和易调试，但是如果全部放到了全局Store中我们的代码会变得冗长和不直观，所以这些个东西还需要边开发边权衡吧。

##### Getter

有时候我们需要从store中的state中派生出一些状态，例如对列表进行过滤并计数：
```js
computed: {
  doneTodosCount () {
    return this.$store.state.todos.filter(todo => todo.done).length
  }
}
```

vuex允许我们在store中定义“getter".就像计算属性一样，getter返回值会根据他的依赖被缓存起来，且只有当他的依赖值发生了改变才会被重新计算。

getter接受state作为其第一个参数：
```js
const store = new Vuex.Store({
  state: {
    todos: [
      { id: 1, text: '...', done: true },
      { id: 2, text: '...', done: false }
    ]
  },
  getters: {
    doneTodos: state => {
      return state.todos.filter(todo => todo.done)
    }
  }
})
```
getter会暴露store.getters对象：
getter类似于dva中的reducer。
```js
store.getters.doneTodos // -> [{ id: 1, text: '...', done: true }
```
getter也可以接受其他getter作为第二个参数：
```js
getters: {
  // ...
  doneTodosCount: (state, getters) => {
    return getters.doneTodos.length
  }
}
store.getters.doneTodosCount // -> 1
```
我们可以很容易的在任何组件中调用它：
```js
computed: {
  doneTodosCount () {
    return this.$store.getters.doneTodosCount
  }
}
```
###### mapGetters辅助函数
mapGetters辅助函数仅仅是将store中的getter映射到局部计算属性。
例如：
```js
computed:{
    ...mapGetters(['getter1','getter2'])
}
```
按照上述写法，全局store中的getter1和getter2函数便可以在局部组件中使用了。
如果想将一个getter属性另取一个名字，使用对象形式：
```js
computed:{
    ...mapGetters({
        newGetter:"oldStoreGetter"
    })
}
```
#### Mutation
更改Vuex的store中的状态的唯一办法是提交mutation.Vuex中的mutation非常类似于事件，这有点类似于dva中的effects,每一个mutation都会有一个字符串的事件类型和一个回调函数。这个回调函数就是我们实际进行状态更改的地方。并且他会接受state作为第一个参数：
```js
 mutations: {
    setAdminInfo(state,adminInfo){
      state.adminInfo = adminInfo;
    },
    setCityList(state,cityList){
      state.cityList = cityList
    }
  }
```
当然了，我们还不能直接调用mutation handler,我们想要执行此函数时，需要以相应的type调用store.commit方法:

当我们需要给mutation传参时，我们需要通过payload来进行,上述例子中的adminInfo就是我们要传的的参数，在vuex中叫payload，当然，官方的建议是payload尽量是一个对象，这样我们在使用的时候能够更好的去追踪数据流。

当然，更好的调用mutation的方式是commit包含type属性的对象：
```js
store.commit({
    type:'updateAdminInfo',
    adminInfo:{
        username:'allen',
        password:'qwe123',
    }
})
```
mutation需要遵守vue的响应规则

既然Vuex的store中的状态是响应式的，那么当我们变更状态时，监视状态的vue组件也会自动更新。这就意味着Vuex中的mutation也需要与Vue一样遵守一些注意事项：

* 在store中初始化好所有的属性
* 使用新对象替换老对象，即每次都返回一个全新的state对象，可以使用Object.Assign的方式。

>使用常亮替代Mutation事件类型

使用常量替代mutation事件类型再各种flux实现中是很常见的模式。可以使我们整个项目的数据流向一目了然。

当然，另外重要的一点就是mutation必须是同步函数，当我们再debug一个app并且观察devtool的mutation日志时，每一条mutation被记录，都需要捕捉到前一状态和后一状态的快照。

我们可以在组件中使用this.$store.commit("example")提交mutation,或者使用mapMutations辅助函数将组件中的methods映射为store.commit调用。
```js
import { mapMutations } from 'vuex'

export default {
  // ...
  methods: {
    ...mapMutations([
      'increment', // 将 `this.increment()` 映射为 `this.$store.commit('increment')`

      // `mapMutations` 也支持载荷：
      'incrementBy' // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
    ]),
    ...mapMutations({
      add: 'increment' // 将 `this.add()` 映射为 `this.$store.commit('increment')`
    })
  }
}
```
#### Action

Action类似于mutation,不同在于:
* Action提交的是mutation，而不是直接变更状态。
* Action可以包含任意异步操作。

下边来看一个简单的action的例子：
```jsconst store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    }
  }
})
const store = new Vuex.Store({
  state: {
    count: 0
  },
  mutations: {
    increment (state) {
      state.count++
    }
  },
  actions: {
    increment (context) {
      context.commit('increment')
    }
  }
})
```

Action通过store.dispatch方法触发：
```js
store.dispatch('increment');
```
使用action进行触发mutation可以不用受必须同步执行的约束。我们可以在Action内部执行异步操作。

同样Action中也可以使用payload来传递参数
```js
store.dispatch({
    type:"increment",
    amount:10
})
```
同时，Action通常是异步的，store.dispatch可以处理被触发的action的处理函数返回的Promise,并且store.dispatch仍然返回Promise.

现在，我们已经不需要去执行promise.then函数了，我们直接用async/await就可以了。
```js
async action1({commit}){
    commit('gotData', await getData());
}
```
### Module

使用单一状态树，应用的所有状态会集中到一个比较大的对象中。当应用变得非常复杂时，store对象就会变得十分臃肿。

vuex允许我们将store分割成模块，每个模块拥有自己的state,mutation,acion,getter,甚至是嵌套子模块，从上至下进行分割。
```js
const moduleA = {
  state: { ... },
  mutations: { ... },
  actions: { ... },
  getters: { ... }
}

const moduleB = {
  state: { ... },
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

##### 模块的局部状态
对于模块内部的mutation和getter,接受的第一个参数是模块的局部状态对象。同样的，对于模块内部的action，局部状态通过context.state暴露出来，根节点状态则为context.rootState；

对于模块内部的getter,根节点状态会作为第三个参数暴露出来；

##### 命名空间
默认情况下，模块内部的action,mutation,和getter是注册再全局命名空间的--这样可以使多个模块能够对同一个mutation或action作出响应。

当然，模块化中，我们可以使用namespaced:true的方式使其成为命名空间模块。当模块被注册之后，他的所有getter、Action、以及mutation都会自动根据模块注册的路径调整命名。启用了命名空间之后，便是将一个整体的store给分割成了一个个模块。

##### 模块动态注册

在store创建之后，你可以使用store.registerModule方法注册模块：
```js
// 注册模块 `myModule`
store.registerModule('myModule', {
  // ...
})
// 注册嵌套模块 `nested/myModule`
store.registerModule(['nested', 'myModule'], {
  // ...
})
```
当然，我们也可以通过store.unregisterModule（moduleName）来动态卸载模块。但是我们无法使用此方法去卸载静态模块。

---
Vuex并不限制你的代码结构。但是，他规定了一些需要遵守的规则：
* 应用层级的状态应该集中到单个store对象中。
* 提交mutation是更改状态的唯一办法，并且这个过程是同步的。
* 异步逻辑都应该封装在Action里面。


