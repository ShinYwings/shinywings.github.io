---
title: "Bootstrap: Responsive Navbar"
date: 2023-01-02 03:00:00 -0500
categories: [StyleSheet, Bootstrap]
tags: [StyleSheet, Bootstrap]
---

<style type='text/css'>
blockquote pre {
  overflow: auto !important;
  overflow-wrap: anywhere !important;
  white-space: pre-wrap;
}
</style>

Project에서 화면비율에 따라 바뀌는 반응형 (Responsive) application을 일반 Grid를 사용하여 디자인하였다가 [Bootstrap](https://getbootstrap.com/)의 [Grid System](https://getbootstrap.com/docs/4.1/layout/grid/)으로 대체하기로 했다. 그러면서 자연스럽게 Bootstrap의 [Navbar](https://getbootstrap.com/docs/4.1/components/navbar/) 또한 적용시키기로 하였다. 이글은 Navbar을 적용시키는 방법에 대해 설명하려고 한다.

## Prerequisites

- 현재 진행하는 Project는 SSR(Server Side Rendering)으로 Node.js, Express.js에 Pug View Engine을 사용하고있다.
- Markdown Code block에서 Jade 및 Pug의 문법을 지원하지 않는 관계로 예제코드는 일반 HTML 문법에 맞춰 작성한다.

## Setup

1. `html`, `head` tag에 Stylesheet `link`와 `script`를 삽입한다.

   ```html
   <head>
     <link
       href="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/css/bootstrap.min.css"
       rel="stylesheet"
       integrity="sha384-GLhlTQ8iRABdZLl6O3oVMWSktQOp6b7In1Zl3/Jr59b6EGGoI1aFkw7cmDA6j6gD"
       crossorigin="anonymous"
     />
     <script
       src="https://cdn.jsdelivr.net/npm/bootstrap@5.3.0-alpha1/dist/js/bootstrap.bundle.min.js"
       integrity="sha384-w76AqPfDkMBDXo30jS1Sgez6pr3x5MlQ1ZAGC+nuZB+EYdgRZgiwxhTBTkF7CXvN"
       crossorigin="anonymous"
     ></script>
   </head>
   ```

2. Navigation Bar을 정의한다.

   ```html
   <nav class="navbar navbar-expand-lg navbar-light bg-light">
     <a class="navbar-brand" href="#">Navbar</a>
     <button
       class="navbar-toggler"
       type="button"
       data-bs-toggle="collapse"
       data-bs-target="#navbarNav"
       aria-controls="navbarNav"
       aria-expanded="false"
       aria-label="Toggle navigation"
     >
       <span class="navbar-toggler-icon"></span>
     </button>
     <div class="collapse navbar-collapse" id="navbarNav">
       <ul class="navbar-nav">
         <li class="nav-item active">
           <a class="nav-link" href="#"
             >Home <span class="sr-only">(current)</span></a
           >
         </li>
         <li class="nav-item">
           <a class="nav-link" href="#">Features</a>
         </li>
         <li class="nav-item">
           <a class="nav-link" href="#">Pricing</a>
         </li>
         <li class="nav-item">
           <a class="nav-link disabled" href="#">Disabled</a>
         </li>
       </ul>
     </div>
   </nav>
   ```

   여기서 주목해야 할 것은 `navbar-toggler` 클래스를 갖은 버튼의 `data-bs-target`의 `id`와 `collapse navbar-collapse`를 갖은 `div` 태그의 `id`가 서로 일치해야 한다는 것이다.

## Troubleshoot

1. `head` 태그에 `link`와 `script`를 모두 넣었는지 확인한다. 간혹 Bootstrap의 css 목적으로 `link`만 삽입하는 경우가 있는데 `script`의 부재시 클릭했을때 버튼이 작동하지 않는다.

2. Bootstrap 공식홈페이지 예제를 보면 `navbar-toggler` 버튼에 `data-toggle`, `data-target` 속성을 사용하는데 이 두 속성은 Bootstrap v4를 기준으로 한 속성이며 v5 기준 `data-bs-toggle`, `data-bs-target` 속성을 사용한다. 기존 두 속성키를 v5에 사용한다면 `navbar-toggler` 버튼이 작동하지 않는다.

## Reference

- [Bootstrap](https://getbootstrap.com/)
