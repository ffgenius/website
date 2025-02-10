---
title: 类型体操初学者指南：轻松上手
date: '2024-11-13T09:00:00.000+00:00'
lang: zh
duration: 17min
---
[[toc]]

## 前言

> 老祖：我教你 C#、Java 各种数据库和底层协议，以后做个后端大拿。
>
> 吗喽：不学，不学。
>
> 老祖：我教你 Python , C ++，神经网路，机器学习，数据分析和统计，以后做个 AI 专家。
>
> 吗喽：不学，不学。
>
> 老祖：我教你黑客技术、木马投放，病毒设计、解密秘诀，外带赠送各大银行的安全漏洞
>
> 吗喽：不学，不学。
>
> 老祖在吗喽后脑上打了三下，吗喽眼珠乱转，明白了其中奥妙。
>
> 半夜，吗喽跑到老祖房间，老祖小声说：我教你  TypeScript 类型体操，学习各种高级类型技巧，保你在前端圈呼风唤雨。
>
> 吗喽：对对对，我就想学这个。我以后该怎么谢你？
>
> 老祖：说什么谢不谢的，以后出去别说你是写  TypeScript 就行。
>
![wukong.png](/images/type-challenges-learn-wukong.jpg)

`TS(TypeScript)` 类型体操是指利用 `TS` 强大类型系统来进行复杂的类型操作以实现某些特定的功能或解决特定问题的过程。众所周知，`TS` 是 `JS` 的超集，何谓 `超集`，这意味着所有的 `JS` 代码在 `TS`上都是有效的，而对于 `TS`，它新增了类型检查和一些高级类型的操作（对类型进行逻辑运算）。

学习 `TS` 类型体操并不是强制性的，但确实有助于深入理解类型运算和提升类型系统的运用能力。通常来说，我们只需学习基本的类型操作，而不需要深入到过于复杂的类型体操中。

接下来，我们来学习几个 `TypeScript` 类型体操的小技巧，以及在 `TS` 内置函数中是如何使用这些技巧的，帮助你提升 `TS` 的使用能力。

## 技巧

### 📝 extends （条件类型）

在 `JS` 中我们使用 `if` 或 `三元表达式` 来判断条件是否符合，继而执行某些操作，例如：

```js
// if 条件判断
if (x > 0) { return 'true' }

// 三元表达式
let result = x > 0 ? 'true' : 'false'
```

在 `TS` 的类型运算中虽然没有给我们提供 `if` 作为条件判断，但是给我们提供了一种特殊的 `三元表达式` ，为什么说是特殊的呢？这是因为 `TS` 中的三元表达式通常需要伴随着 `extends` 关键字。

```ts
// 语法
type T = U extends Y ? X : Z // 表示为 U 是否被包含于 Y，如果是则返回 X，反之返回 Z
```

就这？这也太简单了吧。那什么叫做包含呢？

