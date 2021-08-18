---
title: Typescript 最佳实践
date: 2021-08-18 09:38:14
tags: [Typescript, 最佳实践]
categories: Typescript

---

# 如何处理第三方库类型相关的问题

在 TS 项目中使用一些第三方库的时候，经常遇到第三方库是使用 JS 实现的，并且库内部并没有类型声明文件。

![https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/Typescript/typescript-0.png](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/Typescript/typescript-0.png)

解决方案：

1. 如果有 @type 相关库，可以直接引入使用
2. 自己声明一下

```ts
// libs.d.ts

declare module "qrcode"
```

# 联合类型数据问题解决

![https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/Typescript/typescript-1.png](https://raw.githubusercontent.com/fangmd/markdownphoto/master/src/Typescript/typescript-1.png)

>报错信息：Object is possibly 'undefined'.ts(2532)


解决方案1：类型强转

```ts
// 类型强转
function add(one: number, two: number | undefined): number {
  return one + (two as number)
}
```

解决方案2：使用 typeof, in, instanceof 做类型判断

```ts
// 类型判断
function add3(one: number, two: number | undefined): number | undefined {
  if (typeof two === "number") {
    return one + two
  }
}
```

解决方案3: !断言，人为判断数据不会是 null|undefined

```ts
function add3(one: number, two: number | undefined): number | undefined {
  return one + two!
}
```








# 问题

1. tsc 没有打包 node_module 文件夹里面的内容
