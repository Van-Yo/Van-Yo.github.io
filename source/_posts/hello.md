---
uuid: 8e5fd5eb-9476-8aad-3d8f-5c95ab961bbc
title: 前端面试必问的JS基础知识——面向对象
categories: 技术
tags: Javascript
---
## 前言
之前面试过几家公司，虽然算不上大厂，但是对js基础知识还是挺重视的。其中面试官就问了有关面向对象的几个问题，例如，1.谈谈你对面向对象的认识？2.面向对象有三大特性，是哪些，并着重介绍一下。我发现平时工作之余，太过于注重物业逻辑和框架，导致`面向对象`就几个字都成了最熟悉的陌生人，感觉有点印象，但又不确定，生怕说错了还给面试官留下不好的印象，所以写一个简短的博客，方便自己每次看到能勾起这段面试尴尬的回忆，和警示自己，重视基础！

了解过面向对象的同学应该都知道，面向对象三个基本特征是：`封装`、`继承`、`多态`，但是对于这三个词具体可能不太了解。对于前端来讲接触最多的可能就是封装与继承，对于多态来说可能就不是那么了解了。
## 封装
> 将对象运行所需的资源封装在程序对象中——基本上，是方法和数据。对象是“公布其接口”。其他附加到这些接口上的对象不需要关心对象实现的方法即可使用这个对象。这个概念就是“不要告诉我你是怎么做的，只要做就可以了。”对象可以看作是一个自我包含的原子。对象接口包括了公共的方法和初始化数据。

说白了，封装就是新建一个容器，容器中定义一些属性和方法，供对象使用。
- 类：封装对象的属性和行为
- 方法：封装具体逻辑功能
- 访问封装：访问修饰封装无非就是对其访问权限进行封装

注：下面的代码都是基于ES6类的语法糖class来演示的。

```js
class Employees {
    constructor(name,age){
        this.name = name;
        this.age = age;
    }
    getInfo(){
        let {name,age} = this;
        return {name,age};
    }
    static seyHi(){
        console.log("Hi");   
    }
}

let lisi = new Employees("Aaron",18);
lisi.seyHi();   // 报错 lisi.seyHi is not a function
lisi.getInfo();  // {name: "Aaron", age: 18}
Employees.seyHi();  // Hi
```
在Employees中抽出的`公共属性`有name,age,`公共方法`有getInfo,seyHi，然而getInfo与seyHi所不同的是seyHi使用了`static修饰符`，改变其为`静态方法`，seyHi`只属于`Employees这个类。然而getInfo方法则是`属于实例`的。这里使用了`static`对seyHi方法对其进行了`访问权限`的封装。再举一个Promise的例子：
```js
// .then()是公共方法，属于实例
Promise.then()  //  报错 Promise.then is not a function
let p1 = new Promise(() => {})
p1.then();  //  Promise {<pending>}

// .all()是静态方法，只属于Promise类
Promise.all([1]);   //  Promise {<resolved>: Array(1)}
```
从上面的代码中可以看出`Promise`也使用了`static`对其方法的访问权限进行了封装。
## 继承
> 继承可以使得子类具有父类的各种的公有属性和公有方法。而不需要再次编写相同的代码。

子类`继承`父类后，`子类具有父类属性和方法`，然而也同样具备自己所独有的属性和方法，也就是说，子类的功能要比父类多或相同，不会比父类少。
```js
class Employees {
    constructor(name){
        this.name = name;
    }
    getName(){
        console.log(this.name)
    }
    static seyHi(){
        console.log("Hi");   
    }
}
class Java extends Employees{
    constructor(name){
        super(name);
    }
    work(){
        console.log("做后台工作");
    }
}
let java = new Java("Aaron");
java.getName();  // Aaron
java.work();   // 做后台工作
// java.seyHi();    //  报错 java.seyHi is not a function
```
从上面的例子可以看出继承`不会继承`父类的`静态方法`，`只会继承父类的公有属性与方法`。这一点需要注意。子类继承之后既拥有了getName方法，同样也拥有自己的worker方法。
## 多态
> 按字面的意思就是“多种状态”，允许将子类类型的指针赋值给父类类型的指针。

说白了多态就是相同的事物，一个接口，多种实现，同时在最初的程序设定时，有可能会根据程序需求的不同，而不确定哪个函数实现，`通过多态不需要修改源代码`，就可以实现一个接口多种解决方案。多态的表现形式`重写`与`重载`。
>  `重写`:子类可继承父类中的方法，而不需要重新编写相同的方法。但有时子类并不想原封不动地继承父类的方法，而是想作一定的修改，这就需要采用方法的重写。方法重写又称方法覆盖。
```js
class Employees {
    constructor(name){
        this.name = name;
    }
    seyHello(){
        console.log("Hello")
    }
    getName(){
        console.log(this.name);
    }
}
class Java extends Employees{
    constructor(name){
        super(name);
    }
    seyHello(){
        console.log(`Hello,我的名字是${this.name},我是做Java工作的。`)
    }
}
const employees = new Employees("Aaron");
const java = new Java("Leo");
employees.seyHello();   //  Hello
java.seyHello();    //  Hello,我的名字是Leo,我是做Java工作的。
employees.getName();    //  Aaron
java.getName(); //  Leo
```
通过上面的代码可以看出Java`继承`了Employees,然而子类与父类中都存在seyHello方法，为了满足不同的需求子类继承父类之后`重写`了seyHello方法。所以在`调用的时候会得到不同的结果`。既然子类继承了父类，子类也同样拥有父类的getName方法。
> `重载`就是函数或者方法有相同的名称，但是参数列表不相同的情形，这样的同名不同参数的函数或者方法之间，互相称之为重载函数或者方法。

因为JavaScript是`没有重载的概念`的所以要自己编写逻辑完成重载。
```js
class Employees {
    constructor(arg){
        let obj = null;
        switch(typeof arg)
        {
            case "string":
                  obj = new StringEmployees(arg);
                  break;
            case "object":
                  obj = new ObjEmployees(ObjEmployees);
                  break;
            case "number":
                obj = new NumberEmployees(ObjEmployees);
                break;
        }
        return obj;
    }
}
class ObjEmployees {
    constructor(arg){
        console.log("ObjEmployees")
    }
}
class StringEmployees {
    constructor(arg){
        console.log("StringEmployees")
    }
}
class NumberEmployees {
    constructor(arg){
        console.log("NumberEmployees")
    }
}
new Employees({})   // ObjEmployees
new Employees("123456") //  StringEmployees
new Employees(987654)   //  NumberEmployees
```
在上面的代码中定义了`Employees`,`ObjEmployees`,`StringEmployees`,`NumberEmployees`类,在实例化Employees的时候在constructor里面进行了`判断`，根据`参数`的不同`返回不同的对应的类`。这样完成了一个简单的类重载。
## 总结
在编程的是多多运用这个写思想对其编程时很有用的，能够使你的代码达到高复用以及可维护。
- 封装可以隐藏实现细节，使得代码模块化；
- 继承可以扩展已存在的代码模块（类），它们的目的都是为了——代码重用。
- 多态就是相同的事物，调用其相同的方法，参数也相同时，但表现的行为却不同。多态分为两种，一种是`行为多态`与`对象的多态`。