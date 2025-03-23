---
title: Install Homebrew on WSL2
date: 2021-05-27 12:00:00 -0500
categories: [Setup]
tags: [WSL2, Homebrew]
---

## Install HomeBrew on WSL2

1. Open Ubuntu WSL App
2. Run Update command
   ```
   $ sudo apt update
   ```
3. Install tool require to setup Brew
   ```
   $ sudo apt-get install build-essential curl file git
   ```
4. Command to Install HomeBrew on WSL Windows 10
   ```
   $ /bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install.sh)"
   ```
5. Add HomeBrew to your system PATH
   ```
   $ test -d ~/.linuxbrew && eval $(~/.linuxbrew/bin/brew shellenv)
   $ test -d /home/linuxbrew/.linuxbrew && eval $(/home/linuxbrew/.linuxbrew/bin/brew shellenv)
   $ test -r ~/.bash_profile && echo "eval \$($(brew --prefix)/bin/brew shellenv)" >>~/.bash_profile
   $ echo "eval \$($(brew --prefix)/bin/brew shellenv)" >>~/.profile
   ```
6. Use Brew on WSL
   ```
   $ brew -v
   $ brew update
   ```

## References:

- [Using Brew on Windows 10 with Windows Subsystem for Linux (WSL)](https://medium.com/@edwardbaeg9/using-homebrew-on-windows-10-with-windows-subsystem-for-linux-wsl-c7f1792f88b3)
- [How to install Brew on WSL- Windows subsystem for Linux](https://www.how2shout.com/linux/install-brew-on-wsl-windows-subsystem-for-linux/)
