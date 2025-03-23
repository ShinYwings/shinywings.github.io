---
title: "React: Using environment variables"
date: 2021-06-07 12:00:00 -0500
categories: [JavaScript, React]
tags: [React]
---

React Web Client 작업을 하면서 필요했던 `.env` 파일 안의 환경변수를 가져오는 방법에 대하여 서술해 본다. `dotenv` 를 사용하여 환경변수를 가져올 계획이었으나 `dotenv`를 불러오는데 문제가 많았다. 허나, React App 에서는 환경변수를 `REACT_APP_` 으로 시작하여 작성하면 `dotenv`를 사용하지 않더라도 환경변수를 불러올 수 있었다.

## Using environment variables in React

> Note: this feature is available with `react-scripts@0.5.0` and higher.

Add `.env` file in the root of your project.

```
REACT_APP_API_KEY=XXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXXX
```

In the file where you need to call an environment variable from `.env`

```js
const API_KEY = process.env.REACT_APP_API_KEY;
```

## Types of `.env`

> Note: this feature is available with `react-scripts@1.0.0` and higher.

- `.env`:
  default
- `.env.local`:
  **local override**
- `.env.development`, `.env.test`, `.env.production`:
  Environment-specific settings
- `.env.development.local`, `.env.test.local`, `.env.production.local`:
  local overrides of environment-specific settings

## References:

- [Adding Development Environment Variables In `.env`](https://create-react-app.dev/docs/adding-custom-environment-variables/#adding-development-environment-variables-in-env)
