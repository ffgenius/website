---
title: 'Type gymnastics beginner guide: Easy to Get Started'
date: '2024-11-13T09:00:00.000+00:00'
lang: en
duration: 17min
---
[[toc]]

`TS(TypeScript)` type gymnastics refer to the process of using the powerful `TS` type system to preform complex 
type manipulations in order to achieve specific functions or solve specific problems. As is well-known, 
`TS` is a superset of `JS`, which means that all `JS` code is valid in `TS`. For `TS`, it adds types checking and some
advanced type operations (performing logic operations on types).

Learning `TS` type gymnastics is not mandatory, but it indeed helps in gaining a deeper understanding of type manipulations
and enhancing the proficiency utilizing the type system. Generally, learning basic type operations suffices, and there's 
no need to delve into overly complex type operations.

Let's export a few small tricks of `TypeScript` type gymnastics and how these techniques are used within `TS` build-in
functions, helps you enhance your `TS` usage skills.

## Skills

### 📝 extends (Conditional Types)

In `JS` we use `if` or `ternary expression` to determine if the conditions are met, and then perform certain operations.

```js
// if
if (x > 0) { return 'true' }

// ternary expression
let result = x > 0 ? 'true' : 'false'
```
In Typescript's type operations, although there is no provision for using `if` as a conditional judgment, a special
from of the ternary expression is provided. Why is it considered special? This is because the ternary expression in `TypeScript`
typically require the use of the `extends` keyword.

```ts
// syntax
// Indicate whether U is included in Y, if so return X, otherwise return Z
type T = U extends Y ? X : Z
```

It this is? This is too simple, isn't it. What does is mean to include?

```ts
// Subtypes are included in the supertype.
type A1 = 123 extends number ? 'Y' : 'N' // Y
type A2 = '123' extends string ? 'Y' : 'N' // Y
type A3 = true extends boolean ? 'Y' : 'N' // Y
type A4 = [1] extends number[] ? 'Y' : 'N' // Y
type A5 = 123 extends 123 | 456 ? 'Y' : 'N' // Y
// The types of 123 | 456 are more extendsive and therefore not included in 123
type A6 = 123 | 456 extends 123 ? 'Y' : 'N' // N

// The same applies to type equivalence
type A7 = '123' extends '123' ? 'Y' : 'N' // Y
type A8 = 123 extends 123 ? 'Y' : 'N' // Y
type A9 = true extends true ? 'Y' : 'N' // Y

// Boxed types that contain the methods that programmers associate with those types.
// number and Number
type A10 = number extends Number ? 'Y' : 'N' // Y
type A11 = 123 extends Number ? 'Y' : 'N' // Y
// string and String
type A12 = string extends String ? 'Y' : 'N' // Y
type A13 = '123' extends String ? 'Y' : 'N' // Y
// boolean and Boolean
type A14 = boolean extends Boolean ? 'Y' : 'N' // Y
type A15 = true extends Boolean ? 'Y' : 'N' // Y
// '{}' 'object' 'Object', Here are three guys who make you feel overwhelmed. Let's briefly sort out their relationships.
// 'Stirng' 'Number' 'Boolean' are included in '{}' 'Object' 'object'
type A15 = String extends {} ? 'Y' : 'N' // Y
type A16 = String extends Object ? 'Y' : 'N' // Y
type A17 = String extends object ? 'Y' : 'N' // Y

// top type (any、unknown)
// Any types are included in the top type, which can be understood as the top type covering all types.
type A18 = number extends any ? 'Y' : 'N' // Y
type A19 = string extends unknown ? 'Y' : 'N' // Y
// For 'any', it is a loose type, so it may be included in other types or not included.
type A20 = any extends number ? 'Y' : 'N' // Y | N
// But for 'unknown', it is strict ant not included in any type. therefore, in order to ensure the security
// of the code during development, we usually use 'unknown' instead of 'any'.
type A21 = unknown extends string ? 'Y' : 'N' // N

// bottom type (never)
// bottom type not included in any types, but is contained whihin all types.
type A22 = number extends never ? 'Y' : 'N' // N
type A23 = never extends number ? 'Y' : 'N' // Y

```

The above is the containment relationship between types, which may seem complex, but in fact, the relationship between them
is easy to clarify. The summary can be summarized as follows: 

(1). Tow identical types contain each other. 