```ts
// 子类型被包含于父类型
type A1 = 123 extends number ? 'Y' : 'N' // Y
type A2 = '123' extends string ? 'Y' : 'N' // Y
type A3 = true extends boolean ? 'Y' : 'N' // Y
type A4 = [1] extends number[] ? 'Y' : 'N' // Y
type A5 = 123 extends 123 | 456 ? 'Y' : 'N' // Y
type A6 = 123 | 456 extends 123 ? 'Y' : 'N' // N 123 | 456 的类型更广泛，因此不被包含于 123

// 类型全等同样也成立
type A7 = '123' extends '123' ? 'Y' : 'N' // Y
type A8 = 123 extends 123 ? 'Y' : 'N' // Y
type A9 = true extends true ? 'Y' : 'N' // Y

// 特殊的装箱类型。主要是指将原始类型（如 number、string、boolean）包装成对应的对象类型。这些对象类型提供了更多的方法和属性，以便于操作和处理原始类型的数据。
// number 和 Number
type A10 = number extends Number ? 'Y' : 'N' // Y
type A11 = 123 extends Number ? 'Y' : 'N' // Y
// string 和 String
type A12 = string extends String ? 'Y' : 'N' // Y
type A13 = '123' extends String ? 'Y' : 'N' // Y
// boolean 和 Boolean
type A14 = boolean extends Boolean ? 'Y' : 'N' // Y
type A15 = true extends Boolean ? 'Y' : 'N' // Y
// {} object Object 令人头大的三个家伙，在这里简单梳理一下他们的关系
// Stirng Number Boolean 等装箱类型都包含于 {} Object object
type A15 = String extends {} ? 'Y' : 'N' // Y
type A16 = String extends Object ? 'Y' : 'N' // Y
type A17 = String extends object ? 'Y' : 'N' // Y

// 顶层类型（any、unknown）
// 任何类型都包含于顶层类型，可以理解为顶层类型涵盖了所有的类型
type A18 = number extends any ? 'Y' : 'N' // Y
type A19 = string extends unknown ? 'Y' : 'N' // Y
// 对于 any 来说，他是松散的类型，因此它可能包含于其他类型或者不包含
type A20 = any extends number ? 'Y' : 'N' // Y | N
// 而对于 unknown 来说，他是严格的，他不包含于任何类型。因此，在开发当中为了保证代码的安全性，我们通常使用 unknown 来代替 any
type A21 = unknown extends string ? 'Y' : 'N' // N

// 底层类型 （never）
// 底层类型不包含于任何类型，而被其他类型所包含，因为他是最窄的类型，用于不会发生的类型
type A22 = number extends never ? 'Y' : 'N' // N
type A23 = never extends number ? 'Y' : 'N' // Y

```

上述就是类型与类型之间的包含关系了，看似很复杂，其实他们之间的关系很容易捋清楚。总结概括为：全等包含于全等；小类型（窄类型）被包含于大类型（宽类型）。

在 `TS` 的内置函数中基本所有的函数都使用到了 `extends` ，让我们来简单的看两个内置函数的实现：

**`Exclude<UnionType, ExcludedMembers>`**

`Exclude` 会将 `ExcludedMembers` 类型从 `UnionType` 类型中所剔除。

```ts
type T1 = Exclude<'a' | 'b' | 'c', 'a'> // 'b' | 'c'
type T2 = Exclude<'a' | 'b' | 'c', 'a' | 'b'> // 'c'
type T3 = Exclude<string|(() => void), Function> // string
type T4 = Exclude<string | string[], any[]> // string
type T5 = Exclude<(() => void) | null, Function> // null
type T6 = Exclude<200 | 400, 200 | 201> // 400
type T7 = Exclude<number, boolean> // number

// 实现 
type Exclude<T, U> = T extends U ? never : T
```

**`Extract<Type, Union>`**

`Extract` 会取出 `Type` 类型中指定的 `Union`，如果找不到该类型则返回 `never`。

```ts
type T1 = Extract<'a' | 'b' | 'c', 'a'> // 'a'
type T2 = Extract<'a' | 'b' | 'c', 'a' | 'b'> // 'a' | 'b'
type T3 = Extract<'a' | 'b' | 'c', 'a' | 'd'> // 'a'
type T4 = Extract<string | string[], any[]> // string[]
type T5 = Extract<(() => void) | null, Function> // () => void
type T6 = Extract<200 | 400, 200 | 201> // 200

// 实现
type Extract<T, U> = T extends U ? T : never
```

原来内置函数的实现，这么简单，真是我上我也行！

### 📝 分布式条件类型

相信大家已经彻底弄懂了 `extends` 条件类型。但是大家是不是有个疑问：在 `Exclude` 的实现中 `type Exclude<T, U> = T extends U ? never : T` ，当 `T` 的类型为 `'a' | 'b' | 'c'` ，`U` 的类型为 `a` 时，不是说范围更广的类型为大类型，而类型较窄的类型为小类型吗？那么：

```ts
type A = 'a' | 'b' | 'c' extends 'a' ? 'Y' : 'N'
```

这里 `A` 的类型应该是 `'N'` ，这样的话，`type A = Exclude<'a' | 'b' | 'c', 'a'>` 的类型应该是 `never` 。

其实这里就涉及到了 `分布式条件类型` ：**它允许我们对联合类型中的每个成员单独应用类型转换。**

也就是 `TS` 在内部处理联合类型时，并不是单纯的直接 `'a' | 'b' | 'c' extends 'a'` 处理，而是转化成：

