---
title: "React Native: fix Component return number"
date: 2022-12-17 03:00:00 -0500
categories: [Mobile, React Native]
tags: [React Native, Bugfix]
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

React Native Application에서 SVG 이미지 파일을 사용할 때 `react-native-svg` 이외에 `react-native-svg-transformer` 또한 함께 사용하기를 권고한다. 아래 오류는 이전의 PNG 이미지 파일이 binary code로 변환되는 과정에서 발생하는 오류와 비슷한 문제로 보이며 `react-native-svg-transformer`을 설치하여 문제를 해결 할 수 있다.

## Error Message

> <pre>
> <span class=ERROR> ERROR </span> Error: Element type is invalid: expected a string (for built-in components) or a class/function (for composite components) but got: number.
> 
> Check the render method of `LogInScreen`.
> </pre>

## Solution

1. Install `react-native-svg` and `react-native-svg-transformer`

   ```sh
   $ npm i react-native-svg
   $ npm i react-native-svg-transformer
   ```

2. Set project's `metro.config.js` with following config

   ```js
   module.exports = {
     transformer: {
       getTransformOptions: async () => ({
         transform: {
           experimentalImportSupport: false,
           inlineRequires: true,
         },
       }),
     },
   };

   const { getDefaultConfig } = require("metro-config");

   module.exports = (async () => {
     const {
       resolver: { sourceExts, assetExts },
     } = await getDefaultConfig();
     return {
       transformer: {
         babelTransformerPath: require.resolve("react-native-svg-transformer"),
       },
       resolver: {
         assetExts: assetExts.filter((ext) => ext !== "svg"),
         sourceExts: [...sourceExts, "svg"],
       },
     };
   })();
   ```
   {: file='metro.config.js'}

3. Add `.svgrrc`

   ```js
   {
     "replaceAttrValues": {
       "red": "currentColor"
     }
   }
   ```
   {: file='.svgrrc'}

## Reference

- [Stakoverflow: install react-native-svg-transformer](https://stackoverflow.com/a/65949244)
