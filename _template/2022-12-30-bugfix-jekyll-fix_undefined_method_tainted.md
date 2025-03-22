---
title: "Chirpy Jekyll Theme: fix undefined method `tainted?`"
date: 2022-12-30 03:00:00 -0500
categories: [Jekyll, Chirpy Jekyll Theme]
tags: [Jekyll, Bugfix]
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

Ruby 3.2 버젼에서 나타는 오류로 보이며 blog 빌드시 3.1 버젼으로 제안하는 것으로 오류를 해결 할 수 있는것으로 보인다.

## Error Message

> <pre>
> Liquid Exception: undefined method `tainted?' for "h_":String in /home/runner/work/username.github.io/username.github.io/vendor/bundle/ruby/3.2.0/gems/jekyll-theme-chirpy-5.3.2/_layouts/categories.html
>                     ------------------------------------------------
>       Jekyll 4.3.1   Please append `--trace` to the `build` command 
>                      for any additional information or backtrace. 
>                     ------------------------------------------------
> </pre>

## Solution

```yml
- name: Setup Ruby
  uses: ruby/setup-ruby@v1
  with:
    ruby-version: 3.1 # <- change ruby-version from 3 to 3.1
    bundler-cache: true
```
{: file='.github/workflows/pages-deploy.yml'}

## Reference

- [Issue: undefined method `tainted?`](https://github.com/cotes2020/jekyll-theme-chirpy/issues/811#issuecomment-1364902702)