```ts
// type T1 = Exclude<'a' | 'b' | 'c', 'a'> // 'b' | 'c'
type A = ('a' extends 'a' ? never : 'a') | ('b' extends 'a' ? never : 'b') | ('c' extends 'a' ? never : 'c') // never | 'b' | 'c'
```

我们曾提及到 `never` 类型表示并不存在的类型，因此上述 `A` 的类型为 `'b' | 'c'`。

举一反三：

```ts
// type T2 = Exclude<'a' | 'b' | 'c', 'a' | 'b'> // 'c'
type A = ('a' extends 'a' | 'b' ? never : 'a') | ('b' extends 'a' | 'b' ? never : 'b') | ('c' extends 'a' | 'b' ? never : 'c') // never | never | 'c'
```

相同的，忽略 `never` 后，最后的类型为 `'c'`。讲到这里，相信大家对条件类型有了更深入的理解呢！

### 📝 infer

`infer` 是 `TypeScript` 中用于类型推断的关键字，主要用于在条件类型中从复杂类型中提取子类型。`infer` 允许你在类型操作过程中捕获并使用某个类型的一部分。

```ts
type Infer<T> = T extends Array<infer Item> ? Item : never

type ITest1 = Infer<number[]> // number
type ITest2 = Infer<string> // never
```

`infer Item` 表示你并不需要定义 `Item` 的类型，完全由 `TS` 推断，可以帮助我们捕获一些内部的子类型。让我们来看一个，`TS` 的内置函数 `ReturnType<Type>`。

**`ReturnType<Type>`** 顾名思义就是取出 `Type` 类型的返回值。并且对 `Type` 做了一个限制，那就是 `Type` 必须是一个函数类型，只有函数类型才有返回值，这个内置函数才有意义。

```ts
type T1 = ReturnType<() => string> // string
type T2 = ReturnType<() => {
  a: string; b: number
}> // { a: string; b: number }
type T3 = ReturnType<(s:string) => void> // void
type T4 = ReturnType<() => () => any[]> // () => any[]
type T5 = ReturnType<typeof Math.random> // number
type T6 = ReturnType<typeof Array.isArray> // boolean

// 实现
type ReturnType<
  T extends (...args: any) => any // 对泛型 T 做了限制，他必须是一个函数类型
> =
  T extends (...args: any) => infer R ? R : any // 使用了 infer R 让 TS 自动捕获到了该函数的返回类型，如果表达式成立则返回 R 类型。
```

**值得注意的是：在 `extends` 篇幅中我们了解到了 `any` 类型是被所有类型所包含的，因此 `ReturnType<any>` 当该方法传入一个 `any` 类型，`TS` 也是会将他推导为一个函数类型。细心的小伙伴就发现了，`T extends (...args: any) => infer R ? R : any`，`any` 类型虽然可以表示任何类型的值，但它没有具体的结构。这意味着 `any` 不能提供足够的信息来推断 `R`。因此表达式不成立返回了 `any`**。

### 📝 keyof

`keyof` 是一个类型操作符，用于获取对象类型的所有键的联合类型。`keyof` 可以帮助你创建更强大和灵活的类型，特别是在处理映射类型和条件类型时非常有用。

由于 `keyof` 的用法简单，并且常常需要搭配其他技巧使用，这里就简单介绍一下使用方法：

```ts
type Obj = { 
    name: string,
    age: number
}
type KeyObj = keyof Obj // 'name' | 'age'

interface T {
  0: boolean,
  a: string,
  b(): void
}

type KeyT = keyof T // 0 | 'a' | 'b'

// 特殊用法 上述提到 any 可以代表任何类型，TS 帮我们推断出在这里是一个 objce 的类型，而 object 对象的 key 值可以包括 number string symbol，因此得到如下结果
type KeyObject = keyof any // string | number | symbol
```

### 📝 in

`in` 好熟悉，我在哪听过？原来世界上最伟大的语言 `JavaScript` 中也有这个运算符。在 `JS` 中 我们通常使用 `in` 来检查一个元素是否在对象，数组或者类中存在，例如：

```js
const obj = { name: 'zs' }
if ('name' in obj) { console.log('JavaScript new bee') }
```

