---
title: 'Typescript奇怪的符号'
date: 2023-10-30
#description: <descriptive text here>

# weight: 1

# aliases: ["/first"]

tags: ['Typescript奇怪的符号']
categories: ['Typescript']
showToc: true
TocOpen: false
draft: false
hidemeta: false
comments: true
disableShare: false
disableHLJS: false
hideSummary: false
searchHidden: false
ShowReadingTime: true
ShowBreadCrumbs: true
ShowPostNavLinks: true
cover:
image: '<image path/url>' # image path/url
alt: '<alt text>' # alt text
caption: '<text>' # display caption under cover
relative: false # when using page bundles set this to true
hidden: true # only hide on current single page
---

### 1. ! 非空断言操作符

概念：在上下文中当类型检查器无法断定类型时，一个新的后缀表达式操作符 ！可以用于断言操作对象是非 null 和非 undefined 类型。具体而言，x! 将从 x 值域中排除 null 和 undefined。

使用场景：

1.1 忽略 undefined 和 null 类型

```Typescript
function myFunc(maybeString: string | undefined | null) {
  // Type 'string | null | undefined' is not assignable to type 'string'.
  // Type 'undefined' is not assignable to type 'string'.
  const onlyString: string = maybeString; // Error
  const ignoreUndefinedAndNull: string = maybeString!; // Ok
}
```

1.2 调用函数时忽略 undefined 类型

```Typescript
type NumGenerator = () => number;

function myFunc(numGenerator: NumGenerator | undefined) {
  // Object is possibly 'undefined'.(2532)
  // Cannot invoke an object which is possibly 'undefined'.(2722)
  const num1 = numGenerator(); // Error
  const num2 = numGenerator!(); //OK
}
```

注意：因为 ！非空断言操作符会从编译生成的 JavaScript 代码中移除，所以在实际使用的过程中，要特别注意。比如下面这个例子：

```typescript
const a: number | undefined = undefined;
const b: number = a!;
console.log(b);
```

以上 TS 代码会编译生成 ES5 代码：

```javascript
'use strict';
const a = undefined;
const b = a;
console.log(b);
```

虽然在 TS 代码中，我们使用了非空断言，使得 const b:number = a!；语句可以通过 Typescript 类型检查器的检查。但在生成的 ES5 代码中，！非空断言操作符被移除了，所以在浏览器中执行以上代码，在控制台会输出 undefined。

### 2. & 运算符

在 Typescript 中交叉类型是将多个类型合并为一个类型。通过 & 运算符可以将现有的多种类型叠加到一起成为一种类型，它包含了所需的所有类型的特性。

```typescript
type PartialPointX = { x: number };
type Point = PartialPointX & { y: number };

let point: Point = {
  x: 1,
  y: 1,
};
```

2.1 同名基础类型属性的合并

假设在合并多个类型的过程中，刚好出现某些类型存在相同的成员，但对应的类型又不一致，比如：

```typescript
interface X {
  c: string;
  d: string;
}

interface Y {
  c: number;
  e: string;
}

type XY = X & Y;
type YX = Y & X;

let p: XY;
let q: YX;
```

在上面的代码中，接口 X 和接口 Y 都含有一个相同的成员 c，但它们的类型不一致。对于这种情况，此时 XY 类型或 YX 类型中成员 c 的类型是不是可以是 string 或 number 类型呢？看下面的例子：

