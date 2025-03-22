---
title: "Unit Test: Setup React Native Apps for Testing"
date: 2022-12-16 10:00:00 -0500
categories: [Unit Test, Jest]
tags: [React Native, Unit Test, Jest]
---

[React Native](https://reactnative.dev/) 프로젝트 생성 후 [Jest](https://jestjs.io/)를 이용한 Unit Test Setup에 대하여 기록한다. Facebook에서도 React Native application을 테스트 할때 Jest를 사용하는 만큼 Jest의 역활이 크다고 생각된다.

## Setup

<!-- prettier-ignore-start -->
```json
{
  "scripts": {
    "test": "jest",
    "test:watch": "jest --watchAll"
  },
  "jest": {
    "preset": "react-native"
  }
}
```
{: file='package.json'}
<!-- prettier-ignore-end -->

- `--watchAll` option enables to specify the name or path to a file to focus on a specific set of tests.

혹은

<!-- prettier-ignore-start -->
```js
module.exports = {
  preset: "react-native",
};
```
{: file='jest.config.js'}
<!-- prettier-ignore-end -->

## Snapshot Test

<!-- prettier-ignore-start -->
```js
import React from "react";
import renderer from "react-test-renderer";
import Intro from "../Intro";

test("renders correctly", () => {
  const tree = renderer.create(<Intro />).toJSON();
  expect(tree).toMatchSnapshot();
});
```
{: file='__tests__/Intro.test.js'}
<!-- prettier-ignore-end -->

## Preset configuration

### **transformIgnorePatterns** customization

`jest-react-native`의 preset 초기값은 `react-native`로 세팅되 있으나, npm dependencies로 인한 custom preset 필요시 `transformIgnorePatterns` 옵션으로 커스터마이징 할 수 있다.

<!-- prettier-ignore-start -->
```js
{
  "transformIgnorePatterns": [
    "node_modules/(?!(react-native|my-project|react-native-button)/)"
  ]
}
```
{: file='jest.config.js'}
<!-- prettier-ignore-end -->

### **setupFiles**

매 테스트에 additional configuration 추가를 원할시 `setupFiles`로 setup script를 작성할 수 있다.

### **moduleNameMapper**

사용하는 module의 위치가 다를경우 `moduleNameMapper` option을 통해 지정해 줄 수 있다.

<!-- prettier-ignore-start -->
```js
{
  "moduleNameMapper": {
    "my-module.js": "<rootDir>/path/to/my-module.js"
  }
}
```
{: file='jest.config.js'}
<!-- prettier-ignore-end -->

## Mock native module

만약 테스트 하는 컴포넌트에서 `react-native-video` dependency를 사용한다면 아래와 같은 방법으로 manual mock을 할 수 있다.

```js
jest.mock("react-native-video", () => "Video");
```

## Reference

- [Testing React Native Apps](https://jestjs.io/docs/tutorial-react-native)
- [Jest CLI Options](https://archive.jestjs.io/docs/en/23.x/cli)
- [React - Testing Recipes](https://reactjs.org/docs/testing-recipes.html)
