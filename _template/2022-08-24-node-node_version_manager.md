---
title: "Node.js: Switch Node Version with Node Version Manager"
date: 2022-08-24 12:00:00 -0500
categories: [JavaScript, Node.js]
tags: [Node.js, NVM, Homebrew]
---

오늘은 새로운 Frontend build tool, Vite를 사용한 프로젝트를 진행하려는데 NodeJS version이 낮아 지원하지 않아 NodeJS version을 바꾸는 법에 대해 알아보도록 하자.

## Prerequisites

`Homebrew` must be installed on your system.

## Install Node Version Manager

```bash
$ brew update
$ brew install nvm
```

## After Download NVM

zsh may not recognize nvm command and return an error message like: `zsh: command not found: nvm`. To fix this issue, open `~/.zshrc` and add the NVM_DIR to the file.

```
export NVM_DIR=~/.nvm
[ -s "$NVM_DIR/nvm.sh" ] && . "$NVM_DIR/nvm.sh"
```

You may restart the terminal or VSCode to run `nvm` command

## Install and Switch NodeJS with NVM

To see available versions:

```bash
$ nvm ls-remote
```

With Node Version Manager, different versions of node can be installed:

```bash
$ nvm install node  ## Installing latest version of NodeJS
$ nvm install 14    ## Installing NodeJS 14.x version
```

After the install, you can check what versions are installed:

```bash
$ nvm ls
```

To switch the Node version, use:

```bash
# targeted node version will be used after the installation
$ nvm install <node_version>
# use `use` command
$ nvm use <node_version>
```

Then you can verify your Node version with:

```bash
# with nvm command
$ nvm current
# with node version check
$ node --version
```

## References:

- [How To Install NVM on macOS with Homebrew](https://tecadmin.net/install-nvm-macos-with-homebrew/)
- [How to run "nvm" in "oh my zsh"?](https://stackoverflow.com/questions/47009776/how-to-run-nvm-in-oh-my-zsh)
