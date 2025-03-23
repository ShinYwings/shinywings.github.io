---
title: "Tools: using Jupyter Notebook in VSCode"
date: 2023-01-11 12:00:00 -0500
categories: [Tools, Editor]
tags: [Python, Tools]
---

Jupyter Notebook은 셀 (cell) 단위로 live code, 방정식, 문서등을 작성할 수 있는 편집기이며 웹 베이스의 어플리캐이션으로 `Python`, `Julia`, `R`, `Ruby`, `Scala` 등 40여개의 프로그래밍 언어를 별도의 설치 없이 사용 할수 있다.

Jupyter Notebook의 장점으로 코드를 각 셀마다 실행시켜 점진적으로 코드가 추가되는 tutorial 혹은 function의 implementation을 눈으로 쉽게 확인 할 수 있다.

## Installation

1. VSCode를 설치한다.

2. [Anaconda](https://docs.anaconda.com/anaconda/install/index.html)/[Miniconda](https://docs.conda.io/en/latest/miniconda.html)를 설치한다.

3. Extension `.ipynb`의 파일을 만들면 VSCode에서 오른쪽 하단에 필요한 Plugins를 설치하라고 알려준다.

   만약 알림이 안뜬다면 [Jupyter Extension](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter)과 [Python Extension](https://marketplace.visualstudio.com/items?itemName=ms-python.python)을 설치한다.

## Shortcuts

| 단축키        | 기능                        |
| ------------- | --------------------------- |
| Shift + Enter | 셀 코드 실행                |
| A             | 상위 셀 추가                |
| B             | 하위 셀 추가                |
| D, D          | 셀 삭제                     |
| Y             | 셀 코드로 전환              |
| M             | 셀 마크다운 다큐먼트로 전환 |

## Code Example

> ### For Loops
>
> ```py
> from requests import get
>
> websites = (
>     "google.com",
>     "airbnb.com",
>     "https://twitter.com",
>     "facebook.com",
>     "https://tiktok.com"
> )
>
> for website in websites:
>     if website.startswith("https://"):
>         print(website)
>     else:
>         print("need to fix")
> ```
>
> > need to fix<br/>
> > need to fix<br/>
> > https://twitter.com<br/>
> > need to fix<br/>
> > https://tiktok.com
>
> ### URL Formatting
>
> ```py
> for website in websites:
>     if not website.startswith("https://"):
>         website = f"https://{website}"
>     print(website)
> ```
>
> > https://google.com<br/>
> > https://airbnb.com<br/>
> > https://twitter.com<br/>
> > https://facebook.com<br/>
> > https://tiktok.com

## Reference

- [Visual Studio, Marketplace - Jupyter](https://marketplace.visualstudio.com/items?itemName=ms-toolsai.jupyter)
- [Python Package Index: requests](https://pypi.org/project/requests/)