在 `TS` 中，`in` 关键字可以创建映射类型，映射类型允许你基于现有的类型或键值集合动态地创建新的类型，听着有点难懂，那就不听，简单来看一个例子：

```ts
type T = 'a' | 'b' | 'c' 
type Obj = { [props in T]: number }
// 简单的理解为: [props in T] 遍历了所有 T 中的类型，而 props 则是每个类型的 key, 最后组成一个类型。
// 等同于
type Obj = {
    'a': number,
    'b': number,
    'c': number
}
```

```ts
type T = {
    name: string,
    age: number
}
// 如果 T 的类型不再是简单的联合类型，而是一个对象类型，那么我们需要怎么用来遍历每个 T 中的 key 呢。聪明的小伙伴已经想到了我们上文提到的 keyof。
type Obj = { [props in keyof T]: T[props] } // 使用 keyof 取出 T 对象的每个键名组成一个联合类型，这样是不是就跟上面的联合类型一样了呢。
```

这次我们来看看哪些 `TS`  的内置函数使用到了 `in` 来实现呢。

**`Pick<Type, Keys>`**

`Pick<Type, Keys>` 返回一个新的对象类型，选中的类型是在 `Type` 对象类型中，`key` 值包含在 `Keys` 中的对象。

```ts
interface A {
  x: number,
  y: number
}
type T1 = Pick<A, 'x'> // { x: number }
type T2 = Pick<A, 'y'> // { y: number }
type T3 = Pick<A, 'x' | 'y'>  // { x: number; y: number }
```

```ts
// 实现
type Pick<
    T, K extends keyof T // 允许传入两个类型，T 为我们需要从中挑选的对象类型，K 为我们需要挑选出来的目标键名，并且限制 K 的类型只能是 T 对象类型的键名组成的一个联合类型
> = {
  [P in K]: T[P] // 使用 in 映射我们需要的 key，value 为 T[P]
}
```

**`Record<Keys, Type>`**

`Record<Keys, Type>` 返回一个对象类型，参数 `Keys` 用作键名，参数 `Type` 用作键值类型。

```ts
type T = Record<'a', number> // { a: number }
type T = Record<'a' | 'b', number> // { a: number, b: number }
type T = Record<'a', number | string> // { a: number | string }

// 实现
type Record<
    K extends string | number | symbol, T // K 是用作键名，还记得我们上面提过，对象的键名 只能是 string|number|symbol ，因此我们使用 extends 限制 K 的类型，而对于对象的键值来说，没有明确的类型要求，因此泛型 T 不做限制。
>
  = { [P in K]: T } // 相信大家也都明白这里的含义了
```

### 📝 映射类型中的修饰符

上述中我们使用了 `in` 来生成新的映射类型，在这过程中可以使用修饰符来控制生成的属性的行为。

*   **`-?` 和 `+?`**：控制属性的可选性
  *   `-?`：移除属性的可选性。
  *   `+?`：添加属性的可选性。
*   **`-readonly` 和 `+readonly`**：控制属性的只读性
  *   `-readonly`：移除属性的只读性。
  *   `+readonly`：添加属性的只读性。

这里就不过多赘述属性的可选性以及只读性，如果不了解的小伙伴可以参考文末的链接进行学习。

在 `TS` 的内置方法中有以下方法使用到了属性修饰符：

**`Readonly<Type>`**

将所有的属性设置为只读

```ts
interface A {
  x: number,
  y?: number
}

type T = Readonly<A> // { readonly x: number; readonly y?: number; }

// 实现
type Readonly<T> = {
  readonly [P in keyof T]: T[P]
}
```

**`Required<Type>`**

将所有的属性设置为必选

```ts
interface A {
  x?: number,
  y: number
}

type T = Required<A> // { x: number; y: number; }

// 实现
type Required<T> = {
  [P in keyof T]-?: T[P]
}
```

**`Partial<Type>`**

将所有属性设置为可选

```ts
interface A {
  x: number,
  y: number
}
 
type T = Partial<A> // { x?: number; y?: number; }

type Partial<T> = {
  [P in keyof T]?: T[P]
}
```

**`ReadonlyArray<Type>`**

