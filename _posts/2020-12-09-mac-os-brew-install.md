---
layout: post
title: macOS에 Homebrew 설치하기
tags:
  - macos
  - brew
---

macOS에 Homebrew 설치하기

macOS용 패키지 관리자 Homebrew.

[Homebrew Homepage](https://brew.sh/index_ko)

Homebrew를 이용하면 Apple(또는 linux 시스템)에서 제공하지 않는 **유용한 패키지 관리자**를 설치합니다.


터미널에 아래의 명령어를 입력합니다. (2020-12-09 현재 공식페이지 제공)
```
/bin/bash -c "$(curl -fsSL https://raw.githubusercontent.com/Homebrew/install/HEAD/install.sh)"
```

"Press RETURN to continue or any other key to abort" 라는 문장이 뜨면, 엔터를 눌러 넘어가면 설치가 완료됩니다.

password에는 현재 mac에 설정하신 비밀번호를 입력하면 됩니다.

```
...
==> Installation successful!
...
```

설치 완료 및 버전 확인
```
$ brew -v
Homebrew 2.6.1
Homebrew/homebrew-core (git revision c7b0b1; last commit 2020-12-09)
```

최신버전 업데이트
```
$ brew update
```
