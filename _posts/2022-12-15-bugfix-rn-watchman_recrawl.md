---
title: "React Native: fix watchman Recrawled warning"
date: 2022-12-15 12:00:00 -0500
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

`watchman`에서 `filesystem` sync가 끊어진 것에 대한 경고로 오류 메세지의 안내에 따라 `filesystem`을 다시 연결한다

## Error Message

> <pre>
> watchman warning:  Recrawled this watch 1 times, most recently because:
> MustScanSubDirs UserDroppedTo resolve, please review the information on
> https://facebook.github.io/watchman/docs/troubleshooting.html#recrawl
> To clear this warning, run:
> `watchman watch-del '/Users/username/Documents/workspace/MyApp' ; watchman watch-project '/Users/username/Documents/workspace/MyApp'`
> </pre>

## Solution

```sh
$ watchman watch-del '/Users/username/Documents/workspace/MyApp'
$ watchman watch-project '/Users/username/Documents/workspace/MyApp'
```

## Reference

- [Watchman - Recrawl](https://facebook.github.io/watchman/docs/troubleshooting.html#recrawl)
