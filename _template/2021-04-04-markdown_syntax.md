---
title: Markdown Syntax 및 사용법
date: 2021-04-04 12:00:00 -0500
categories: [Markdown]
tags: [Markdown]
---

GitHub 작업중 README.md 등 Markdown 파일을 작업 할 때 유용한 정보들을 모아보았다.

## Headings

| Markdown               | HTML                     |
| ---------------------- | ------------------------ |
| # Heading level 1      | <h1>Heading level 1</h1> |
| ## Heading level 2     | <h2>Heading level 2</h2> |
| ### Heading level 3    | <h3>Heading level 3</h3> |
| #### Heading level 4   | <h4>Heading level 4</h4> |
| ##### Heading level 5  | <h5>Heading level 5</h5> |
| ###### Heading level 6 | <h6>Heading level 6</h6> |

## Alternate Syntax

| Markdown                                | HTML                     |
| --------------------------------------- | ------------------------ |
| Heading level 1<br/>===========         | <h1>Heading level 1</h1> |
| Heading level 2<br/>------------------- | <h2>Heading level 2</h2> |

## Emphasis

### Bold

| Markdown           | HTML                            |
| ------------------ | ------------------------------- |
| I am **smart**.    | I am <strong>smart</strong>.    |
| I love **coding**. | I love <strong>coding</strong>. |

### Italic

| Markdown         | HTML                    |
| ---------------- | ----------------------- |
| I am _smart_.    | I am <em>smart</em>.    |
| I love _coding_. | I love <em>coding</em>. |

### Strikethrough

| Markdown           | HTML                            |
| ------------------ | ------------------------------- |
| I am ~~smart~~.    | I am <strike>smart</strike>.    |
| I love ~~coding~~. | I love <strike>coding</strike>. |

## Blockquotes

```
> Example of blockquote
```

> Example of blockquote

### Multiline

```
> Example of blockquote
> Bockquote can be multiline
```

> Example of blockquote  
> Bockquote can be multiline

### Nested

```
> Example of
> > nested blockquote
```

> Example of
>
> > nested blockquote

### Markdowns

````
> * bullet point
> ```
> code example
> ```
````

> - bullet point
>
> ```
> code example
> ```

## Lists

### Ordered Lists

| Markdown                                           | HTML                                                                                    |
| -------------------------------------------------- | --------------------------------------------------------------------------------------- |
| 1. First item<br/>2. Second item<br/>3. Third item | <ol><br/><li>First item</li><br/><li>Second item</li><br/><li>Third item</li><br/></ol> |

### Unordered Lists

| Markdown                                          | HTML                                                                                    |
| ------------------------------------------------- | --------------------------------------------------------------------------------------- |
| - First item<br/> + Second item<br/>\* Third item | <ul><br/><li>First item</li><br/><li>Second item</li><br/><li>Third item</li><br/></ul> |

`-`, `+`, `*` 모두 사용 가능하다.

## Horizontal Rules

```
***
---
___
```

## Links

```
[link keyword][id]

[id]: URL "Optional Title here"

// code
Link: [Google][googlelink]

[googlelink]: https://google.com "Go google"
```

Link: [Google][googlelink]

[googlelink]: https://google.com "Go google"

- 외부링크

```
사용문법: [Title](link)
적용예: [Google](https://google.com, "google link")
```

Link: [Google](https://google.com, "google link")

- 자동연결

```
일반적인 URL 혹은 이메일주소인 경우 적절한 형식으로 링크를 형성한다.

* 외부링크: <https://example.com/>
* 이메일링크: <address@example.com>
```

- 외부링크: <https://example.com/>
- 이메일링크: <address@example.com>

## Images

```
![Alt text](/path/to/img.jpg)
![Alt text](/path/to/img.jpg "Optional title")

![석촌호수 러버덕](http://cfile6.uf.tistory.com/image/2426E646543C9B4532C7B0)
![석촌호수 러버덕](http://cfile6.uf.tistory.com/image/2426E646543C9B4532C7B0 "RubberDuck")
```

사이즈 조절 기능은 없기 때문에 `<img width="" height=""></img>`를 이용한다.

예

```
<img src="/path/to/img.jpg" width="450px" height="300px" title="px(픽셀) 크기 설정" alt="RubberDuck"></img><br/>
<img src="/path/to/img.jpg" width="40%" height="30%" title="px(픽셀) 크기 설정" alt="RubberDuck"></img>

<img src="http://cfile6.uf.tistory.com/image/2426E646543C9B4532C7B0" width="450px" height="300px" title="px(픽셀) 크기 설정" alt="RubberDuck"></img><br/>
<img src="http://cfile6.uf.tistory.com/image/2426E646543C9B4532C7B0" width="40%" height="30%" title="%(비율) 크기 설정" alt="RubberDuck"></img>
```

## Escaping Characters

| Character | Name                |
| --------- | ------------------- |
| \         | backslash           |
| `         | backtick            |
| \*        | asterisk            |
| \_        | underscore          |
| { }       | curly braces        |
| [ ]       | brackets            |
| < >       | angle brackets      |
| ( )       | parentheses         |
| #         | pound sign          |
| +         | plus sign           |
| -         | minus sign (hyphen) |
| .         | dot                 |
| !         | exclamation mark    |
| \|        | pipe                |

## Creating tooltips

```html
<a href="#" data-toggle="tooltip" data-original-title="Tooltip data goes here!"
  >Tooltip information</a
>
```

<a href="#" data-toggle="tooltip" data-original-title="Tooltip data goes here!"> Tooltip information</a>

## References

- [Markdown Guide - Basic Syntax](https://www.markdownguide.org/basic-syntax/)
- [78 Tools for writing and previewing Markdown](https://mashable.com/archive/markdown-tools)
- [John gruber 마크다운 번역](https://nolboo.github.io/blog/2013/09/07/john-gruber-markdown/)
- [깃허브 취향의 마크다운 번역](https://nolboo.github.io/blog/2014/03/25/github-flavored-markdown/)
- [허니몬의 마크다운 작성법](https://www.slideshare.net/ihoneymon/ss-40575068)
- [Jekyll: Tooltips](https://idratherbewriting.com/documentation-theme-jekyll/mydoc_adding_tooltips.html#creating-tooltips)
- [Notion.so](https://www.notion.so/product)
- [Atom](https://atom.io/)
- [Visual Studio Code](https://code.visualstudio.com/)
- [Notepad++](https://notepad-plus-plus.org/)
