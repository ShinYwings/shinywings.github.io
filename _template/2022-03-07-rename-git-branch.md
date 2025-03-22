---
title: "Git: Rename a Local and Remote Git Branch"
date: 2022-03-07 12:00:00 -0500
categories: [Version Control, Git]
tags: [Git]
---

간혹 `Git`을 사용하면서 `branch` 이름을 변경 해야 할 때가 있었다. 회사에서 프로젝트를 진행하며 `branch`의 이름을 바꾸는 경우는 드물었지만 개인 Project나 공부를 할 때, 각각의 `branch`로 Project의 타임라인을 포맷에 맞춰 이름을 바꾼 방법을 공유한다.

<img src="/assets/img/git_banner.png" alt="Git" width="100%">

## Rename Local Git Branch

1. 이름을 바꾸고자 하는 `local branch`로 들어간다.

   ```sh
   $ git checkout <old_branch_name>
   ```

2. `branch`에 새로운 이름을 부여한다.

   ```sh
   $ git branch -m <new_branch_name>
   ```

## Rename Remote Git Branch

작업하는 `branch`가 `remote branch`에 `push`되지 않았다면, 위의 방법으로 `branch`이름을 바꾼뒤 작업을 `push`하였을 때 새로운 이름이 `remote branch`에 제대로 저장된 것을 볼 수 있다. 하지만 이미 `push`한 `branch`라면, 아래의 방법으로 `remote branch`의 이름을 바꿀 수 있다.

1. 새로운 `branch`를 `upstream`을 이용해 `reset`한다

   ```sh
   $ git push origin -u <new_branch_name>
   ```

2. 바뀌기 전 `branch`를 지운다.

   ```sh
   $ git push origin --delete <old_branch_name>
   ```

## References:

- [How To Rename a Local and Remote Git Branch](https://linuxize.com/post/how-to-rename-local-and-remote-git-branch/)