用来生成一个只读数组。只读意味着我们不能对数组的元素进行新增、删除或者修改（数组长度不可变，并且不能修改数组元素）。换句话说，我们不能对数组进行 `push` `pop` `splice` 等会改变数组长度的数组方法，也不能对数组元素重新赋值。

```ts
const values: ReadonlyArray<string> = ['a', 'b', 'c']

values[0] = 'x' // 报错
values.push('x') // 报错
values.pop() // 报错
values.splice(1, 1) // 报错

// 实现
interface ReadonlyArray<T> {
  readonly length: number // 对数组的 length 设置为只读，因此数组长度不可改变
  readonly [n: number]: T // 对数组元素设置为只读，因此数组元素不可修改
}
```

### 📝 递归

在这里我们来做个小小的体操：设计一个类型函数，他用来取出 `Promise` 的返回类型。

```ts
// 示例
Promise<string> // 返回类型为 string
Promise<number> // 返回类型为 number
```

相信大家立马能联想到 `ReturnType` 这个类型方法，我们需要使用关键字 `infer` 来捕获到他的返回类型。

```ts
// 看似这个函数有亿点点复杂，让我们来逐步分解一下
type Awaited<T> =
  T extends null | undefined ? T : // 判断传入的 T 泛型是否为 null 或者 undefined，如果是则直接返回 T ，或者进入下一个三元表达式判断
  T extends object & {
    then(
      onfulfilled: infer F,
      ...args: infer _
    ): any // 当 T 不为 null 或者 undefined 时，判断 T 是否为一个 promise 类型，主要的判断方法是，T 必须是一个 object 类型，并且（&）T 类型必须要有个 then() 方法，并且 then(onfulfilled, ...args) 方法必须要有一个 onfulfilled 参数，以及 0 个或者 多个的剩余参数。这个判断符合了 promise A+ 规范，想要继续深入的伙伴可以自行学习。
  } ? F extends (
    value: infer V,
    ...args: infer _
  ) => any ? V : never: // 当 T 满足了 promise A+ 规范，证明了我们传进来的参数是一个 promise，继而判断我们刚刚使用 infer 推断出来的 F 类型。同样的当 F 满足他是一个 (value, ...args) => any 方法，我们只需继而使用 infer 来推断出第一个参数 value 的类型。满足上述条件，我们只需将类型 V 返回
  T // 当 T 不满足 promise A+ 规范直接返回 T
```

让我们来验证一下：

```ts
type A1 = Awaited<null> // null
type A2 = Awaited<() => void> // () => void
type A3 = Awaited<string> // string
type A4 = Awaited<Promise<number>> // number，成功了这里获取到了 promise 返回的类型
type A5 = Awaited<Promise<Promise<number>>> // Promise<number> 这里好像不太对
```

我们看到 `A5` 的类型为 `Promise<number>` ，可是我们这个函数的作用不就是为了获取 `promise` 的返回类型吗，这里怎么又给我返回了一个 `promise` 类型。聪明的小伙伴已经想到了，把这个返回结果在丢入 `Awaited` 类型函数里面让他把最终的类型返回出来，是个好主意！

```ts
// 那如果我的类型是
type A5 = Awaited<Promise<Promise<Promise<number>>>>
// 甚至是
type A5 = Awaited<Promise<Promise<Promise<Promise<number>>>>>
// 甚至甚至是
type A5 = Awaited<Promise<Promise<Promise<Promise<Promise<number>>>>>>
// 逐渐离谱....
```

这是时候有小伙伴站出来了，递归，递归，使用递归！没错，我们的思路就是当返回的类型还是 `promise` 类型时，我们递归使用 `Awaited` 直至最后的结果返回。

```ts
type Awaited<T> =
  T extends null | undefined ? T :
  T extends object & {
    then(
      onfulfilled: infer F,
      ...args: infer _
    ): any;
  } ? F extends (
    value: infer V,
    ...args: infer _
  ) => any ? Awaited<V> : never: // 当我们使用 V 推断出了一层 promise 的结果后，我们继续丢入 Awaited，他又会重新进行三元表达式判断如果 V 不是 promise 直接返回结果，如果还是继续递归。
  T
```

最后的最后，让我们在来验证一下：

