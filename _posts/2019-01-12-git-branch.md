---
layout: post
use_math: true
title: "[Git] Branch 사용 방법 정리"
date: 2019-01-12 12:26:37
tagline: "Git에서 버전 관리를 위해 사용되는 Branch 사용 방법 정리"
categories:
- Git
tags:
- git
- linux
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2019-01-12-git-branch
---

Git의 기본적인 사용법에 관한 포스팅은 다음의 [링크](https://hcnoh.github.io/2018-08-20-git-basic-usage)를 참조하면 된다. 여기서는 Git의 Branch 기능에 대해서만 정리한다.

이번 포스팅은 다음의 링크를 참고하여 작성하였다.
- [링크1](https://mylko72.gitbooks.io/git/content/branch/checkout.html)

## Git에서 Branch란?
흔히 알다시피 Git에서는 버전 관리를 위한 Branch라는 기능을 제공한다. 일단 Git 저장소를 생성하면 Branch는 디폴트로 `master`로 설정이 되어있는 것을 확인할 수 있을 것이다. 이 `master` Branch는 가장 기본이 되는 최상위 Branch이며 개발자들은 이 Branch로부터 하위 Branch를 따서 작업을 하게 된다. 물론 `master` Branch도 특별한 기능이 있는 것은 아니고 다른 일반적인 Branch와 동일한 한 종류의 Branch이지만 디폴트로 생성되는 Branch의 이름이 `master`일 뿐이다.

그렇다면 Branch가 필요한 이유는 무엇일까? 보통 Git을 통해서 여러 개발자들이 협업을 통해서 프로젝트를 수행한다고 하면 프로젝트 하나는 여러 단위의 하위 모듈들이 존재할 것이고 개발자들은 각각 자신들이 맡은 모듈을 개발하고 최종적으로 합치는 방식으로 수행할 것이다. 이 경우 개발자들은 각각 전체 모듈을 아우르는 소스 코드를 복사하여 각자 개발하고 마지막에 합치는 방식으로 개발을 수행할텐데 이것을 좀 더 간편하게 수행할 수 있도록 도와주는 Git의 기능이 Branch이다. 전체 모듈을 아우르는 `master` Branch로부터 개발자들은 각각 자신들의 모듈에 해당하는 이름의 Branch를 분기해오게 되며 이렇게 Branch를 따면 `master` Branch의 소스 코드들이 복사가 될 것이다. 그리고 개발자들은 각각의 Branch에서 자신들의 모듈을 개발하고 최종적으로 Merge 요청을 내리게 되면 `master` Branch의 관리자가 확인하고 `master` Branch로 Merge를 시켜주는 방식이 된다.

## Git Branch 확인
현재 등록된 Branch를 확인하기 위해서는 다음의 명령을 수행하면 된다. 그러면 현재 Branch 목록을 확인할 수 있다.

```bash
>>> git branch
* master
  devel
```
현재 Branch 목록은 `master` 및 `devel`이 있는 것을 확인할 수 있으며 `*`을 통해서 현재 위치한 Branch의 이름이 `master`라는 것까지 확인할 수 있다. 아래의 명령을 통해서 Branch 목록 뿐 아니라 좀 더 자세한 Branch 정보들을 확인할 수 있다.

```bash
>>> git branch -v
* master  211d918  Add README.md
  devel   110aac9  modified some source codes
```

첫 번째 칼럼의 목록 뿐 아니라 각 Branch의 최신 Commit Message 등을 확인할 수 있다.

## Git Branch 생성
Branch를 생성하는 명령은 기본적으로 `git branch`이다. 만약 `devel` Branch를 `master` Branch로부터 분기해오고 싶다면 아래와 같은 명령을 수행하면 된다.

```bash
>>> git branch devel master
```

물론 분기해오고자 하는 Branch가 따로 없고 단독으로 Branch를 생성하고 싶으면 아래와 같이 그냥 `master`를 비우고 사용하면 된다.

```bash
>>> git branch devel
```

## Branch Checkout
현재 Branch에서 다른 Branch로 이동하는 작업을 Checkout이라고 한다. Checkout은 기본적으로 아래의 명령을 통해 수행된다. 다음은 `devel` Branch로 이동하는 명령이다.

```bash
>>> git checkout devel
```

또는 `checkout`을 `co`로 요약해서 아래처럼 사용할 수 있다.

```bash
>>> git co devel
```

## 원격 저장소로부터 Branch 정보 받아오기
원격 저장소에서 작업을 한 이후에 로컬에서 이것을 받아오기 위해서는 보통 `git pull` 명령을 사용하면 된다. 하지만 원격 저장소에서 새로운 Branch를 생성하였을 경우에는 `git pull` 명령을 통해서도 Branch 정보를 받아올 수 없다. 이 경우에는 `git pull`이 아닌 다음의 명령을 수행한다.

```bash
>>> git remote update
```

또는 원격 저장소의 Branch 정보를 확인만 하고싶을 때도 있다. 원격 저장소의 모든 Branch 목록을 확인하고 싶은 경우에는 아래의 명령을 수행하면 된다.

```bash
>>> git branch -r
```

또는 원격 및 로컬 저장소의 모든 Branch 목록을 확인하고 싶은 경우에는 아래의 명령을 수행한다.

```bash
>>> git branch -a
```

## 원격 저장소의 특정 Branch만 따로 Clone해오기
원격 저장소를 받아오는 경우 `clone`을 통해서 받아오게 되는데 이 경우 보통 `master` Branch에 대해서 전부 받아오게 된다. 이 경우 특정 Branch만 따로 `clone`하고 싶은 경우는 `-b` 옵션을 이용하면 된다. 예를 들면 주소가 https://github.com/hcnoh/multi-speaker-tacotron-tensorflow.git인 원격 저장소의 `devel`이라는 이름의 Branch만 따로 `clone`해오고 싶은 경우는 다음과 같이 사용할 수 있다.

```bash
>>> git clone -b devel https://github.com/hcnoh/multi-speaker-tacotron-tensorflow.git
```

즉, 정리하자면 다음과 같이 사용하면 된다.

```bash
>>> git clone -b [Branch 이름] [저장소 URL]
```

