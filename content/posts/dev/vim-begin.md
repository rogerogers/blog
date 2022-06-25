---
title: "Vim tourist"
author: "rogers"
date: 2022-06-24T10:56:57+08:00
slug: "vim-tourist"
summary: "coding with vim, vim is so powerful"
tags: ["vim"]
---

## Install

```bash
sudo apt purge vim-tiny vim-common -y && sudo apt install vim
sudo dnf remove vim-tiny vim-common -y && sudo dnf install vim
brew install vim
vimtutor
#basic vim tourist help you quick operator vim
```

config your vim file

```bash
#use rogerogers's vimrc
git clone git@github.com:rogerogers/vimrc.git
```

```bash
#!/usr/bin/env sh
set -ex

git clone --depth=1 https://github.com/amix/vimrc.git ~/.vim_runtime
sh ~/.vim_runtime/install_awesome_vimrc.sh

#aimx's custom config position
ln -s $(pwd)/my_configs.vim $HOME/.vim_runtime/my_configs.vim

#use vim-plug add some other plug
curl -fLo ~/.vim/autoload/plug.vim --create-dirs \
    https://raw.githubusercontent.com/junegunn/vim-plug/master/plug.vim
```

happy vim
