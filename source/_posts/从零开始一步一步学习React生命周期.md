---
uuid: fc654e93-c869-4584-6f64-65b1dc1afd93
title: 从零开始一步一步学习React生命周期
date: 2020-11-10 21:06:45
categories: 技术
tags: 
- react
---
在学习ReactJs Hooks的时候，对其中的几个副作用函数一直理不清，总觉得是由于Reactjs的基本知识还不够熟悉，特别是对于ReactJs的生命周期部分。因此本文主要是对ReactJs的生命周期进行一个学习记录，改文的代码，完全是从零开始，一步一步的手写测试。只有这样，通过比较和测试，才能更深一层次地对ReactJs的生命周期有所了解，写起代码才能得心应手，发现bug才能又快又准，优化性能才能一语中的。
## 前言
每一套框架都有自己的生命周期，例如Vuejs(beforeCreated、created...)。当然，Reactjs也不例外。下面是reactjs的生命周期图，虽然有些生命周期已经被摒弃了，但是闲着也是闲着，就都学习了吧。

![ReactJs生命周期](https://user-gold-cdn.xitu.io/2018/8/12/16529f7518a0d615?imageView2/1/w/1304/h/734/q/85/format/webp/interlace/1)

## constructor、render
利用Reactjs的class写法，先新建一个简单的点击计数的demo：
```js
import React, { Component } from 'react'

export default class Reviews extends Component {
    constructor(props){
        console.log('Constructor CONTENT')
        super(props)
        this.state = {number: 0};
    }
    numAdd = () => {
        this.setState({
            number:this.state.number+1
        })
    }

    render() {
        console.log('Render CONTENT')
        return (
            <div>
                <div>当前数字是：{this.state.number}</div>
                <button onClick={this.numAdd}>点我+1</button>
            </div>
        )
    }
}
```
当我们访问该组件，在控制台默认输出：
```js
// Constructor CONTENT
// Render CONTENT
```
也就是说，先执行`constructor()`里的东西，再去执行`render()`函数：先初始化数据，然后再把数据渲染到DOM中。

所以目前的生命周期更新为：**constructor()>render()**

当我们此时点击三下按钮后，控制台会多输出三次
```js
// Render CONTENT
// Render CONTENT
// Render CONTENT
```
这就表明，当```constructor()```中的数据发生变化时，会自动触发```render()```函数，所以上述操作会输出三次 ' Render CONTENT ' ，但是```constructor()```并不会再去执行。

接下来，我们将Reactjs自带的几个生命周期依次加入到demo中，通过查看控制台的输出来了解生命周期的执行过程。
## componentWillMount
```js
import React, { Component } from 'react'

export default class Reviews extends Component {
    constructor(props){
        console.log('Constructor CONTENT')
        super(props)
        this.state = {number: 0};
    }
    numAdd = () => {
        this.setState({
            number:this.state.number+1
        })
    }
    // ********** 修改的地方 **********
    componentWillMount() {
		console.log('Component WILL MOUNT!')
	}
    render() {
        console.log('Render CONTENT')
        return (
            <div>
                <div>当前数字是：{this.state.number}</div>
                <button onClick={this.numAdd}>点我+1</button>
            </div>
        )
    }
}
```
这时候控制台输出：
```js
// Constructor CONTENT
// Component WILL MOUNT!
// Render CONTENT
```
所以目前的生命周期更新为：**constructor()>componentWillMount()>render()**

当我们点击计数按钮时，控制台依然只会输出：
```js
// Render CONTENT
```
表明，组件内部的数据更新，并不会触发`componentWillMount()`执行。
## componentDidMount
```js
import React, { Component } from 'react'

export default class Reviews extends Component {
    constructor(props){
        console.log('Constructor CONTENT')
        super(props)
        this.state = {number: 0};
    }
    numAdd = () => {
        this.setState({
            number:this.state.number+1
        })
    }
    componentWillMount() {
		console.log('Component WILL MOUNT!')
    }
    // ********** 修改的地方 **********
    componentDidMount() {
        console.log('Component DID MOUNT!')
    }
    render() {
        console.log('Render CONTENT')
        return (
            <div>
                <div>当前数字是：{this.state.number}</div>
                <button onClick={this.numAdd}>点我+1</button>
            </div>
        )
    }
}
```
这时候控制台输出：
```js
// Constructor CONTENT
// Component WILL MOUNT!
// Render CONTENT
// Component DID MOUNT!
```
所以目前的生命周期更新为：**constructor()>componentWillMount()>render()>componentDidMount()**
当我们点击计数按钮时，控制台依然只会输出：
```js
// Render CONTENT
```
表明，组件内部的数据更新，并不会触发`componentDidMount()`执行。
通常我们一进入页面就需要发起ajax请求时，这个动作就会在该生命周期中执行，我们尝试在这个生命周期中修改组件数据，看看有什么变化：
```js
import React, { Component } from 'react'

export default class Reviews extends Component {
    constructor(props){
        console.log('Constructor CONTENT')
        super(props)
        this.state = {number: 0};
    }
    numAdd = () => {
        this.setState({
            number:this.state.number+1
        })
    }
    componentWillMount() {
		console.log('Component WILL MOUNT!')
    }
    // ********** 修改的地方 **********
    componentDidMount() {
        console.log('Component DID MOUNT!')
        this.setState({
            number:this.state.number+100
        })
    }
    render() {
        console.log('Render CONTENT')
        return (
            <div>
                <div>当前数字是：{this.state.number}</div>
                <button onClick={this.numAdd}>点我+1</button>
            </div>
        )
    }
}
```
在`componentDidMount()`中将本地数据`number`自增100，控制台会输出：
```js
// Constructor CONTENT
// Component WILL MOUNT!
// Render CONTENT
// Component DID MOUNT!
// Render CONTENT
```
这就表明：`componentDidMount()`中只要涉及修改组件数据，就会再次出发`render()`函数。
到目前为止，可以总结一些结论：
- 只要涉及数据更新，必然会触发render函数；
- constructor、componentWillMount和componentDidMount在生命周期中只会执行一次。

## shouldComponentUpdate
> 根据 shouldComponentUpdate() 的返回值，判断 React 组件的输出是否受当前 state 或 props 更改的影响。默认行为是 state 每次发生变化组件都会重新渲染。大部分情况下，你应该遵循默认行为。

```js
import React, { Component } from 'react'

export default class Reviews extends Component {
    constructor(props){
        console.log('Constructor CONTENT')
        super(props)
        this.state = {number: 0};
    }
    numAdd = () => {
        this.setState({
            number:this.state.number+1
        })
    }
    componentWillMount() {
		console.log('Component WILL MOUNT!')
    }
    componentDidMount() {
        console.log('Component DID MOUNT!')
        this.setState({
            number:this.state.number+100
        })
    }
    // ********** 修改的地方 **********
    shouldComponentUpdate(preState, newState) {
        console.log('Should Component update!')
        return true;
    }
    render() {
        console.log('Render CONTENT')
        return (
            <div>
                <div>当前数字是：{this.state.number}</div>
                <button onClick={this.numAdd}>点我+1</button>
            </div>
        )
    }
}
```
此时控制台输出：
```js
// Constructor CONTENT
// Component WILL MOUNT!
// Render CONTENT
// Component DID MOUNT!
// Should Component update!
// Render CONTENT
```
当我们点击一次计数按钮时，此时控制台会输出：
```js
// Should Component update!
// Render CONTENT
```
也就是说，只要涉及数据更新，必然会触发`render`函数，但是在触发render函数之前，会先触发`shouldComponentUpdate()`。所以目前的生命周期更新为：**constructor()>componentWillMount()>render()>componentDidMount()>shouldComponentUpdate()**。

当在shouldComponentUpdate里面修改如下代码，会有什么变化：
```js
import React, { Component } from 'react'

export default class Reviews extends Component {
    constructor(props){
        console.log('Constructor CONTENT')
        super(props)
        this.state = {number: 0};
    }
    numAdd = () => {
        this.setState({
            number:this.state.number+1
        })
    }
    componentWillMount() {
		console.log('Component WILL MOUNT!')
    }
    componentDidMount() {
        console.log('Component DID MOUNT!')
        this.setState({
            number:this.state.number+100
        })
    }
    // ********** 修改的地方 **********
    shouldComponentUpdate(preState, newState) {
        console.log('Should Component update!')
        return false;
    }
    render() {
        console.log('Render CONTENT')
        return (
            <div>
                <div>当前数字是：{this.state.number}</div>
                <button onClick={this.numAdd}>点我+1</button>
            </div>
        )
    }
}
```
此时控制台输出：
```js
// Constructor CONTENT
// Component WILL MOUNT!
// Render CONTENT
// Component DID MOUNT!
// Should Component update!
```
并没有再次出发`render`函数，这就表明，如果`shouldComponentUpdate`中返回的是`false`，则不会再次渲染DOM,所以页面上显示'当前数字是：0'，但组件内存中的`number`数据被修改了吗？由于`this.setState`函数是异步的，我们通过回调函数来输出一下此时的`number`是多少：
```js
import React, { Component } from 'react'

export default class Reviews extends Component {
    constructor(props){
        console.log('Constructor CONTENT')
        super(props)
        this.state = {number: 0};
    }
    numAdd = () => {
        this.setState({
            number:this.state.number+1
        })
        
    }
    componentWillMount() {
		console.log('Component WILL MOUNT!')
    }
    // ********** 修改的地方 **********
    componentDidMount() {
        console.log('Component DID MOUNT!')
        this.setState({
            number:this.state.number+100
        },()=>{
            console.log(this.state.number)
        })
    }
    shouldComponentUpdate(preState, newState) {
        console.log('Should Component update!')
        return false;
    }
    render() {
        console.log('Render CONTENT')
        return (
            <div>
                <div>当前数字是：{this.state.number}</div>
                <button onClick={this.numAdd}>点我+1</button>
            </div>
        )
    }
}
```
此时控制台输出结果为：
```js
// Constructor CONTENT
// Component WILL MOUNT!
// Render CONTENT
// Component DID MOUNT!
// Should Component update!
// 100
```
这就表明，当组件中对数据改变时，它确实会造成`state`中的数据改变，但是当`shouldComponentUpdate`返回的是`false`时，不会触发`render`函数，导致不会渲染新的数据到DOM中。
## componentWillUpdate
按照上面的步骤加入componentWillUpdate：
```js
import React, { Component } from 'react'

export default class Reviews extends Component {
    constructor(props){
        console.log('Constructor CONTENT')
        super(props)
        this.state = {number: 0};
    }
    numAdd = () => {
        this.setState({
            number:this.state.number+1
        })
        
    }
    componentWillMount() {
		console.log('Component WILL MOUNT!')
    }
    componentDidMount() {
        console.log('Component DID MOUNT!')
        this.setState({
            number:this.state.number+100
        },()=>{
            console.log(this.state.number)
        })
    }
    shouldComponentUpdate(preState, newState) {
        console.log('Should Component update!')
        // ********** 修改的地方 **********
        return true;
    }
    // ********** 修改的地方 **********
    componentWillUpdate(nextProps, nextState) {
        console.log('Component WILL UPDATE!');
    }
    render() {
        console.log('Render CONTENT')
        return (
            <div>
                <div>当前数字是：{this.state.number}</div>
                <button onClick={this.numAdd}>点我+1</button>
            </div>
        )
    }
}
```
这时候控制台输出：
```js
// Constructor CONTENT
// Component WILL MOUNT!
// Render CONTENT
// Component DID MOUNT!
// Should Component update!
// Component WILL UPDATE!
// Render CONTENT
// 100
```
这里设置`shouldComponentUpdate`返回的是`true`，假如设置的是false，返回的结果是：
```js
// Constructor CONTENT
// Component WILL MOUNT!
// Render CONTENT
// Component DID MOUNT!
// Should Component update!
// 100
```
也就是说，跟再次渲染的`render`函数一样，`componentWillUpdate`受`shouldComponentUpdate`的限制，只有当`shouldComponentUpdate`返回为`true`时才会执行，反之，则不会执行。所以目前的生命周期更新为：**constructor()>componentWillMount()>render()>componentDidMount()>shouldComponentUpdate()>componentWillUpdate()**。
## componentDidUpdate
同样将componentDidUpdate添加到代码中：
```js
import React, { Component } from 'react'

export default class Reviews extends Component {
    constructor(props){
        console.log('Constructor CONTENT')
        super(props)
        this.state = {number: 0};
    }
    numAdd = () => {
        this.setState({
            number:this.state.number+1
        })
        
    }
    componentWillMount() {
		console.log('Component WILL MOUNT!')
    }
    componentDidMount() {
        console.log('Component DID MOUNT!')
        this.setState({
            number:this.state.number+100
        },()=>{
            console.log(this.state.number)
        })
    }
    shouldComponentUpdate(preState, newState) {
        console.log('Should Component update!')
        return true;
    }
    componentWillUpdate(nextProps, nextState) {
        console.log('Component WILL UPDATE!');
    }
    // ********** 修改的地方 **********
    componentDidUpdate(prevProps, prevState) {
        console.log('Component DID UPDATE!')
    }
    render() {
        console.log('Render CONTENT')
        return (
            <div>
                <div>当前数字是：{this.state.number}</div>
                <button onClick={this.numAdd}>点我+1</button>
            </div>
        )
    }
}
```
这时候控制台输出：
```js
// Constructor CONTENT
// Component WILL MOUNT!
// Render CONTENT
// Component DID MOUNT!
// Should Component update!
// Component WILL UPDATE!
// Render CONTENT
// Component DID UPDATE!
// 100
```
所以目前的生命周期更新为：**constructor()>componentWillMount()>render()>componentDidMount()>shouldComponentUpdate()>componentWillUpdate()>componentDidUpdate()**。
## componentWillUnmount
现在将componentWillUnmount也添加到代码中：
```js
import React, { Component } from 'react'

export default class Reviews extends Component {
    constructor(props){
        console.log('Constructor CONTENT')
        super(props)
        this.state = {number: 0};
    }
    numAdd = () => {
        this.setState({
            number:this.state.number+1
        })
        
    }
    componentWillMount() {
		console.log('Component WILL MOUNT!')
    }
    componentDidMount() {
        console.log('Component DID MOUNT!')
        this.setState({
            number:this.state.number+100
        },()=>{
            console.log(this.state.number)
        })
    }
    shouldComponentUpdate(preState, newState) {
        console.log('Should Component update!')
        return true;
    }
    componentWillUpdate(nextProps, nextState) {
        console.log('Component WILL UPDATE!');
    }
    componentDidUpdate(prevProps, prevState) {
        console.log('Component DID UPDATE!')
    }
    // ********** 修改的地方 **********
    componentWillUnmount() {
        console.log('Component WILL UNMOUNT!')
    }
    render() {
        console.log('Render CONTENT')
        return (
            <div>
                <div>当前数字是：{this.state.number}</div>
                <button onClick={this.numAdd}>点我+1</button>
            </div>
        )
    }
}
```
此时控制台输出：
```js
// Constructor CONTENT
// Component WILL MOUNT!
// Render CONTENT
// Component DID MOUNT!
// Should Component update!
// Component WILL UPDATE!
// Render CONTENT
// Component DID UPDATE!
// 100
```
没有发生什么变化，但是当离开该组件（进入其他页面）时，控制台会输出：
```js
// Component WILL UNMOUNT!
```
这就表明该组件已销毁，所以目前的生命周期更新为：**constructor()>componentWillMount()>render()>componentDidMount()>shouldComponentUpdate()>componentWillUpdate()>componentWillUnmount**。
## 总结
上述一些生命周期只是reactJs全部生命周期的一部分，这里并没有涉及到props的情况，也就是组件间传值时值变化的情况，相关内容可查看[官网](https://zh-hans.reactjs.org/docs/state-and-lifecycle.html)，对上述内容做一个总结：

- 从组件的创建到渲染，必定会执行一次constructor()、componentWillMount()、render()和componentDidMount()，而且只执行一次；
- 当组件中的数据发生变化时，一定会触发shouldComponentUpdate()，shouldComponentUpdate()的返回值会影响接下来的几个生命周期函数和render()是否执行，若返回true（默认返回true）,则接下来的几个生命周期函数和render()会正常执行；反之，则不执行，也就是数据虽然更新了，但是并不会在界面更新显示；
- 在shouldComponentUpdate()的限制条件下，componentWillUpdate()和componentDidUpdate()可触发0次、或者多次，而render()函数至少会触发一次。

学习好ReactJs的生命周期会对理清业务逻辑产生很好的帮助，同时这也是给学习React hooks打下夯实的基础。

未完待续:D