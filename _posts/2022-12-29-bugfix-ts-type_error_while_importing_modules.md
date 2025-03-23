---
title: "TypeScript: fix Type error while importing open source modules"
date: 2022-12-29 03:00:00 -0500
categories: [JavaScript, TypeScript]
tags: [TypeScript, Bugfix]
---

<style type='text/css'>
blockquote pre {
  overflow: auto !important;
  overflow-wrap: anywhere !important;
  white-space: pre-wrap;
}
[class*="keyword"] {
  color: #14A7CD;
}
[class*="classname"] {
  color: #E5E50E;
}
[class*="err_b"] {
  color: #F14B4C;
  font-weight: 600;
}
[class*="err_r"] {
  color: #DA6A6A;
}
[class*="ERROR"] {
  color: #000000;
  font-weight: 600;
  background-color: #A3231F;
}
[class*="FAIL"] {
  color: #F0F0F0;
  font-weight: 600;
  background-color: #CD3131;
}
</style>

TypeScript에서 흔히 발견되는 간단한 Type 오류 메세지에 대하여 알아보고 오류를 잡는 방법까지 알아보려고 한다.

## Error Message

> <pre>
> import crypto
> 'crypto' is declared but its value is never read.ts(6133)
> Module '"crypto"' has no default export.ts(1192)
> </pre>

## Solution

두가지의 방법으로 Type 오류를 바로잡을 수 있는데 하나는 `*`을 사용하는 방법, 다른 하나는 `tsconfig.json`의 `compileOptions`에서 `esModuleInterop`를 설정해 주는 방법이다.

- Import `*(All)`

  ```ts
  import * as crypto from "crypto";
  ```
  {: file='src/index.ts'}

OR

- Set `esModuleInterop` to true on `tsconfig.json`

  ```json
  {
    "include": ["src"],
    "compilerOptions": {
      "outDir": "build",
      "target": "ES5",
      "lib": ["ES6", "DOM"],
      "strict": true,
      "esModuleInterop": true
    }
  }
  ```
  {: file='tsconfig.json'}

  `ts-node` package를 사용한다면 아래와 같은 오류가 발생하지 않는다. 만약 아래와 같은 오류가 발생한다면 `@types/node` 안에 정의되있는 `crypto.d.ts` 파일을 받아 오류를 없앨수 있다.

  > <pre>
  > 'crypto' is declared but its value is never read. ts(6133)
  > Cannot find module 'crypto' or its corresponding type declarations. ts(2307)
  > </pre>

  `DefinitelyTyped`: 여러 개발자들이 참여한 오픈소스로 npm에 존재하는 수많은 오픈소스 프로젝트를 분석하고 `d.ts`파일을 정의해 TypeScript가 이해할수 있도록 정의해놓은 package이다.

  ```sh
  $ npm i -D @types/node
  ```

## Reference

- [GitHub: DefinitelyTyped](https://github.com/DefinitelyTyped/DefinitelyTyped)
