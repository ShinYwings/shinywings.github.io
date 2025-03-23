---
title: "React Native: fix 'react-native-reanimated' error"
date: 2022-12-14 12:00:00 -0500
categories: [Mobile, React Native]
tags: [React Native, Bugfix]
---

<style type='text/css'>
blockquote pre {
  overflow: auto !important;
  overflow-wrap: anywhere !important;
  white-space: pre-wrap;
}
</style>

`@react-navigation/drawer` package의 dependency의 하나인 `react-native-reanimated` 설치과정에서 나타나는 오류로 `React Navigation > Drawer Navigator` Document 외에 별도의 설정이 필요하다.

## React Native Renanimated

> <pre>
> ERROR  Error: Failed to initialize react-native-reanimated library, make sure you followed installation steps here: https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/installation/
> 1) Make sure reanimated's babel plugin is installed in your babel.config.js (you should have 'react-native-reanimated/plugin' listed there - also see the above link for details)
> 2) Make sure you reset build cache after updating the config, run: yarn start --reset-cache, js engine: hermes
>  ERROR  Invariant Violation: Failed to call into JavaScript module method AppRegistry.runApplication(). Module has not been registered as callable. Registered callable JavaScript modules (n = 11): Systrace, JSTimers, HeapCapture, SamplingProfiler, RCTLog, RCTDeviceEventEmitter, RCTNativeAppEventEmitter, GlobalPerformanceLogger, JSDevSupportModule, HMRClient, RCTEventEmitter.
>         A frequent cause of the error is that the application entry file path is incorrect. This can also happen when the JS bundle is corrupt or there is an early initialization error when loading React Native., js engine: hermes
>  ERROR  Invariant Violation: Failed to call into JavaScript module method AppRegistry.runApplication(). Module has not been registered as callable. Registered callable JavaScript modules (n = 11): Systrace, JSTimers, HeapCapture, SamplingProfiler, RCTLog, RCTDeviceEventEmitter, RCTNativeAppEventEmitter, GlobalPerformanceLogger, JSDevSupportModule, HMRClient, RCTEventEmitter.
>         A frequent cause of the error is that the application entry file path is incorrect. This can also happen when the JS bundle is corrupt or there is an early initialization error when loading React Native., js engine: hermes
> </pre>

## Troubleshoot & Problem Solve

1. `react-native-reanimated`를 설치한다.

   ```sh
   yarn add react-native-reanimated
   ```

2. `babel.config.js` 파일 생성 후, `plugins`에 `react-native-reanimated/plugin`를 추가한다.

   ```js
    module.exports = {
      presets: [
        ...
      ],
      plugins: [
        ...
        'react-native-reanimated/plugin',
      ],
    };
   ```
   {: file='babel.config.js'}

   > CAUTION: Reanimated plugin has to be listed last.
   {: .prompt-warning }

## Reference

- [React Navigation > Drawer Navigator](https://reactnavigation.org/docs/drawer-navigator)
- [React Native Reanimated > Fundamentals > Installation](https://docs.swmansion.com/react-native-reanimated/docs/fundamentals/installation/)
