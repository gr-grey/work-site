---
title: "mac config"
date: 2023-08-10
description: "all things related to mac os: settings, tricks, headaches"
tags: records
---

Mac os related settings, tricks, headaches.

Config

```yaml
model: M2 Max MacBook Pro 2023
system: Ventura 13.5
```

Homebrew
----

```bash
# xcode tools
xcode-select --install
# homebrew from install.sh
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
# following instruction after running installer
(echo; echo 'eval "$(/opt/homebrew/bin/brew shellenv)"') >> /Users/ruiguo/.zprofile
eval "$(/opt/homebrew/bin/brew shellenv)"
```

oh-my-zsh
----

In terminal, set shell to be zsh

```bash
# oh my zsh
sh -c "$(curl -fsSL https://raw.githubusercontent.com/ohmyzsh/ohmyzsh/master/tools/install.sh)"
```

Github ssh
----
```bash
# fill in the email for my github account
ssh-keygen -t ed25519 -C "myemail@server.com"
ssh-keygen -t rsa -b 4096 -C "myemail@server.com"
eval "$(ssh-agent -s)"
```

`vi .ssh/config`,  add the following block:

```
Host github.com
  AddKeysToAgent yes
  UseKeychain yes
  IdentityFile ~/.ssh/id_ed25519
```

```bash
# keychain
ssh-add --apple-use-keychain ~/.ssh/id_ed25519
# copy and paste into github > setting > ssh key > add a new one
pbcopy < ~/.ssh/id_ed25519.pub
# test if authentificated correctly
ssh -T git@github.com
```

Dotfiles v2
----
Configuring dotfiles can be quite a boring task, and somehow I find myself having to doing it at amazing frequency -
I guess I like to wipe my computers clean like wiping a physical desktop.

I started by having repo for all my computers, then quickly get into immense headaches between Mac and Windows system (alas WSL2, you are so adorable but can be real buggy). Then decided maybe it'd be a good idea to branch out for mac and win systems.

I am starting from scratch to log the whole process.
Create a new repo with a blank [template](https://github.com/anishathalye/dotfiles_template).

```bash
# create mac branch and set remote
git checkout -b utsw-mac
git push -u origin utsw-mac # link it to the corresponding remote branch
```

Edit install.conf.yaml

```yaml

- defaults:
    link:
      relink: true
      create: true

- clean: ['~']

- link:
    ~/.zshrc: zshrc
    ~/.vim: vim
    ~/.vimrc: vimrc
    ~/.gitconfig: gitconfig

- create:
    - ~/downloads
    - ~/.vim/undo-history

- shell:
  - [git submodule update --init --recursive, Installing submodules]
```

Obsidian settings
----
Set up my notebook with obsidian and the vault for this blog site.
Refer to [[01-dev-logs/obsidian log|obsidian log]].

Software
----

### Snap: open apps in dock with `cmd + num`

Like you can do in windows, where `win + 1` would open the first software in your menu bar.

[Snap](https://apps.apple.com/us/app/snap/id418073146?mt=12) allows you to do the same thing with cmd + num. 
It used to cost you a bout a dollar, but it's free now 👻.

Source: [stack exchange question](https://apple.stackexchange.com/questions/4390/keyboard-shortcut-for-launching-apps-in-dock).

- Rectangle
- Karabiner for key remap

2023-08-10