---
layout: post
title: macOS에 coreutils 설치
tags:
  - macos
  - brew
---

The CoreUtils package is also published [as a Brew formula](https://formulae.brew.sh/formula/coreutils). So if you have Brew installed you can also just run:

```
brew install coreutils
```

Then add `PATH="/usr/local/opt/coreutils/libexec/gnubin:$PATH"` to `~/.bashrc`, run `source ~/.bashrc` and you're done.