```ts
type A5 = Awaited<Promise<Promise<Promise<number>>>> // number
type A6 = Awaited<Promise<Promise<Promise<Promise<number>>>>> // number
type A7 = Awaited<Promise<Promise<Promise<Promise<Promise<number>>>>>> // number
```

这下没问题了，`Awaited` 也是一个 `TS` 内置的类型函数，恭喜大家又学会一个。

**递归是类型体操一个重要的环节，当我们的类型对象嵌套时，我们需要取出他最后一层的类型就可以考虑递归。递归对于萌新小伙伴来说可能是一个比较困难的知识点，不了解的小伙伴可以多去找一些资料学习学习。**

### 📝 模板字符串

最后一个小技巧，我们来讲点简单的。模板字符串大家都在 `JS` 里面听说过了，没错这里还是那个模板字符串。

```ts
type A = `hello ${string}` // 这里定义了一个 A 类型为 hello xxx；灵活的定义了 A 的类型
const A1: A = 'hello world'
const A2: A = 'hello 666'

type B = `hello ${number}`
const B1: B = 'hello world' // 报错，因为 world 不是一个数字类型
const B2: B = 'hello 666'
```

当然，我们允许模板字符串与 `infer` 一起使用：

```ts
type Include<
  Str extends string
> = Str extends `${infer _R1}${string}` ? _R1 : never

type A = Include<'Hello world'> // 'H' 这里推导出来的类型为第一个字符 H
type B = Include<'TypeScript'> // 'T'
```

我们可以使用模板字符串与 `infer` 来实现对字符串或者数组的替换、切割、组合等一系列操作。

## 三元表达式

![pic1.png](/images/type-challenges-learn-pic1.png)

![pic2.png](/images/type-challenges-learn-pic2.png)

这里是两个在 `vue3` 源码里定义的类型，不少小伙伴一看直呼**妖秀啊**，这是什么鬼，密密麻麻一大坨。其实大部分的类型运算并不困难，大家觉得困难的是嵌套了太多层的三元表达式，导致结构混乱，难以阅读。例如我们在递归中提到的 `Awaited` 本质上是不难的，只要理清楚里面三元表达式的嵌套，我们就能弄清这个类型函数的含义，这里对大家理解三元表达式有几个不太成熟的建议：

*   将三元表达式转为 `IF` 结构化语言，`IF` 嵌套是大家公认的屎山，但却也是可读性最好的编程。
*   将嵌套的三元表达式拆分成每一个单独的三元表达式。
*   一层一层的分析。对于我而言，我喜欢先将代码复制出去一份，然后从外层逐层理解。理解完第一层然后在将第二层的三元复制进去，逐层攻破。

## 总结

通过这些技巧，希望能够帮助大家更好地学习 `TypeScript`。当然，最好的学习方法就是实际使用它。

对于在 `TypeScript` 有没有必要学习，我的认为是有的：

*   工作：在工作中，技术选型通常由团队领导或架构师决定。因此，我们可能会被要求使用 `TypeScript`。掌握 `TypeScript` 可以帮助我们更好地适应项目需求，提高工作效率。
*   未来：对于很多开发者来说，希望不断提升自己，进入大型科技公司或担任更高级别的职位。在这些公司和职位中，`TypeScript` 是不可或缺的技能之一。学习 `TypeScript` 可以为你的职业生涯打开更多的大门。
*   开源：如果你想参与开源项目或主导一个项目的开发，使用 `TypeScript` 可以显著提升代码的质量和安全性。这不仅有助于项目的长期维护，也能吸引更多开发者和用户加入。

当然，如果以上三点与你没有直接联系，那么学习 `TypeScript` 你可以更深入地理解面向对象编程的概念和最佳实践，也可以成为你茶余饭后吹嘘的资本。

**文末链接**

*   [文章部分代码参考 阮一峰 TypeScript 教程](https://wangdoc.com/typescript)
*   [如果大家想对 TS 有更深入的学习了解，可以学习 TypeScript 进阶参考指南](https://juejin.cn/book/7086408430491172901?enter_from=search_result\&utm_source=search)
*   [想练习更多的 TypeScript 类型体操，当然是推荐托尼大神的 typeChallenges](https://github.com/type-challenges/type-challenges)
