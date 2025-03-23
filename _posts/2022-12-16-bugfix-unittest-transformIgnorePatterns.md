---
title: "Unit Test: Cannot set properties of undefined (settings 'call')"
date: 2022-12-16 16:00:00 -0500
categories: [Unit Test, Jest]
tags: [React Native, Bugfix, Unit Test, Jest]
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
[class*="fail"] {
  color: #F0F0F0;
  font-weight: 600;
  background-color: #CD3131;
}
</style>

[React Navigation, Testing with Jest - Mocking native modules](https://reactnavigation.org/docs/testing/)에서 `react-native-reanimated/mock`를 Mock 할때 나타나는 오류로 Unit Test 시 `node_modules`이 변환되는데 때때로 module 내부의 모든 파일이 변환되지 않아 Jest가 module의 코드를 이해하지 못해 발생하는 오류이다. 문제를 해결하려면 `transformIgnorePatterns`를 사용하여 module을 변환시키지 않고 사용하면 된다.

## Error Message

> <pre>
> <span class=fail> FAIL </span> src/__tests__/App-test.js
>  ● Test suite failed to run
> 
>    TypeError: Cannot set properties of undefined (setting 'call')
> 
>       8 |   // The mock for `call` immediately calls the callback which is incorrect
>       9 |   // So we override it with a no-op
>    <span class=err_b>></span> 10 |   <span class=classname>Reanimated</span>.<span class=keyword>default</span>.call = () => {};
>         |                          <span class=err_b>^</span>
>      11 |
>      12 |   <span class=keyword>return</span> <span class=classname>Reanimated</span>;
>      13 | });
> 
>      at setupTests.js:10:26
>      at Object.&lt;anonymous&gt; (node_modules/@react-navigation/drawer/lib/src/DrawerView.tsx:22:1)
>      at Object.&lt;anonymous&gt; (node_modules/@react-navigation/drawer/lib/src/createDrawerNavigator.tsx:20:1)
> </pre>

## Solution

1. `jest.config.js` 안에 `transformIgnorePatterns` 를 설정한다.

   ```js
   module.exports = {
     transformIgnorePatterns: [
       "node_modules/(?!" +
         [
           "(jest-)?react-native",
           "react-clone-referenced-element",
           "@react-native-community",
           "rollbar-react-native",
           "@fortawesome",
           "@react-native",
           "@react-navigation",
           "react-native-vector-icons",
         ].join("|") +
         ")",
     ],
   };
   ```
   {: file='jest.config.js'}

## Reference

- [React Navigation - Testing with Jest](https://reactnavigation.org/docs/testing)
- [@react-navigation/stack/lib/commonjs/views/assets/back-icon.png](https://github.com/react-navigation/react-navigation/issues/8669)
