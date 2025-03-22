---
title: "ES6, CommonJS: import vs require 차이"
date: 2021-08-23 12:00:00 -0500
categories: [JavaScript, Node.js]
tags: [JavaScript, Node.js]
---

React, Front-end projects를 진행하면서 매번 사용하였던 `import` keyword가 NodeJS를 이용한 Server 구축을 하면서 `require`라는 keyword로 대체되면서 이에따른 기본적인 궁금증을 풀기위해 기록한다.

## CommonJS: require

NodeJS에서의 `require`는 built-in function으로 현재 작업하는 스크립트 밖의 external module을 사용할수 있도록 포함시키는 일을 한다. `require()`는 NodeJS의 Core module 뿐만 아니라 Community-base & local module 또한 사용가능하며 module을 동적으로 (Dynamic) load 할수 있다는 강점이 있다.

- Syntax

```js
const module = require("./module.js");

// 전체 module
module.exports = module;
```

## ES6 (ES2015): import

ES6에서의 `import`의 경우, ES module로 구분되는데, 이는 허가된 URL-style relative path 혹은 package name 을 사용 할 수 있으며, `.json`과 같은 file type은 사용 할 수 없다. 따라서 사용하려는 module은 모두 static 해야 하며 프로그램이 실행되고 나서 알수 있는 Dynamic module은 load 할 수 없다.

- Syntax

```js
// 모든 함수
import * from 'module';
// 특정 함수
import { functionA, functionB } from 'module';

export default module;
export { property, functionA };
```

## References:

- [JavaScript require vs import](https://flexiple.com/javascript-require-vs-import/)
