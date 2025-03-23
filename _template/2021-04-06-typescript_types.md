---
title: TypeScript Types
date: 2021-04-06 12:00:00 -0500
categories: [JavaScript, TypeScript]
tags: [TypeScript]
---

TypeScript 로 React Project 작업중 다수의 type error 들로 인한 혼돈으로 typescript types 을 다시한번 정리한다.

## Basic Types

| Types        | Examples                                                                                                                                                                    | Description                                                                        |
| ------------ | --------------------------------------------------------------------------------------------------------------------------------------------------------------------------- | ---------------------------------------------------------------------------------- |
| number       | let num: number = 5;                                                                                                                                                        | All numbers, no differentiation between integers or floats                         |
| string       | let name: string = "Paolo";                                                                                                                                                 | All text values                                                                    |
| boolean      | let result: boolean = false;                                                                                                                                                | true or false                                                                      |
| object       | <pre><code>const person: {<br/>&nbsp;&nbsp;name: string,<br/>&nbsp;&nbsp;age: number<br/>} = {<br/>&nbsp;&nbsp;name: 'Paolo',<br/>&nbsp;&nbsp;age: '30'<br/>};</code></pre> | Any JavaScript object, more specific types (type of object are possible)           |
| Array        | let activities: string[] = ["Climbing", "Running"]                                                                                                                          | Any JavaScript array, type can be flexible or strict (regarding the element types) |
| Tuple        | let device: [string, number] = ["iPhone", 699.99]                                                                                                                           | Added by TypeScript: Fixed-length array                                            |
| Enum         | enum Role { ADMIN, AUTHOR, CLIENT}                                                                                                                                          | Added by TypeScript: Automatically enumerated global constant identifiers          |
| any          | \*                                                                                                                                                                          | Any kind of value, no specific type assignment                                     |
| Union Type   | let phone: number \| string                                                                                                                                                 | Expecting multiple types                                                           |
| Literal Type | let status: "free" \| "busy"                                                                                                                                                | Type that is defined with text                                                     |

## Interface

```typescript
interface IAdd {
    num1: number;
    num2: number;
}
function add<IAdd> {
    (...)
}
```

## Type Aliases

```typescript
type UnionType = number | string;
type LiteralType = "as-number" | "as-text";
```

## Function Return Types and Void

```typescript
function add(n1: number, n2: number): number {
  return n1 + n2;
}

// wide type
function printResult(num: number): void {
  console.log("Result" + num);
}
```

## Function Types

```typescript
function add(n1: number, n2: number): number {
  return n1 + n2;
}

let combineValues: (a: number, b: number) => number = add;
console.log(conbineValues(9, 9));
```

## Function Types and Callbacks

```typescript
function addAndHandle(n1: number, n2: number, callback: (num: number) => void) {
  const result = n1 + n2;
  callback(result);
}

addAndHandle(10, 20, (result) => {
  console.log(result);
});
```

## Unknown Type

```typescript
let userInput: unknown;
let userNmae: string;
userInput = 5;
if (typeof userInput === "string") {
  userInput = "Max";
}
```

## Never Type

```typescript
// infinite loop also return never
function generateError(message: string, code: number): never {
  throw { message: message, error: code };
}

generateError("An error occurred!", 500);
```
