---
uuid: 98461c9d-0b4c-d6f4-bfff-f51c8986445c
title: 集中式状态管理架构之vuex
categories: 技术
tags: vue
---
## 简介
> Vuex 是一个专为 Vue.js 应用程序开发的状态管理模式。它采用集中式存储管理应用的所有组件的状态，并以相应的规则保证状态以一种可预测的方式发生变化。
如果您不打算开发大型单页应用，使用 Vuex 可能是繁琐冗余的。确实是如此——如果您的应用够简单，您最好不要使用 Vuex。一个简单的 store 模式就足够您所需了。但是，如果您需要构建一个中大型单页应用，您很可能会考虑如何更好地在组件外部管理状态，Vuex 将会成为自然而然的选择。
## state
vuex中的state可以理解为vue中的data，就是存放静态数据的，官方的说明是状态管理，由于 Vuex 的状态存储是响应式的，从 store 实例中读取状态最简单的方法就是在计算属性中返回某个状态。
### /src/store/index.js
```js
import Vue from 'vue';
import vuex from 'vuex';
Vue.use(vuex);
export default new vuex.Store({
    state:{
        count:1
    }
});
```
### /index.js
```js
// ******改动的地方******
import store from './store/index.js';
new Vue({
    // ******改动的地方******
    store,
});
```
### /src/pages/Home.vue
```js
<template>
    <section class="home-area">
        <h1>首页</h1>
        <div class="module-list">
            <ul>
                <li @click="goAnimalList('dog')">狗狗列表</li>
                <li @click="goAnimalList('cat')">猫咪列表</li>
            </ul>
        </div>
    </section>
</template>

<script>
export default {
    data() {
        return {};
    },
    methods: {
        goAnimalList(params){
            if(params == 'dog'){
                this.$router.push('/dogList');
            }else if(params == 'cat'){
                this.$router.push('/catList');
            }
        },
    },
    components: {},
    // ******改动的地方******
    // 在计算属性中返回某个状态
    computed: {
        count () {
            return this.$store.state.count;
        }
    },
    created(){
        // ******改动的地方******
        // 使用计算属性中返回的状态
        console.log(this.count);
    }
};
</script>

<style lang='scss' scoped>
</style>
```
## getters
vuex中的getter可以理解为vue中的computed,vuex 允许我们在 store 中定义“getter”（可以认为是 store 的计算属性）。就像计算属性一样，getter 的返回值会根据它的依赖被缓存起来，且只有当它的依赖值发生了改变才会被重新计算。
### /src/store/index.js
```js
import Vue from 'vue';
import vuex from 'vuex';
Vue.use(vuex);
export default new vuex.Store({
    state:{
        count:1
    },
    // ******改动的地方******
    getters:{
        countAdd100:(state) => {
            return state.count + 100;
        }
    }
});
```
### /src/pages/Home.vue
```js
<template>
    <section class="home-area">
        <h1>首页</h1>
        <div class="module-list">
            <ul>
                <li @click="goAnimalList('dog')">狗狗列表</li>
                <li @click="goAnimalList('cat')">猫咪列表</li>
            </ul>
        </div>
    </section>
</template>

<script>
export default {
    data() {
        return {};
    },
    methods: {
        goAnimalList(params){
            if(params == 'dog'){
                this.$router.push('/dogList');
            }else if(params == 'cat'){
                this.$router.push('/catList');
            }
        },
    },
    components: {},
    computed: {
        count () {
            return this.$store.state.count;
        },
        // ******改动的地方******
        countAdd100(){
            return this.$store.getters.countAdd100;
        }
    },
    created(){
        console.log(this.count);
        // ******改动的地方******
        console.log(this.countAdd100);
    }
};
</script>

<style lang='scss' scoped>
</style>
```
## mutations
更改 Vuex 的 store 中的状态的唯一方法是提交 mutation。Vuex 中的 mutation 非常类似于事件：每个 mutation 都有一个字符串的 事件类型 (type) 和 一个 回调函数 (handler)。这个回调函数就是我们实际进行状态更改的地方，并且它会接受 state 作为第一个参数。
### /src/store/index.js
```js
import Vue from 'vue';
import vuex from 'vuex';
Vue.use(vuex);
export default new vuex.Store({
    state:{
        count:1
    },
    getters:{
        countAdd100:(state) => {
            return state.count + 100;
        }
    },
    // ******改动的地方******
    mutations:{
        // 提交载荷（Payload）
        increment(state,n){
            state.count += n;
        }
    }
});
```
### /src/pages/Home.vue
```js
<template>
    <section class="home-area">
        <h1>首页</h1>
        <div class="module-list">
            <ul>
                <li @click="goAnimalList('dog')">狗狗列表</li>
                <li @click="goAnimalList('cat')">猫咪列表</li>
            </ul>
            // ******改动的地方******
            <button @click="$store.commit('increment',10)">点我+10</button>
            // ******改动的地方******
            {{ count }}
        </div>
    </section>
</template>

<script>
export default {
    data() {
        return {};
    },
    methods: {
        goAnimalList(params){
            if(params == 'dog'){
                this.$router.push('/dogList');
            }else if(params == 'cat'){
                this.$router.push('/catList');
            }
        },
    },
    components: {},
    computed: {
        count () {
            return this.$store.state.count;
        },
        countAdd100(){
            return this.$store.getters.countAdd100;
        }
    },
    created(){
        console.log(this.count);
        console.log(this.countAdd100);
    }
};
</script>

<style lang='scss' scoped>
</style>
```