(2). Small types(narrow types) are included in large types(wide types)

In the build-in function of `TS`, almost all functions use `extends`. Let's take a brief look at the implementation of 
two built-in functions:

**`Exclude<UnionType, ExcludedMembers>`**

`Exclude` will remove the `ExcludedMembers` type form the `UnionType`.

```ts
type T1 = Exclude<'a' | 'b' | 'c', 'a'> // 'b' | 'c'
type T2 = Exclude<'a' | 'b' | 'c', 'a' | 'b'> // 'c'
type T3 = Exclude<string|(() => void), Function> // string
type T4 = Exclude<string | string[], any[]> // string
type T5 = Exclude<(() => void) | null, Function> // null
type T6 = Exclude<200 | 400, 200 | 201> // 400
type T7 = Exclude<number, boolean> // number

// implement
type Exclude<T, U> = T extends U ? never : T
```

**`Extract<Type, Union>`**

`Extract` will retrieve the `Union` type specified in the `Type` type, and if the type cannot be found, return `never`.

```ts
type T1 = Extract<'a' | 'b' | 'c', 'a'> // 'a'
type T2 = Extract<'a' | 'b' | 'c', 'a' | 'b'> // 'a' | 'b'
type T3 = Extract<'a' | 'b' | 'c', 'a' | 'd'> // 'a'
type T4 = Extract<string | string[], any[]> // string[]
type T5 = Extract<(() => void) | null, Function> // () => void
type T6 = Extract<200 | 400, 200 | 201> // 200

// implement
type Extract<T, U> = T extends U ? T : never
```

### 📝 Distributed Conditional Types

I believe everyone has thoroughly understood the `extends` conditional type. But do you have a question that in the
implementation of `extends`, `type Exclude<T, U> = T extends U ? never : T`, when the type of `T` is `'a' | 'b' | 'c'` and 
the type of `U` is `a`, doesn't is mean that the broader type is the large type, and the narrow type is the smaller type?
So: 

```ts
type A = 'a' | 'b' | 'c' extends 'a' ? 'Y' : 'N'
```

The type of `A` here should be `'N'`, so `type A = Exclude<'a' | 'b' | 'c', 'a'>` should be `never`.

In fact, this involves `Distributed Conditional Types`: **it allows us to apply type conversions separately to each member of the union type**

That is to say, when processing union types internally, `TS` doesn't simply handle them directly as `'a' | 'b' | 'c' extends 'a'`,
but rather converts them into:

```ts
// type T1 = Exclude<'a' | 'b' | 'c', 'a'> // 'b' | 'c'
type A = ('a' extends 'a' ? never : 'a') | ('b' extends 'a' ? never : 'b') | ('c' extends 'a' ? never : 'c') // never | 'b' | 'c'
```

We have mentioned that the `never` type represents a type that does not exist, so the type of `A` is `'b' | 'c'`.

learn by analogy: 

```ts
// type T2 = Exclude<'a' | 'b' | 'c', 'a' | 'b'> // 'c'
type A = ('a' extends 'a' | 'b' ? never : 'a') | ('b' extends 'a' | 'b' ? never : 'b') | ('c' extends 'a' | 'b' ? never : 'c') // never | never | 'c'
```

Similarly, after ignoring `never`, the final type is `'c'`.

### 📝 infer

The `infer` keyword in `TypeScript` is used for type inference, primarily within conditional types to extract a subtypes
from complex types. `infer` allows you to capture and utilize a portion of a type during type manipulations.

```ts
type Infer<T> = T extends Array<infer Item> ? Item : never

type ITest1 = Infer<number[]> // number
type ITest2 = Infer<string> // never
```

`infer Item` means that you don't need to define the type of `Item`, it is entirely inferred by `TS`. which can help us
capture some internal subtypes. Let's take a look at the built-in function `ReturnType<Type>`.

**`ReturnType<Type>`** literally means extracting the return type of the `Type`. It also imposes a constraint on `Type`,
requiring to be a function type, as only function types have return values. This built-in function is meaningful only when
`Type` is a function type.