![](https://oss-images-1310293673.cos.ap-chongqing.myqcloud.com/static/2023/10/2023-10-30/CleanShot-2023-October-30-11-21-42@2x.png)

![](https://oss-images-1310293673.cos.ap-chongqing.myqcloud.com/static/2023/10/2023-10-30/CleanShot-2023-October-30-11-22-25@2x.png)

我们可以看到混入后的成员 c 的类型为 string & number，即成员 c 的类型既可以是 string 类型又可以是 number 类型。很明显这种类型是不存在的，所以混入后成员 c 的类型为 never。

2.2 同名非基础类型属性的合并

在上面示例中。刚好接口 X 和接口 Y 中内部成员 c 的类型都是基本数据类型，那么如果是非基本数据类型的话，又会是什么情形。看例子：

```typescript
interface D {
  d: boolean;
}
interface E {
  e: string;
}
interface F {
  f: number;
}

interface A {
  x: D;
}
interface B {
  x: E;
}
interface C {
  x: F;
}

type ABC = A & B & C;

let abc: ABC = {
  x: {
    d: true,
    e: 'semlinker',
    f: 666,
  },
};

console.log('abc:', abc);
```

控制台输出结果：

![](https://oss-images-1310293673.cos.ap-chongqing.myqcloud.com/static/2023/10/2023-10-30/CleanShot-2023-October-30-11-47-59@2x.png)

### 3. | 分隔符

在 Typescript 中联合类型（Union Types）表示取值可以为多种类型中的一种，联合类型使用 ｜ 分隔每个类型。联合类型通常与 null 或 undefined 一起使用：

```typescript
const sayHello = (name: string | undefined) => {
  /* ... */
};
```

以上案例中 name 的类型是 string | undefined 意味着可以将 string 或 undefined 的值传递给 sayHello 函数。

此外，对于联合类型来说，你可能会遇到以下的用法:

```typescript
let num: 1 | 2 = 1;
type EventNames = 'click' | 'scroll' | 'mousemove';
```

示例中的 1、2、'click' 被称为字面量类型，用来约束趋之只能是某几个值中的一个。

3.1 类型保护

当使用联合类型时，我们必须尽量把当前值的类型收窄为当前值的实际类型，而类型保护就是实现类型收窄的一种手段。

类型保护是可执行运行时检查的一种表达式，用于确保该类型在一定的范围内。换句话说，类型保护可以保证一个字符串是一个字符串，尽管它的值也可以是一个数字。类型保护与特性检测并不是完全不同，其主要思想是尝试检测属性、方法或原型，以确定如何处理值。

目前主要有四种的方式来实现类型保护：

3.1.1 in 关键字

```typescript
interface Admin {
  name: string;
  privileges: string[];
}

interface Employee {
  name: string;
  startData: Date;
}

type UnknownEmployee = Employee | Admin;

function printEmployeeInformation(emp: UnknownEmployee) {
  console.log('Name: ' + emp.name);
  if ('privileges' in emp) {
    console.log('Privileges: ' + emp.privileges);
  }
  if ('startDate' in emp) {
    console.log('Start Date: ' + emp.startDate);
  }
}
```

3.1.2 typeof 关键字

```typescript
function padLeft(value: string, padding: string | number) {
  if (typeof padding === 'number') {
    return Array(padding + 1).join(' ') + value;
  }
  if (typeof padding === 'string') {
    return padding + value;
  }
  throw new Error(`Expected string or number, got '${padding}'.`);
}
```

注意：typeof 类型保护只支持两种形式：typeof v === typename 和 typeof v !== typename,typename 必须是'number'、'string'、'boolean'或'symbol'。但是 TypeScript 并不会阻止你与其它字符串比较，语言不会把那些表达式识别为类型保护。

3.1.3 instanceof 关键字

```typescript
interface Padder {
  getPaddingString(): string;
}

class SpaceRepeatingPadder implements Padder {
  constructor(private numSpaces: number) {}
  getPaddingString() {
    return Array(this.numSpaces + 1).join(' ');
  }
}

class StringPadder implements Padder {
  constructor(private value: string) {}
  getPaddingString() {
    return this.value;
  }
}

let padder: Padder = new SpaceRepeatingPadder(6);

if (padder instanceof SpaceRepeatingPadder) {
  // padder的类型收窄为 'SpaceRepeatingPadder'
}
```

3.1.4 自定义类型保护的类型谓词（type predicate）

```typescript
function isNumber(x: any): x is number {
  return typeof x === 'number';
}

function isString(x: any): x is string {
  return typeof x === 'string';
}
```