在大多数情况下，载荷应该是一个对象，这样可以包含多个字段并且记录的 mutation 会更易读：
### /src/store/index.js
```js
import Vue from 'vue';
import vuex from 'vuex';
Vue.use(vuex);
export default new vuex.Store({
    state:{
        count:1
    },
    getters:{
        countAdd100:(state) => {
            return state.count + 100;
        }
    },
    // ******改动的地方******
    // mutations:{
    //     // 提交载荷（Payload）
    //     increment(state,n){
    //         state.count += n;
    //     }
    // },
    mutations:{
        // 提交载荷（Payload）
        increment(state,payload){
            state.count += payload.amount;
        }
    }
});
```
### /src/pages/Home.vue
```js
<template>
    <section class="home-area">
        <h1>首页</h1>
        <div class="module-list">
            <ul>
                <li @click="goAnimalList('dog')">狗狗列表</li>
                <li @click="goAnimalList('cat')">猫咪列表</li>
            </ul>
            // ******改动的地方******
            <button @click="$store.commit({type: 'increment',amount: 100})">点我+100</button>
            {{ count }}
        </div>
    </section>
</template>

<script>
export default {
    data() {
        return {};
    },
    methods: {
        goAnimalList(params){
            if(params == 'dog'){
                this.$router.push('/dogList');
            }else if(params == 'cat'){
                this.$router.push('/catList');
            }
        },
    },
    components: {},
    computed: {
        count () {
            return this.$store.state.count;
        },
        countAdd100(){
            return this.$store.getters.countAdd100;
        }
    },
    created(){
        console.log(this.count);
        console.log(this.countAdd100);
    }
};
</script>

<style lang='scss' scoped>
</style>
```
## actions
Action 类似于 mutation，不同在于：
- Action 提交的是 mutation，而不是直接变更状态。
- Action 可以包含任意异步操作，而mutation只能是同步操作。