```ts
type T1 = ReturnType<() => string> // string
type T2 = ReturnType<() => {
  a: string; b: number
}> // { a: string; b: number }
type T3 = ReturnType<(s:string) => void> // void
type T4 = ReturnType<() => () => any[]> // () => any[]
type T5 = ReturnType<typeof Math.random> // number
type T6 = ReturnType<typeof Array.isArray> // boolean

// implment
type ReturnType<
  T extends (...args: any) => any // T must be a function type
> =
  // Using 'infer R' enable 'TS' to automatically capture the return type of the function,
  // and if expression holds, return 'R' type.
  T extends (...args: any) => infer R ? R : any
```

**Notably, in the context of `extends`, we learned that the `any` type is considered a supertype of all type. Therefore,
when `ReturnType<any>` is used, TypeScript treats it as a function. `any` can represent any type of value, but doesn't have 
a specific structure. As a result, the expression does not hold, and `any` is returned**

### 📝 keyof

`keyof` is a type operator that is used to obtain the union type of all the keys of an object type. `keyof` can help you
create more powerful and flexible types, especially when working with mapping types and conditional types.

Due to the simple usage of `keyof` and the frequent need to use it in conjunction with other techniques, here is a brief
introduction its usage:

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

// special case
type KeyObject = keyof any // string | number | symbol
```

### 📝 in

`in` is so familiar, where have I heard of it? Actually, in `JavaScript`, we usually use `in` to check if an element exists
in an object, array or class, for example:

```js
const obj = { name: 'zs' }
if ('name' in obj) { console.log('JavaScript new bee') }
```

In `TS`, the `in` keyword can create mapping types, which allow you to dynamically create new types based on existing
types or key value sets. Let's take a look at an example:

```ts
type T = 'a' | 'b' | 'c' 
type Obj = { [props in T]: number }
// [props in T] travers all types in T, and props is each type's key, and finally forms a type.
// same as
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
// Using 'keyof' to extract each key name of the 'T' object to form a union type
type Obj = { [props in keyof T]: T[props] }
```

This time let's take a look at which built-in functions of TS use in to implement them.

**`Pick<Type, Keys>`**

`Pick<Type, Keys>` return a new object type, pick a key in `Type`

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
// implement
type Pick<
    T, K extends keyof T
> = {
  [P in K]: T[P]
}
```

**`Record<Keys, Type>`**

`Record<Keys, Type>` returns an object type,  with the parameter `Keys` used as the key and the parameter `Type` used as the value.

```ts
type T = Record<'a', number> // { a: number }
type T = Record<'a' | 'b', number> // { a: number, b: number }
type T = Record<'a', number | string> // { a: number | string }

// implement
type Record<
    K extends string | number | symbol, T
>
  = { [P in K]: T }
```

### 📝 Mapped Type's Modifier

上述中我们使用了 `in` 来生成新的映射类型，在这过程中可以使用修饰符来控制生成的属性的行为。
In the above, we used `in` to generate a new mapping type, and you can use modifiers to control the behavior of the 
generated properties. 

  *   **`-?` 和 `+?`**: control the optional property
  *   `-?`: add required property
  *   `+?`: add optional property
  *   **`-readonly` 和 `+readonly`**: control the readonly property
  *   `-readonly`: remove readonly property
  *   `+readonly`: add readonly property


In build-in function there are some functions using modifiers:

**`Readonly<Type>`**

Set all attributes as readonly

```ts
interface A {
  x: number,
  y?: number
}

type T = Readonly<A> // { readonly x: number; readonly y?: number; }

// implement
type Readonly<T> = {
  readonly [P in keyof T]: T[P]
}
```

**`Required<Type>`**

Set all attributes as required

```ts
interface A {
  x?: number,
  y: number
}

type T = Required<A> // { x: number; y: number; }

// implement
type Required<T> = {
  [P in keyof T]-?: T[P]
}
```

**`Partial<Type>`**

Set all attributes as optional

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

Usage to generate a readonly array. Readonly means that we cannot add, delete, or modify the elements of the array
(the length of the array is immutable, and we cannot modify the array elements). In other words, we cannot use array 
methods such as `push`, `pop`, `splice` that will change the length of the array, nor can we reassign array elements.

```ts
const values: ReadonlyArray<string> = ['a', 'b', 'c']

values[0] = 'x' // error
values.push('x') // error
values.pop() // error
values.splice(1, 1) // error

// implement
interface ReadonlyArray<T> {
  readonly length: number
  readonly [n: number]: T
}
```

### 📝 Recursion

Let's do a little exercise: design a type function that uses it to extract the return type of `Promise`.

