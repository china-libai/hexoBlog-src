---
title: install homebrew and wine on mac
date: 2017-05-16 14:02:17
tags:
---

## wine安装
```bash
# 安装homebrew
/usr/bin/ruby -e "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/master/install)"

# 安装wine
brew install wine
# 会提示wine: XQuartz is required to install this formula.X11Requirement unsatisfied!
# 按照提示信息到https://xquartz.macosforge.org下载xquartz或者直接运行：
brew cask install xquartz
<!-- more -->
# 安装成功后，再次：
brew install wine

# 检查wine 安装状态
wine --version

# 使用wine打开exe程序
wine winfile.exe
```
brew cask :: [http://caskroom.github.io](http://caskroom.github.io)

XQuartz :: [https://www.xquartz.org](https://www.xquartz.org)