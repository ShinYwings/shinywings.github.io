---
title: Introduction of PostgreSQL on terminal
date: 2021-04-08 12:01:00 -0500
categories: [Database, PostgreSQL]
tags: [Linux, WSL2, MacOS, PostgreSQL]
---

Project 준비를 하면서 이전에 WSL2에서 사용했던 PostgresQL 명령어들을 정리해 보았다. WSL2 와 PostgresQL의 설치 방법은 포함하지 않는다. psql 내 `\` 로 시작하는 명령어를 제외한 나머지 명령어는 명령어 끝에 `;` 을 꼭 붙여주어야 한다.

## STEP1: Installation

Install/Update Homebrew

```
$ brew update
$ brew doctor
$ brew install postgresql
```

## STEP2: Start PostgreSQL

Run on `Mac`

```
$ brew services start postgresql
```

<img src="/assets/img/postgres_bootstrap_failed.png" alt="Bootstrap failed: 5: Input/output error" width="100%">

```
$ brew services restart postgresql
```

Run on `WSL2`

```
$ sudo service postgresql start
```

## STEP3: Stop PostgreSQL

To stop using database services, run the code below on the terminal

```
$ brew services stop postgresql
```

## References

- [PostgreSQL by jonganebski](https://github.com/jonganebski/jonganebski.github.io_old/blob/main/src/posts/blog/2021-02-18.md)
- [PostgreSQL 8.3 PSQL Cheat Sheet](https://www.postgresonline.com/special_feature.php?sf_name=postgresql83_psql_cheatsheet)
- [Getting started with PostgreSQL on Mac via Homebrew](https://medium.com/@viviennediegoencarnacion/getting-started-with-postgresql-on-mac-e6a5f48ee399)
- [How to completely uninstall and reinstall Homebrew Postgres](https://blog.testdouble.com/posts/2021-01-28-how-to-completely-uninstall-homebrew-postgres/)
