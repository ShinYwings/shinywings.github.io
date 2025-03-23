---
title: Jekyll Facebook Comments Plugin
date: 2021-04-08 12:00:00 -0500
categories: [Jekyll, Minimal Mistakes]
tags: [Jekyll, Minimal Mistakes]
---

Jekyll blog 에 Facebook Comments Plugin 으로 Comments 기능을 활성화 한 방법을 서술한다.

## Generate code

Facebook Comments 기능을 사용하려면 Facebook 에서 제공하는 Comments Plugin 을 사용해야 한다. [Facebook for Developers - Comments Plugin](https://developers.facebook.com/docs/plugins/comments/#) 으로 들어가 `URL`, `Width`, `Number of Posts` 를 넣고 **Get Code** 로 필요한 코드를 받을 수 있다.

<img src="/assets/img/code-generator.jpg" alt="Code Generator" width="100%">

## Add Comments script

Open `_layouts/default.html` and copy and paste **Step 1** code right after the opening body tag.

## Add Comments layout

Open `_layouts/single.html` and copy and paste **Step 2** code right after the closed footer tag.

## Test Comments

위의 Steps 를 잘 따라왔다면 Facebook 에서 제공하는 Comments Plugin 이 제대로 작동하는 것을 확인 할 수 있다. Comments 기능을 넣기위해 찾아보았던 글들 중, `_config.tml` 에서 `comments: true` 로 세팅해야 한다거나, `_includes/comments-providers/facebook.html` 을 지워야 한다는 글들을 확인했었는데 Facebook Comments Plugin 을 사용하는데 앞서 말한 부분을 제외하더라도 Comments 기능이 잘 작동하는 것을 확인했다.

## Reference

- [Roy's Blog - How to Enable Facebook Comments Plugin in Jekyll Blog Posts](https://shantoroy.com/jekyll/facebook-comment-plugin-jekyll-minimal-mistakes-blog-posts/)
