---
title: 'TypeScript 使用技巧（一）'
date: 2024-10-31
description: 枚举类型、新特性 satisfies

tags: ['TypeScript']
categories: ['TypeScript']
showToc: true
TocOpen: true
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
relative: true # when using page bundles set this to true
hidden: true # only hide on current single page
---

## 1. 枚举类型

### 1.1 获取枚举（enum）的键（key）

```typescript
enum MyEnum {
  Key1 = 'Value1',
  Key2 = 'Value2',
  Key3 = 'Value3',
}

type MyEnumKeys = keyof typeof MyEnum;

// MyEnumKeys 的类型为 "Key1" | "Key2" | "Key3"
```

### 1.2 获取枚举（enum）的值（value）

```typescript
enum MyEnum {
  Key1 = 'Value1',
  Key2 = 'Value2',
  Key3 = 'Value3',
}

type MyEnumValues = MyEnum[keyof typeof MyEnum];

// MyEnumValues 的类型为 "Value1" | "Value2" | "Value3"
```

## 2.新特性 satisfies

`satisfies` 运算符提供了一种为值添加类型注解而不丢失值推理的方法。

### 2.1 使用`satisfies`的强类型 URLSearchParams

`satisfies`非常适合强类型化函数，这些函数通常使用更宽松的类型。

在使用 `URLSearchParams` 时，它的参数通常是 `Record<string, string | string[]>` 。这是一种非常松散的类型，并不强制执行任何特性的键。通常情况下，你需要创建一些搜索参数并将它们传递给 URL。因此，这种松散的类型最终会变得相当危险。

这时，`satisfies` 就起作用了。

```typescript
type URLParams = {
  title: string;
  body: string;
};

const params = new URLSearchParams({
  title: 'title',
} satisfies URLParams);
// ❌ 类型 "{ title: string; }" 中缺少属性 "body"，但类型 "URLParams" 中需要该属性。
```

### 2.2 带 satisfies 的强类型 POST 请求

在发出 POST 请求时，向服务器发送正确的数据结构非常重要。服务器会期望请求正文有特定的格式，但使用 JSON.stringify 将其转化为 JSON 的过程会消除任何强类型。

```typescript
type Post = {
  title: string;
  content: string;
};
fetch('/api/posts', {
  method: 'POST',
  body: JSON.stringify({
    title: 'New Post',
    content: 'Lorem ipsum.',
  } satisfies Post),
});
```

我们可以用 Post 类型注释请求正文，确保标题和内容属性存在且类型正确。

### 2.3 使用 satisfies 而不是 as const 来推断元组

通常，你会希望在 TypeScript 中声明一个元素数组，但将其推断为元组而非数组。你可能会使用 `as const` 断言来推断元组类型，而不是数组类型。然后，使用 `satisfies` 操作符，你可以在不使用 `as const` 的情况下获得相同的结果。

```typescript
type MoreThanOneMember = [any, ...any[]];
const array = [1, 2, 3];
const maybeExists = array[3];
const tuple = [1, 2, 3] satisfies MoreThanOneMember;
// ❌ 长度为 "3" 的元组类型 "[number, number, number]" 在索引 "3" 处没有元素。
const doesNotExist = tuple[3];
```

在上面的代码中，我们以两种不同的方式声明数组。如果我们不使用 `satisfies`对其进行注解，它就会被推断为 `number[]`类型。这意味着当我们尝试访问数组中不存在的元素时，TypeScript 不会给出错误信息；他只是将其推断为 `number | undefined`。

然而，当我们使用 `satisfies` 时，TypeScript 会正确推断出数组类型为 `MoreThanOneMember`,一个包含三个元素的元组，并给出错误信息。

### 2.4 使用 `satisfies` 强制 as const 对象成为某种类型。

使用 `as const` 时，我们可以指定将对象视为具有字面类型的不可变值。但是，这并不能强制对象具有任何特定的形状或属性。要强制 `as const` 对象具有特定的形状，我们可以使用 `satisfies` 操作符。

在下面的示例中，我们有一个 `RouteObject` 类型，它代表了一个路由集合。每个路由都有一个字符串类型的 url 属性和一个可选的 `searchParams` 属性。我们要确保路由对象满足 `RouteObject` 类型。

```typescript
type RouteObject = Record<
  string,
  {
    url: string;
    searchParams: Record<string, string | string[]>;
  }
>;

const route = {
  home: {
    url: '/',
    searchParams: {},
  },
  // ❌ 类型 "{ readonly url: "/about"; }" 中缺少属性 "searchParams"，但类型 "{ url: string; searchParams: Record<string, string | string[]>; }" 中需要该属性。
  about: {
    url: '/about',
  },
} as const satisfies RouteObject;
```

### 2.5 使用 `satisfies` 来强制 `as const` 数组成为特定类型。

```typescript
type NavElement = {
  title: string;
  url?: string;
  children?: readonly NavElement[];
};
const nav = [
  {
    title: 'Home',
    url: '/',
  },
  {
    title: 'About',
    children: [
      {
        title: 'Team',
        url: '/about/team',
      },
    ],
  },
] as const satisfies readonly NavElement[];

nav[0].children;
// ❌ 类型“{ readonly title: "Home"; readonly url: "/"; }”上不存在属性“children”。
```
