---
uuid: d2197eb4-bf89-aab2-dd1e-ddc2ba77c69c
title: Typescript基础学习
categories: 技术
tags: 
- Typescript
---
TypeScript 可以使用 JavaScript 中的所有代码和编码概念，TypeScript 是为了使 JavaScript 的开发变得更加容易而创建的。例如，TypeScript 使用类型和接口等概念来描述正在使用的数据，这使开发人员能够快速检测错误并调试应用程序。

> TypeScript 正在成为开发大型编码项目的有力工具。因为其面向对象编程语言的结构保持了代码的清洁、一致和简单的调试。因此在应对大型开发项目时，使用 TypeScript 更加合适。如果有一个相对较小的编码项目，似乎没有必要使用 TypeScript，只需使用灵活的 JavaScript 即可。

## TypeScript 具有以下特点
- TypeScript 是 Microsoft 推出的开源语言，使用 Apache 授权协议
- TypeScript 增加了静态类型、类、模块、接口和类型注解
- TypeScript 可用于开发大型的应用
- TypeScript 易学易于理解

## TypeScript 具有以下优点
- 1.静态输入。静态类型化是一种功能，可以在开发人员编写脚本时检测错误。查找并修复错误是当今开发团队的迫切需求。有了这项功能，就会允许开发人员编写更健壮的代码并对其进行维护，以便使得代码质量更好、更清晰。
- 2.大型的开发项目。有时为了改进开发项目，需要对代码库进行小的增量更改。这些小小的变化可能会产生严重的、意想不到的后果，因此有必要撤销这些变化。使用TypeScript工具来进行重构更变的容易、快捷。
- 3.更好的协作。当发开大型项目时，会有许多开发人员，此时乱码和错误的机也会增加。类型安全是一种在编码期间检测错误的功能，而不是在编译项目时检测错误。这为开发团队创建了一个更高效的编码和调试过程。
- 4.更强的生产力。干净的 ECMAScript 6 代码，自动完成和动态输入等因素有助于提高开发人员的工作效率。这些功能也有助于编译器创建优化的代码。

## 枚举类型
如果在程序中能灵活的使用枚举(`enum`),会让程序有更好的可读性。
枚举类型使用：
```ts
enum Grade {
    PRIMARY,
    MIDDLE,
    HIGH
}

function getGrade(grade:any){
    if(grade == Grade.PRIMARY){
        return '小学';
    }else if(grade == Grade.MIDDLE){
        return '中学';
    }else{
        return '大学';
    }
}

const result = getGrade(Grade.PRIMARY);
console.log(result);
```
也可以这样传值或者调值：
```ts
console.log(getGrade(0),Grade.MIDDLE, Grade[2]);
// 小学 1 HIGH
```
这看起来很神奇，这是因为枚举类型是有对应的数字值的，`默认`是从 0 开始的。那这时候不想默认从 0 开始，而是想从 1 开始。可以这样写。
```ts
enum Grade {
    PRIMARY = 1,
    MIDDLE,
    HIGH
}
```

未完待续 ：D