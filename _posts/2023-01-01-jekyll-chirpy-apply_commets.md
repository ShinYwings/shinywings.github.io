---
title: "Chirpy Jekyll Theme: Apply Comments"
date: 2023-01-01 03:00:00 -0500
categories: [Jekyll, Chirpy Jekyll Theme]
tags: [Jekyll, Ruby]
---

<style type='text/css'>
blockquote pre {
  overflow: auto !important;
  overflow-wrap: anywhere !important;
  white-space: pre-wrap;
}
</style>

[giscus](https://giscus.app/en)는 GitHub Discussions을 기반으로 댓글 기능을 구현한 오픈소스이다. `giscus`의 소개에서 언급했듯이 [utterances](https://utteranc.es/)에서 많은 영감을 받았는데, 두 application의 차이는 댓글을 작성하는 방식에 있다. 두 application 모두 별도의 `database`를 두고 있지 않는데 `utterances`는 댓글을 Repository `Issues`에 접근하여 댓글을 작성하는 반면, `giscus`는 `Discussions`을 사용한다. 내가 `giscus`를 고른 이유는 Post에 댓글 기능 뿐만 아니라 Emoji를 이용한 reaction을 사용할수 있기 때문이다. 다만 주의할 점으로는 `giscus`는 활발히 개발되고 있는 단계임으로 시간이 지남에따라 일부 기능이 작동되지 않거나 변경될수 있는 부분이 있다고 한다. 또한 [giscus는 한글번역본](https://giscus.app/ko)을 지원하여 더 자세한 부분은 직접 읽어보길 바란다.

## Setup

1. GitHub OAuth를 이용하여 [giscus app](https://github.com/apps/giscus)이 Discussion 글을 개시할 수 있도록 권한을 부여해야한다. 위의 `giscus app` 링크를 열어 Install을 한다.

2. GitHub repositories에 관한 permissions을 물어보는 팝업이 뜬다. 우리의 목적은 Jekyll Blog에서 댓글을 구현하는 것이니 `Only select repositories`를 선택하고 `<username>.github.io` branch를 선택한다.

3. Configuration 세팅에 앞서 `<username>.github.io` repository > `Settings` > `General`, `Features`에 Discussions checkbox를 선택한다.

4. 다음과 같이 Configuration 세팅을 한다.

   - Language: `원하는 언어 선택`
   - Repository: `<username>.github.io`
   - Discussion Category: `General`
   - Theme: `Preferred color scheme`

5. Configuration 세팅을 마치면 미리보기 script가 준비되어 있다.

   ```html
   <script
     src="https://giscus.app/client.js"
     data-repo="<username>/<username>.github.io"
     data-repo-id="YOUR_REPO_ID"
     data-category-id="YOUR_CATEGORY_ID"
     data-mapping="pathname"
     data-strict="0"
     data-reactions-enabled="1"
     data-emit-metadata="0"
     data-input-position="bottom"
     data-theme="preferred_color_scheme"
     data-lang="en"
     crossorigin="anonymous"
     async
   ></script>
   ```

6. 위의 script를 `_layouts/post.html` 맨 끝에 붙여넣는다.

   ```html
     </div>
     <!-- .post-tail-bottom -->
   </div>
   <!-- div.post-tail-wrapper -->

   <!-- Enable giscus -->
   <script
     src="https://giscus.app/client.js"
     data-repo="<username>/<username>.github.io"
     data-repo-id="YOUR_REPO_ID"
     data-category-id="YOUR_CATEGORY_ID"
     data-mapping="pathname"
     data-strict="0"
     data-reactions-enabled="1"
     data-emit-metadata="0"
     data-input-position="bottom"
     data-theme="preferred_color_scheme"
     data-lang="en"
     crossorigin="anonymous"
     async
   ></script>
   ```
   {: file='_layouts/post.html'}

## Reference

- [GitHub: giscus/giscus](https://github.com/giscus/giscus)
- [GitHub: utterance/utterance](https://github.com/utterance/utterances)
