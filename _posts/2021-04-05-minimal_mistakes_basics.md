---
title: Minimal Mistakes 간단 사용법
date: 2021-04-05 12:00:00 -0500
categories: [Jekyll, Minimal Mistakes]
tags: [Jekyll, Minimal Mistakes]
---

Jekyll theme 중 Minimal Mistakes 을 사용하여 간단하게 GitHub Page 에서 Blog 로 사용 할 수 있는 방법을 정리해 보았다.

## Setup

1. minimal-mistakes 를 자신의 repository 로 가져온다.

   - Git command 로 clone 하여 새로운 repository 올리는 방법

     ```
     $ git clone git@github.com:mmistakes/minimal-mistakes.git
     ```

   - Fork 하는 방법

     Minimal-mistakes repository 에 직접 방문하여 `Fork` 한다

     [minimal-mistakes](https://github.com/mmistakes/minimal-mistakes)

2. Settings 에서 Repository name 을 `username.github.io` 로 변경한다.

## Theme

In `_config.yml`:

```
# Theme Settings
minimal_mistakes_skin: "default" # "default", "air", "aqua", "contrast", "dark", "dirt", "neon", "mint", "plum", "sunrise"
```

## Site Settings

```
# Site Settings
title: "Note by Paolo"
name: "Paolo Chang"
url: "https://paolochang.github.io" # the base hostname & protocol for your site e.g. "https://mmistakes.github.io"

```

## Site Author

```
# Site Author
author:
  name: "Paolo Chang"
  avatar: # path of avatar image, e.g. "/assets/images/bio-photo.jpg"
  bio: "Web & Mobile Developer"
  location: "Toronto. ON"
  email: "chanhwan.chang@gmail.com"
  links:
- label: "LinkedIn"
      icon: "fab fa-fw fa-linkedin"
      url: "https://www.linkedin.com/in/paolochang/"
    - label: "GitHub"
      icon: "fab fa-fw fa-github"
      url: "https://github.com/PaoloChang"
```

## Site Footer

```
# Site Footer
footer:
  links:
    - label: "LinkedIn"
      icon: "fab fa-fw fa-linkedin"
      url: "https://www.linkedin.com/in/paolochang/"
    - label: "GitHub"
      icon: "fab fa-fw fa-github"
      url: "https://github.com/PaoloChang"
```

## Navigation

In `_data/navigation.yml`: 주석처리된 Categories 와 Tags 를 사용한다.

```
# main links
main:
  - title: "Categories"
    url: /categories/
  - title: "Tags"
    url: /tags/
```

Navigation 에서 `Categories` 와 `Tags` 가 나타나지만 화면 이동이 불가하다.

Create `category-archive.md`: Categories 의 글을 볼 수 있도록 도운다.

```
# main links
main:
  - title: "Categories"
    url: /categories/
  - title: "Tags"
    url: /tags/
```

Create `tags-archive.md`: Tags 의 글을 볼 수 있도록 도운다.

```
---
title: Posts by Tag
layout: tags
permalink: /tags/
author_profile: true
---
```

## Create Post

1. `_posts` 폴더를 생성한다
2. `yyyy-mm-dd-post_name.md` 포맷으로 글을 작성한다
3. Front Matter 예시

   ```
   ---
   layout: single
   category:
   - Jekyll
   tag:
   - Jekyll
   - Minimal Mistakes
   toc: true
   toc_sticky: true
   title: "Minimal Mistakes 간단 사용법"
   ---
   ```

   - `toc`: table of contents (목차) 의 준말
   - `toc_sticky`: 스크롤 이후에도 목차가 페이지에 남아있다

## Test on localhost

```
$ sudo gem install jekull bundler
$ bundle install
$ bundle exec jekyll serve
```

## Reference

- https://jekyllrb.com/docs/front-matter/
