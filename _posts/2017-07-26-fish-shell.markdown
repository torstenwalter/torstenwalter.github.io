---
layout: post
title:  "How to install and configure fish shell on OSX"
date:   2017-07-26 12:00:00 +0200
categories: osx shell fish
excerpt: Explains how to install fishshell on OSX and configure a powerline style prompt.
author: Torsten Walter
---

[fish](https://fishshell.com/) is a very nice shell with autocompletion out of the box. I find it extremely useful.
This explains how you can install it on OS X using homebrew and make it even better by using a popwerline style prompt.

## Install fish shell
Let's install it using brew
```
brew install fish
```

Add `/usr/local/bin/fish` to `/etc/shells` if it is not alredy there:
```
cat /etc/shells|grep /usr/local/bin/fish ||sudo sh -c 'echo "/usr/local/bin/fish" >> /etc/shells'
```

make fish your default shell
```
chsh -s /usr/local/bin/fish
```

## Make the terminal even nicer by customizing the prompt

fish shell is already awesome, but we can make it even better by using a powerline style prompt which displays git branches,
the exit status of the last command, the current time  and much more.

To install it we are going to use Oh My Fish:

> Oh My Fish provides core infrastructure to allow you to install packages which extend or modify the look of your shell. It's fast, extensible and easy to use.

Install [oh-my-fish](https://github.com/oh-my-fish/oh-my-fish)
```
curl -L https://get.oh-my.fish | fish
```

Now we can use oh my fish to install bobthefish, which is a Powerline-style, Git aware fish theme.
```
omf install bobthefish
```

Let's make it even more awesome by installing nerd-fonts
```
brew tap caskroom/fonts
brew cask install font-hack-nerd-font
```

After that you should change the font of your terminal to `Knack Nerd Font` and enable them in bobthefish
```
set -g theme_nerd_fonts yes
```
