---
title: "Unit Test: fix Invalid or unexpected token on jest image PNG"
date: 2022-12-16 14:00:00 -0500
categories: [Unit Test, Jest]
tags: [React Native, Bugfix, Unit Test, Jest]
---

<style type='text/css'>
blockquote pre {
  overflow: auto !important;
  overflow-wrap: anywhere !important;
  white-space: pre-wrap;
}
</style>

`.png` 혹은 다른 이미지 파일들이 `.js` file에 binary code로 변환되는 과정에서 생기는 오류로 보이며 `Mock`을 통해 이 문제를 해결 할 수 있다.

## Error Message

> <pre>
> <span style="color:red;">FAIL</span> src/__tests__/App-test.js
> ● Test suite failed to run
> 
> /Users/username/Documents/workspace/MyApp/node_modules/@react-navigation/elements/lib/commonjs/assets/back-icon.png:1
> ({"Object.&lt;anonymous&gt;":function(module,exports,require,__dirname,__filename,global,jest){�PNG
> 
> SyntaxError: Invalid or unexpected token
>     at Runtime.createScriptFromCode (node_modules/jest-runtime/build/index.js:1350:14)
>     at Object.&lt;anonymous&gt; (node_modules/@react-navigation/elements/src/index.tsx:20:3)
> </pre>

## Solution

1. Mock filepath와 `ImageMock.js` file을 만들어 준다. `src/__mocks__/ImageMock.js`

2. `ImageMock.js` 안에 `export default '';` 를 정의한다.

3. `jest.config.js` 안에 `moduleNameMapper` 를 설정한다.

   ```js
   module.exports = {
     moduleNameMapper: {
       "\\.(png|jpg|ico|jpeg|gif|eot|otf|webp|svg|ttf|woff|woff2|mp4|webm|wav|mp3|m4a|aac|oga)$":
         "<rootDir>/src/__mocks__/ImageMock.js",
     },
   };
   ```
   {: file='jest.config.js'}

## Reference

- [React Navigation - Testing with Jest](https://reactnavigation.org/docs/testing)
- [jest: includes png images](https://stackoverflow.com/a/71618485)
- [@react-navigation/stack/lib/commonjs/views/assets/back-icon.png](https://github.com/react-navigation/react-navigation/issues/8669)
- [jest: jest.useFakeTimers()](https://stackoverflow.com/a/57684999)
