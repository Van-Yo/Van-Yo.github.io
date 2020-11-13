---
uuid: fb74c9ee-d0c0-d14a-edd2-ec02dc26e7f5
title: Vue数据双向绑定实现原理
date: 2020-09-12 21:06:45
categories: 技术
tags: 
- vue
---
Vue数据双向绑定（即数据响应式），是利用了Object.defineProperty()这个方法重新定义了对象获取属性值get和设置属性值set的操作来实现的。
## index.html
```js
<!DOCTYPE html>
<html lang="en">
<head>
    <meta charset="UTF-8">
    <meta name="viewport" content="width=device-width, initial-scale=1.0">
    <title>vue knowledge</title>
</head>
<body>
    <div id="app">
        <input type="text" v-model="school.name">
        <div>{{school.name}}</div>
        <div>{{school.age}}</div>
    </div>
    <script src="./mvvm.js"></script>
    <script>
        let vm = new Mvvm({
            el : '#app',
            data : {
                school : {
                    age :18,
                    name : 'vanlus'
                }
            }
        })
    </script>
</body>
</html>
```
## mvvm.js
```js
// 基类 调度
class Mvvm{
    constructor(options){
        this.$el = options.el;
        this.$data = options.data;
        if(this.$el){
            // 把数据 全部转化用Object.definedProperty来定义
            new Observer(this.$data);

            new Compiler(this.$el,this);
        }
    }
}

// 编译（用于将基类中的数据渲染到视图中）
class Compiler{
    constructor(el,vm){
        this.vm = vm;
        // 判断el属性是不是一个元素，如果不是元素，那就获取他
        this.el = this.isElementNode(el)?el:document.querySelector(el);

        // 把当前节点中的元素获取到放在内存中
        let fragment = this.node2fragment(this.el);

        // 把内存节点中的内容(变量)进行替换
        this.compile(fragment);
        // 把内存节点再塞到页面中
        this.el.appendChild(fragment);
    }
    // 判断时候是元素节点
    isElementNode(node){
        return node.nodeType == 1;
    }
    // 把节点移动到内存中
    node2fragment(node){
        // 创建一个文档碎片
        let fragment = document.createDocumentFragment();
        let firstChild;
        // 循环将node节点中的子元素一条一条地插到文档碎片中
        while(firstChild = node.firstChild){
            // appendChild具有移动性
            fragment.appendChild(firstChild);
        }

        return fragment;
    }
    // 用来编译内存中的dom节点
    compile(node){
        let childNodes = node.childNodes;
        [...childNodes].forEach(child=>{
            if(this.isElementNode(child)){
                this.compileElement(child);
                // 如果是元素的话，需要把自己传进去，再去遍历子节点
                this.compile(child);
            }else{
                this.compileText(child);
            }
        })
    }
    // 编译元素的
    compileElement(node){
        let attributes = node.attributes;   // 类数组
        [...attributes].forEach(attr=>{
            // type="text" v-model="school.name"
            let {name,value:expr} = attr;
            if(this.isDirective(name)){
                let [,directive] = name.split('-');
                // 需要调用不同的指令来处理
                CompileUtil[directive](node,expr,this.vm);

            }
        })
    }
    // 编译文本的，判断当前文本节点内容中是否包含{{}}
    compileText(node){
        let content = node.textContent;
        if(/\{\{(.+?)\}\}/.test(content)){
            CompileUtil['text'](node,content,this.vm);
        }
    }
    // 是否是指令，例如v-model
    isDirective(attrName){
        return attrName.startsWith('v-');
    }
}

CompileUtil = {
    setValue(vm,expr,value){
        expr.split('.').reduce((data,current,index,arr)=>{
            if(index == arr.length-1){
                data[current] = value;
            }
            return data[current];
        },vm.$data)
    },
    // node是节点 expr是表达式（school.name） vm是当前实例(vm.$data)
    model(node,expr,vm){
        // 给输入框加一个观察者，如果稍后数据更新了，会触发此方法，给输入框赋予新值
        new Watcher(vm,expr,(newVal)=>{
            node.value = newVal;
        });
        node.addEventListener('input',(e)=>{
            let value = e.target.value; // 获取用户输入的内容
            this.setValue(vm,expr,value);
        })
        // 给输入框赋予value属性 node.value = xxx
        let value = this.getVal(vm,expr);
        node.value = value;
    },

    getContentValue(vm,expr){
        // 遍历表达式,将内容替换成一个新的完整的内容返还回去
        return expr.replace(/\{\{(.+?)\}\}/g,(...args)=>{
            return this.getVal(vm,args[1]);
        })
    },

    text(node,expr,vm){
        // expr => {{a}} {{b}} {{c}}
        let content = expr.replace(/\{\{(.+?)\}\}/g,(...args)=>{
            // 给表达式每个{{}}都加上观察者
            new Watcher(vm,args[1],()=>{
                node.textContent = this.getContentValue(vm,expr) 
            })
            return this.getVal(vm,args[1]);
        })
        node.textContent = content;
    },
    // 给一个expr，就去vm中取到对应的值
    getVal(vm,expr){
        // 'school.name' => ['school','name']
        // vm.$data['school']['name']
        return expr.split('.').reduce((data,current)=>{
            return data[current];
        },vm.$data)
    }
}

// 实现数据劫持
class Observer{
    constructor(data){
        this.observer(data);
    }
    observer(data){
        // 如果是对象才观察
        if(data && typeof data == 'object'){
            for(let key in data){
                this.defineReactive(data,key,data[key]);
            }
        }
    }
    defineReactive(obj,key,value){
        this.observer(value);
        // 给每一个属性都加上一个具有发布订阅功能
        let dep = new Dep();
        Object.defineProperty(obj,key,{
            get(){
                // 创建watcher时，会取到对应的内容，并把watcher放到了全局上
                Dep.target && dep.addSub(Dep.target);
                return value
            },
            set:(newVal)=>{    // {school:{name:'vanlus'}} => this.school={} => {school:{}}
                if(newVal != value){
                    this.observer(newVal);
                    value = newVal;
                    dep.notify();
                }
            }
        })
    }
}

// 观察者（发布订阅）
class Watcher{
    constructor(vm,expr,cb){
        this.vm = vm;
        this.expr = expr;
        this.cb = cb;
        // 默认先存放一个老值
        this.oldValue = this.get();
    }
    // 因为数据劫持，这里一取值就会调数据劫持中的get()方法
    get(){
        Dep.target = this;
        // 取值，然后把观察者和数据关联起来
        let value = CompileUtil.getVal(this.vm,this.expr);
        Dep.target = null;
        return value;
    }
    // 更新操作，数据变化后，会调用观察者的update方法
    update(){
        let newVal = CompileUtil.getVal(this.vm,this.expr);
        if(newVal != this.oldValue){
            this.cb(newVal);
        }
    }
}

class Dep{
    constructor(){
        this.subs = []; //存放所有的watcher
    }
    // 添加订阅
    addSub(watcher){
        this.subs.push(watcher);
    }
    // 发布
    notify(){
        this.subs.forEach(watcher=>watcher.update());
    }
}
```