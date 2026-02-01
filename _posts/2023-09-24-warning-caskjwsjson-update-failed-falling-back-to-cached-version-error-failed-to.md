---
title: "Warning: cask.jws.json: update failed, falling back to cached version. Error: Failed to download https://formulae.brew.sh/api/cask.jws.json!"
date: 2023-09-24
description: "심플하게.. 어느날(?) brew 통해서 특정패키지를 설치하다가 위와 같은 오류 문구를 만났다.https&#x3A;//github.com/Homebrew/brew/issues/14516 구글링 해보니config doctor 조회등 다양한 접근등이 제안되었던 거 같은데 "
tags: ["cask", "homebrew", "macbook"]
categories: ["Tech Note", "업무환경"]
---
![](/assets/img/posts/28333c20-a584-46b7-b686-1f99f3b51161-image.png)
심플하게.. 어느날(?) brew 통해서 특정패키지를 설치하다가 위와 같은 오류 문구를 만났다.

https://github.com/Homebrew/brew/issues/14516 구글링 해보니
config doctor 조회등 다양한 접근등이 제안되었던 거 같은데 해결책은 
```
export HOMEBREW_NO_INSTALL_FROM_API=1 
```
요거였다.
![](/assets/img/posts/8b9ef51c-8cc8-46f2-a828-3693d32886cc-image.png)


![](/assets/img/posts/6bd9d249-28e6-40a2-8d11-b8fe0f0b0f74-image.png)

> This will make Homebrew install formulae and casks from the homebrew/core and homebrew/cask taps using local checkouts of these repositories instead of Homebrew’s API.

대충은 이해하겠는데 영어라서 정확하게 번역하기엔 역량이 떨어진다.