```ts
// e.g.
Promise<string> // return string
Promise<number> // return number
```

相信大家立马能联想到 `ReturnType` 这个类型方法，我们需要使用关键字 `infer` 来捕获到他的返回类型。
I believe everyone immediately think of the `ReturnType` type method, and we need to use the `infer` keyword to capture its return type.

```ts
// decompose
type Awaited<T> =
  T extends null | undefined ? T : // if T is null or undefined, return T
  T extends object & {
    then(
      onfulfilled: infer F,
      ...args: infer _
    ): any // when T is not null or undefined, we need to determine whether T is a promise type
  } ? F extends (
    value: infer V,
    ...args: infer _
  ) => any ? V : never:
  T
```

verify: 

```ts
type A1 = Awaited<null> // null
type A2 = Awaited<() => void> // () => void
type A3 = Awaited<string> // string
type A4 = Awaited<Promise<number>> // number
type A5 = Awaited<Promise<Promise<number>>> // it doesn't seem to be right
```

We see that the type of `A5` is `Promise<number>`, but isn't the purpose of our function to obtain the return type of
`promise`? Why did I receive another `promise` type here. The clever friend has already thought of throwing this return
result into the `Awaited` type function and asking it to return the final type. It's a good idea!

```ts
type A5 = Awaited<Promise<Promise<Promise<number>>>>
type A5 = Awaited<Promise<Promise<Promise<Promise<number>>>>>
type A5 = Awaited<Promise<Promise<Promise<Promise<Promise<number>>>>>>
// ....
```

It's time for some friends to step forward, recursion, recursion, use recursion! That's right, our approach is to
recursively use `Awaited` until the final result is returned when the returned type is still a 'promise' type.

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
  ) => any ? Awaited<V> : never:
  T
```

verify

```ts
type A5 = Awaited<Promise<Promise<Promise<number>>>> // number
type A6 = Awaited<Promise<Promise<Promise<Promise<number>>>>> // number
type A7 = Awaited<Promise<Promise<Promise<Promise<Promise<number>>>>>> // number
```

There's no problem, we've solved the problem.

### 📝 Template Literals

The last little trick, let's talk about something simple. Everyone has heard of template strings in JS, and yes, it's 
still the same template string here.

```ts
type A = `hello ${string}`
const A1: A = 'hello world'
const A2: A = 'hello 666'

type B = `hello ${number}`
const B1: B = 'hello world'
const B2: B = 'hello 666'
```

Of course, we allow template strings to be used with `inner`:

```ts
type Include<
  Str extends string
> = Str extends `${infer _R1}${string}` ? _R1 : never

type A = Include<'Hello world'> // 'H'
type B = Include<'TypeScript'> // 'T'
```

We can use template strings and `inner` to perform a series of operations such as replacing, cutting, and combining 
strings or arrays.

## Summary

Through these techniques, we hope to help everyone learn TypeScript better. Of course, the best learning method is to actually use it.

As for whether it is necessary to learn in 'TypeScript', I think it is:

*   `Work`: In the workplace, technology selection is usually determined by team leaders or architects. Therefore, we may 
be required to use 'TypeScript'. Mastering TypeScript can help us better adapt to project requirements and improve work 
efficiency.
*   `Future`: For many developers, they hope to continuously improve themselves and enter large technology companies or 
hold higher-level positions. In these companies and positions` TypeScript is one of the essential skills. Learning 
TypeScript can open more doors for your career.
*   `Open source`: If you want to participate in open source projects or lead the development of a project, using 
TypeScript can significantly improve the quality and security of your code. This not only helps with long-term
maintenance of the project, but also attracts more developers and users to join.

Of course, if the above three points are not directly related to you, then learning TypeScript can help you gain a deeper
understanding of the concepts and best practices of object-oriented programming, and can also be a capital for you to 
boast about after dinner.

**End of article link**

*   [Part of the code in the article is based on Ruan Yifeng's TypeScript tutorial](https://wangdoc.com/typescript)
*   [If you want to have a deeper understanding of TS, you can learn the TypeScript Advanced Reference
Guide](https://juejin.cn/book/7086408430491172901?enter_from=search_result\&utm_source=search)
*   [If you want to practice more TypeScript style gymnastics, of course, I recommend Antfu the Great's 
typeChallenges](https://github.com/type-challenges/type-challenges)
