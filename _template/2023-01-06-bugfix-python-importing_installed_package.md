---
title: "Python: fix importing installed package which could not be resolved"
date: 2023-01-06 12:00:00 -0500
categories: [Programming Language, Python]
tags: [Bugfix, Python]
---

<style type='text/css'>
blockquote pre {
  overflow: auto !important;
  overflow-wrap: anywhere !important;
  white-space: pre-wrap;
}
[class*="kw"] {
  color: #14A7CD;
}
[class*="cn"] {
  color: #E5E50E;
}
[class*="err_b"] {
  color: #F14B4C;
  font-weight: 600;
}
[class*="err_r"] {
  color: #DA6A6A;
}
[class*="fail"] {
  color: #F0F0F0;
  font-weight: 600;
  background-color: #CD3131;
}

</style>

Python에서 package를 설치하고도 import 구문에 error message가 나타나며 `main.py`파일을 실행시켰을 시 `ModuleNotFoundError: No module named 'requests'` 오류에 대한 기록이다.

## Error Message

```py
from requests import get
```
{: file='main.py'}

ERROR message on importing `requests` package on VS Code

> <pre>
> (module) requests
> Import "requests" could not be resolved from source Pylance (reportMissingModuleSource)
> </pre>

ERROR message after run the `main.py`

> <pre>
> Traceback (most recent call last):
>   File "/Users/username/Documents/workspace/python/myproject/main.py", line 1, in &lt;module&gt;
>     from requests import get
> ModuleNotFoundError: No module named 'requests'
> </pre>

## Solution

1. VSCode에서 사용하는 `Python interpreter`와 사용자가 사용하는 `pip`이 일치하는지 확인해야 한다.

   맨위에 `from requests import get`에서 request에 오류가 있다고 보여주는 이유는 현재 VS Code가 default로 사용하는 interpreter에서 `requests` module을 불러올수 없기 때문에 보여주는 오류이다. 이 오류는 `Python interpreter`가 사용하려는 `module`을 찾지 못하는 경로의 문제, 혹은 `version`의 차이로 사용하지 못하는 문제 중 하나로 보인다.

   VSCode 오른쪽 하단 `Python` 옆, `3.11.0 64-bit`과 같이 interpreter version이 보이는데 이는 현재 VSCode에서 사용중인 interpreter version을 나타내고 version을 클릭하면 interpreter의 version을 변경 할 수 있다. 현재 사용중인 version은 현재 사용자가 `pip`을 사용하여 설치한 `module`을 돌릴수 있는 interpreter version과 다름으로 오류가 발생하는 것이다.

2. `Module`을 돌릴수 있는 Python의 version을 확인하려면 밑의 방법으로 현재 Python의 version을 확인할 수 있는것으로 보인다.

   ```py
   from platform import python_version

   print("Current Python Version-", python_version())
   ```
   {: file='main.py'}

   Output of `main.py`:

   > <pre>
   > Current Python Version- 3.9.1
   > </pre>

   현재 Python Version `3.9.1`을 확인하고 interpreter의 version을 `3.9.1`로 변경한 결과 오류메세지가 없어짐과 동시에 코드가 문제없이 돌아가는 것을 확인 하였다.

3. 만약 interpreter의 버젼을 선택했음에도 불구하고 오류메세지가 나타난다면 밑의 방법으로 module을 설치해보길 바란다.

   ```bs
   $ sudo python3 -m pip install <module_name>
   ```

   `python -m` 옵션은 script의 path 대신 module의 이름으로 script를 실행하도록 한다.

## Reference

- [ModuleNotFoundError: No module named 'requests' even though I have the module installed](https://stackoverflow.com/a/74157016)
- [ImportError: No Module Named bs4 (BeautifulSoup)](https://stackoverflow.com/a/56524169)
- [I don't understand what "python3 -m" does](https://www.reddit.com/r/learnpython/comments/8nn4za/comment/dzx0byl/?utm_source=share&utm_medium=web2x&context=3)
