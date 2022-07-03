---
title: "Build your gui use tauri"
author: "rogers"
date: 2022-07-03T10:56:57+08:00
slug: "build-your-gui-use-tauri"
summary: "Build your first cross platform gui application use tauri"
tags: ["gui", "rust", "tauri"]
---

## source

- [tauri](https://tauri.app/)
- Tauri 1.0 has launched! [Read the blog post](https://tauri.app/blog/tauri_1_0)
- [rustup](https://rustup.rs/)

## Install

I use kali as my main development desktop, see the full install doc [here](https://tauri.app/v1/guides/getting-started/prerequisites)

```bash
sudo apt update
sudo apt install -y libwebkit2gtk-4.0-dev \
    build-essential \
    curl \
    wget \
    libssl-dev \
    libgtk-3-dev \
    libayatana-appindicator3-dev \
    librsvg2-dev

#install rust use rustup
curl --proto '=https' --tlsv1.2 -sSf https://sh.rustup.rs | sh
#install tauri use cargo
cargo install tauri-cli --version "^1.0.0"
#manage node version use nvm
curl -o- https://raw.githubusercontent.com/nvm-sh/nvm/v0.39.1/install.sh | bash
#restart you terminal session or source you terminal rc file
#install latest lts version node
nvm install --lts
```

Download node from [node official website](https://nodejs.org/en/)

node script

```json
{
  // This content is just a sample
  "scripts": {
    "tauri": "tauri"
  }
}
```

```bash
# here i use [ant design pro](https://pro.ant.design/zh-CN/docs/getting-started)
npm i @ant-design/pro-cli -g
pro create tauri-app
cd tauri-app && npm install
#install tauri
npm install --save-dev @tauri-apps/cli
#edit your package.json put node script in
npm run tauri init
#edit src-tauri/tauri.conf.json
#"beforeBuildCommand": "npm run build",
#"beforeDevCommand": "npm run dev",
#"devPath": "http://localhost:8000",
#"distDir": "../dist"
npm run tauri dev
```

```bash
npm run tauri build
#will got a deb bundle and an appimage file under src-tauri/target/release/bundle
```

Happy dev your cross-platform GUI use javascript
