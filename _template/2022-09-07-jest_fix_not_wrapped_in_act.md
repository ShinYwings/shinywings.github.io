---
title: "Unit Test: fix '... was not wrapped in act(...)' warning"
date: 2022-09-07 12:00:00 -0500
categories: [Unit Test, Jest]
tags: [React, Jest, Bugfix, Unit Test, React Testing Library]
---

<style type='text/css'>
blockquote pre {
  overflow: auto !important;
  overflow-wrap: anywhere !important;
  white-space: pre-wrap;
}
</style>

I found one of my application failed to build on Netlify after I add new feature. And I found the problem caused by yhe conflicts in the dependencies of package in the `package.json` file.

## Solution on local development

> <pre>
> <span style="color:red; font-weight:bold;">Warning: An update to PostProvider inside a test was not wrapped in act(...).</span>
> 
>     When testing, code that causes React state updates should be wrapped into act(...):
> 
>     act(() => {
>       /* fire events that update state */
>     });
>     /* assert on the output */
> 
>     This ensures that you're testing the behavior the user would see in the browser. Learn more at https://reactjs.org/link/wrap-tests-with-act
> </pre>

## References:

- [Fix the "not wrapped in act(...)" warning](https://kentcdodds.com/blog/fix-the-not-wrapped-in-act-warning)
