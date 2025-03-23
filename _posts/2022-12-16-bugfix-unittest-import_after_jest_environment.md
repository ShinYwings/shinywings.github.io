---
title: "Unit Test: fix `import` a file after the Jest environment has been torn down"
date: 2022-12-16 12:00:00 -0500
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
[class*="err_r"] {
  color: #DA6A6A;
}
[class*="fail"] {
  color: #F0F0F0;
  font-weight: 600;
  background-color: #CD3131;
}

</style>

Component 내 사용하는 module의 loading 시간이 초과되었을때 나타나는 오류로 보인다. Component 테스트에 앞서 `async/await` 처리를 해주거나 `timer` configuration setting으로 문제를 해결 할 수 있다.

## Error Message

> <pre>
> ReferenceError: You are trying to `import` a file after the Jest environment has been torn down.
> 
>      at Object.get BackHandler [as BackHandler] (node_modules/react-native/index.js:129:12)
>      at node_modules/@react-navigation/native/lib/commonjs/useBackButton.js:50:37
>      at commitHookEffectListMount (node_modules/react-test-renderer/cjs/react-test-renderer.development.js:12932:26)
>      at commitPassiveMountOnFiber (node_modules/react-test-renderer/cjs/react-test-renderer.development.js:14346:11)
>      at commitPassiveMountEffects_complete (node_modules/react-test-renderer/cjs/react-test-renderer.development.js:14306:9)
> 
> ReferenceError: You are trying to `import` a file after the Jest environment has been torn down.
> 
>      11 | <span class=keyword>const</span> <span class=classname>AppNav</span> = () => {
>      12 |   <span class=keyword>const</span> scheme = useColorScheme();
>    <span class=err_b>></span> 13 |
>         | <span class=err_b>^</span>
>      14 |   <span class=keyword>let</span> user = <span class=keyword>null</span>;
>      15 |
>      16 |   <span class=keyword>return</span> (
> 
>      at Object.get useColorScheme [as useColorScheme] (node_modules/react-native/index.js:219:12)
>      at AppNav (src/navigation/AppNav.js:13:33)
>      at describeNativeComponentFrame (node_modules/react-test-renderer/cjs/react-test-renderer.development.js:2028:7)
>      at describeFunctionComponentFrame (node_modules/react-test-renderer/cjs/react-test-renderer.development.js:2123:12)
>      at describeFiber (node_modules/react-test-renderer/cjs/react-test-renderer.development.js:2686:14)
> 
>  ●  <span class=err_r><strong class=err_b>Cannot log after tests are done.</strong> Did you forget to wait for something async in your test?</span>
>    Attempted to log "The above error occurred in the &lt;ForwardRef(NavigationContainerInner)&gt; component:
> 
>        at NavigationContainerInner (/Users/username/Documents/workspace/MyApp/node_modules/@react-navigation/native/src/NavigationContainer.tsx:107:23)
>        at AppNav (/Users/username/Documents/workspace/MyApp/src/navigation/AppNav.js:13:48)
>        at App
> 
>    Consider adding an error boundary to your tree to customize error handling behavior.
>    Visit https://reactjs.org/link/error-boundaries to learn more about error boundaries.".
> 
> Node.js v18.8.0
> <span class=fail> FAIL </span> src/__tests__/App-test.js
>   ● Test suite failed to run
> 
>     Call retries were exceeded
> 
>       at ChildProcessWorker.initialize (node_modules/jest-worker/build/workers/ChildProcessWorker.js:193:21)
> </pre>

## Solution

- Test a async component

  ```js
  import "react-native";
  import React from "react";
  import App from "../../App";

  // Note: test renderer must be required after react-native.
  import renderer, { act } from "react-test-renderer";

  describe("App", () => {
    it("compares snapshot", async () => {
      let wrapper;
      await act(async () => {
        wrapper = renderer.create(<App />);
      });
      await expect(wrapper.toJSON()).toMatchSnapshot();
    });
  });
  ```
  {: file='src/__tests__/App-test.js'}

OR

- Add `timers: 'fake'` to `jest.config.js`

  ```js
  module.exports = {
    timers: "fake",
  };
  ```
  {: file='jest.config.js'}

  > timers [string]
  > Default: real
  >
  > Setting this value to fake allows the use of fake timers for functions such as setTimeout. Fake timers are useful when a piece of code sets a long timeout that we don't want to wait for in a test.

OR

- Add `jest.useFakeTimers();` to `setupTests.js`

  ```js
  jest.useFakeTimers();
  ```
  {: file='setupTests.js'}

## Reference

- [React Native: test a async component snapshot with Jest/Testing-library?](https://stackoverflow.com/a/71163030)
- [jest: jest.useFakeTimers()](https://stackoverflow.com/a/57684999)
- [Jest: Timer Mocks](https://jestjs.io/docs/timer-mocks)
- [jest.useFakeTimers(fakeTimersConfig?)](https://jestjs.io/docs/jest-object#jestusefaketimersfaketimersconfig)