> Action 函数接受一个与 store 实例具有相同方法和属性的 context 对象，因此你可以调用 context.commit 提交一个 mutation，或者通过 context.state 和 context.getters 来获取 state 和 getters。但 context 对象不是 store 实例本身。
### /src/store/index.js
```js
import Vue from 'vue';
import vuex from 'vuex';
Vue.use(vuex);
export default new vuex.Store({
    state:{
        count:1
    },
    getters:{
        countAdd100:(state) => {
            return state.count + 100;
        }
    },
    mutations:{
        increment(state,payload){
            state.count += payload.amount;
        }
    },
    // ******改动的地方******
    actions:{
        incrementAsync({commit}){
            setTimeout(()=>{
                commit({type: 'increment',amount: 100});
            },3000);
        }
    }
});
```
### /src/pages/Home.vue
```js
<template>
    <section class="home-area">
        <h1>首页</h1>
        <div class="module-list">
            <ul>
                <li @click="goAnimalList('dog')">狗狗列表</li>
                <li @click="goAnimalList('cat')">猫咪列表</li>
            </ul>
            <button @click="$store.commit({type: 'increment',amount: 100})">点我+100</button>
            {{ count }}
            // ******改动的地方******
            <button @click="incrementAsync()">点我三秒后自增100</button>
        </div>
    </section>
</template>

<script>
export default {
    data() {
        return {};
    },
    methods: {
        goAnimalList(params){
            if(params == 'dog'){
                this.$router.push('/dogList');
            }else if(params == 'cat'){
                this.$router.push('/catList');
            }
        },
        // ******改动的地方******
        incrementAsync(){
            this.$store.dispatch('incrementAsync');
        }
    },
    components: {},
    computed: {
        count () {
            return this.$store.state.count;
        },
        countAdd100(){
            return this.$store.getters.countAdd100;
        }
    },
    created(){
        console.log(this.count);
        console.log(this.countAdd100);
    }
};
</script>

<style lang='scss' scoped>
</style>
```
逻辑就是先dispatch Actions里的异步方法，异步里面commit Mutations的同步方法，最终改变state中的值。
## mapState
之前在要使用vuex中的state，是这样的：
```js
computed: {
    count () {
        return this.$store.state.count;
    }
},
```
但这种写法过于繁琐，于是可以简化：
```js
import { mapState } from 'vuex';
export default {
    computed:mapState([
        'count',
        'total',
    ]),
}
```
上面的这段代码会转换为:
```js
import { mapState } from 'vuex'
export default {
  computed: {
    count () {
      return this.$store.state.count
    },
    total() {
      return this.$store.state.total
    },
  }
}
```
这么写简单了很多，但这样就会出现一个问题：如何将它与局部计算属性混合使用呢（ es6 的扩展运算符）？
```js
import { mapState } from 'vuex';
export default {
    computed:{
        // 局部计算属性
        localComputed(){
            return none;
        },
        // 注意是[]
        ...mapState([
            'count',
        ]),
        // 赋值使用，注意是{}
        ...mapState({
            newTotal:'total'
        })
    },
}
```
## mapGetters
mapGetters和mapState用法很像：
```js
import { mapState , mapGetters } from 'vuex';
export default {
    computed:{
        // 局部计算属性
        localComputed(){
            return none;
        },
        // 注意是[]
        ...mapState([
            'count',
        ]),
        // 赋值使用，注意是{}
        ...mapState({
            newTotal:'total'
        }),
        // ******改动的地方******
        // mapGetters
        ...mapGetters([
            'countAdd100'
        ])
    },
}
```
## mapMutations
你可以在组件中使用 this.$store.commit('xxx') 提交 mutation，或者使用 mapMutations 辅助函数将组件中的 methods 映射为 store.commit 调用（需要在根节点注入 store）。
```js
<template>
    <section class="home-area">
        <h1>首页</h1>
        <div class="module-list">
            <ul>
                <li @click="goAnimalList('dog')">狗狗列表</li>
                <li @click="goAnimalList('cat')">猫咪列表</li>
            </ul>
             // ******改动的地方******
            <!-- <button @click="$store.commit({type: 'increment',amount: 100})">点我+100</button> -->
            // ******改动的地方******
            <button @click="increment({amount: 1000})">点我+1000</button>
            {{ count }}
            <button @click="incrementAsync()">点我三秒后自增100</button>
        </div>
    </section>
</template>

<script>
// ******改动的地方******
import { mapState , mapGetters , mapMutations  } from 'vuex';
export default {
    data() {
        return {};
    },
    methods: {
        goAnimalList(params){
            if(params == 'dog'){
                this.$router.push('/dogList');
            }else if(params == 'cat'){
                this.$router.push('/catList');
            }
        },
        incrementAsync(){
            this.$store.dispatch('incrementAsync');
        },
        // ******改动的地方******
        ...mapMutations([
            // 将 `this.increment()` 映射为 `this.$store.commit('increment')`
            // `mapMutations` 也支持载荷：
            // 将 `this.incrementBy(amount)` 映射为 `this.$store.commit('incrementBy', amount)`
            'increment'
        ])
    },
    components: {},
    computed:{
        // 局部计算属性
        localComputed(){
            return none;
        },
        // 注意是[]
        ...mapState([
            'count',
        ]),
        // 赋值使用，注意是{}
        ...mapState({
            newTotal:'total'
        }),
        // mapGetters
        ...mapGetters([
            'countAdd100'
        ])
    },
    created(){
        console.log(PUBLIC_PROJECTNAME);
        console.log(PUBLIC_ENV);
        console.log(this.count);
        console.log(this.newTotal);
        console.log(this.countAdd100);
    }
};
</script>

<style lang='scss' scoped>
</style>
```
## mapActions
你在组件中使用 this.$store.dispatch('xxx') 分发 action，或者使用 mapActions 辅助函数将组件的 methods 映射为 store.dispatch 调用（需要先在根节点注入 store）：
```js
import { mapState , mapGetters , mapMutations , mapActions } from 'vuex';
export default{
    methods: {
        goAnimalList(params){
            if(params == 'dog'){
                this.$router.push('/dogList');
            }else if(params == 'cat'){
                this.$router.push('/catList');
            }
        },
        // ******改动的地方******
        // incrementAsync(){
        //     this.$store.dispatch('incrementAsync');
        // },
        ...mapMutations([
            // 将 `this.increment()` 映射为 `this.$store.commit('increment')`
            // `mapMutations` 也支持载荷：
            // 将 `this.increment(amount)` 映射为 `this.$store.commit('increment', amount)`
            'increment'
        ]),
        // ******改动的地方******
        ...mapActions([
            // 将 `this.incrementAsync()` 映射为 `this.$store.dispatch('incrementAsync')`
            // `mapActions` 也支持载荷：
            // 将 `this.incrementAsync(amount)` 映射为 `this.$store.dispatch('incrementAsync', amount)`
            'incrementAsync'
        ])
    },
}
```
## Module
由于使用单一状态树，应用的所有状态会集中到一个比较大的对象。当应用变得非常复杂时，store 对象就有可能变得相当臃肿。
为了解决以上问题，Vuex 允许我们将 store 分割成模块（module）。每个模块拥有自己的 state、mutation、action、getter、甚至是嵌套子模块——从上至下进行同样方式的分割。
### 官方：
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
```
### /src/store/index.js
```js
import Vue from 'vue';
import vuex from 'vuex';
import catVuex from './catModule/catVuex';
Vue.use(vuex);
const state = {
    count:20,
    total:40,
};
const getters = {
    countAdd100:(state) => {
        return state.count + 100;
    }
};
const mutations = {
    increment(state,payload){
        state.count += payload.amount;
    }
};
const actions = {
    incrementAsync({commit}){
        setTimeout(()=>{
            commit({type: 'increment',amount: 100});
        },3000);
    }
};
export default new vuex.Store({
    // 公用
    state,
    getters,
    mutations,
    actions,
    // 模块
    modules:{
        catVuex,
    }
});
```
### /src/store/catModule/catVuex.js
```js
const state = {
    catModuleState : 1
};
const getters = {};
const actions = {};
const mutations = {};
export default {
    // 使其成为带命名空间的模块,它的所有 getter、action 及 mutation 都会自动根据模块注册的路径调整命名
    namespaced: true,
    state,
    getters,
    actions,
    mutations
};
```
### 使用：
```js
import { mapState } from 'vuex';
export default {
    computed:{
        ...mapState('catVuex',[
            'catModuleState',
        ])
    },
}
```
### mapGetters，mapMutations 和 mapActions使用方法类似于 mapstate:
```js
import { mapState , mapGetters , mapMutations , mapActions } from 'vuex';
export default {
    computed:{
        // mapGetters 放在computed中
        ...mapGetters('catVuex',[
            'catModuleGetter'
        ]),
    },
    methods:{
        // mapMutations放在methods中
         ...mapMutations('catVuex',[
            'catModuleMutation'
        ]),
        // mapActions放在methods中
        ...mapActions('catVuex',[
            'catModuleAction'
        ])
    }
}
```