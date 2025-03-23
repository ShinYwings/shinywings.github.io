---
title: "Python: Let's build web scrapper using Beautiful Soup"
date: 2023-01-08 12:00:00 -0500
categories: [Programming Language, Python]
tags: [Python, Web Scrapping]
---

Web에서 원하는 정보는 찾아오는 것을 `Web scrapper`라고 한다. 오늘은 `Web scrapping`을 할 수 있도록 도와주는 Python library, `Beautiful Soup` module에 대하여 간략하게 기록하려 한다. `Web scrapping`을 할때 주의 해야할 점은 사용자가 스크랩을 하려는 웹사이트 마다 각자 다른 정책를 갖고 있으며 scrape한 데이터를 상업적으로 사용하지 못하도록 해놓은 곳 또한 있다. 따라서 scrape을 할때 사용자의 주의가 필요하다.

## Prerequisites

```bash
$ pip3 install requests
$ pip3 install beautifulsoup4
```

- [requests](https://pypi.org/project/requests/)
- [Beautiful Soup](https://pypi.org/project/beautifulsoup4/)

## Beautiful Soup

- [find_all()](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#find-all)

  Method signature: find_all(name, attrs, recursive, string, limit, \*\*kwargs)

  ```py
  # soup 안에 모든 <title> tag를 가져온다
  soup.find_all("title")
  # [<title>The Dormouse's story</title>]

  # soup 안에 class가 title인 모든 <p> tag를 가져온다
  soup.find_all("p", "title")
  # [<p class="title"><b>The Dormouse's story</b></p>]

  # soup 안에 모든 <a> tag를 가져온다
  soup.find_all("a")
  # [<a class="sister" href="http://example.com/elsie" id="link1">Elsie</a>,
  #  <a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>,
  #  <a class="sister" href="http://example.com/tillie" id="link3">Tillie</a>]

  # soup 안에 id가 link2인 모든 tag를 가져온다
  soup.find_all(id="link2")
  # [<a class="sister" href="http://example.com/lacie" id="link2">Lacie</a>]
  ```

- [find()](https://www.crummy.com/software/BeautifulSoup/bs4/doc/#find)

  Method signature: find(name, attrs, recursive, string, \*\*kwargs)

  ```py
  # find_all은 list를 반환한다
  soup.find_all('title', limit=1)
  # [<title>The Dormouse's story</title>]

  soup.find('title')
  # <title>The Dormouse's story</title>
  ```

## Web Scrapper

1. Scrape 할 대상을 `base_url`에 저장하고 `requests` module을 사용하여 response를 확인한다.

   ```py
   from bs4 import BeautifulSoup
   import requests


   def searchBook(keyword):
       title = keyword.replace(' ', '%20')
       url = f"https://www.chapters.indigo.ca/en-ca/home/search/?keywords={title}"
       request = requests.get(url)
       if request.status_code == 200:
         print("Request 200")
       else:
         print("Request is not working")

   ```
   {: file='main.py'}

2. `request`가 200일 경우 `BeautifulSoup`을 이용하여 원하는 데이터 값을 출력 할 수 있다.

   ```py
   from bs4 import BeautifulSoup
   import requests


   def searchBook(keyword):
       title = keyword.replace(' ', '%20')
       url = f"https://www.chapters.indigo.ca/en-ca/home/search/?keywords={title}"
       request = requests.get(url)
       if request.status_code == 200:
         soup = BeautifulSoup(request.text, "html.parser")
         for book in soup.find_all('div', class_="product-list__product"):
           title = book.find(
               'a', class_="product-list__product-title-link--grid")
           print("title:", title.string)
           author = book.find(
                'a', class_="product-list__author-link product-list__contributor")
           print("author:", author.string)
           print("---------------------------")

       else:
           print("Can't get item.")

    print(searchBook("harry potter"))
   ```
   {: file='main.py'}

   Output of `main.py`

   > <pre>
   > title: Harry Potter and the Philosopher’s Stone – 25th Anniversary Edition
   > author: J.K. Rowling
   > ---------------------------
   > title: Harry Potter Box Set: The Complete Collection (Children’s Paperback)
   > author: J.K. Rowling
   > ---------------------------
   > title: Harry Potter And The Order Of The Phoenix
   > author: J.K. Rowling
   > ---------------------------
   > title: YuMe Harry Potter Magical Capsules - Series 3
   > author: Harry Potter
   > ---------------------------
   > title: Harry Potter: Patronus Mini Projector Set
   > author: Running Press
   > ---------------------------
   > (...)
   > </pre>

## Reference

- [Python Package Index: requests](https://pypi.org/project/requests/)
- [Python Package Index: Beautiful Soup](https://pypi.org/project/beautifulsoup4/)
- [Beautiful Soup Documentation](https://www.crummy.com/software/BeautifulSoup/bs4/doc/)
