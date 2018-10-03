---
layout: post
title: "Git 기본 사용법"
date: 2018-08-20 19:7:44
tagline: "Git을 사용하기 위한 기초개념 및 사용법 정리"
categories:
- Git
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-08-20-git-basic-usage
---

저번 Git 포스팅을 통해서 Git을 맨 처음 사용하였다면 이번에는 Git을 사용하기 위하여 필요한 기초개념 및 사용법들을 정리하여 필요할때 찾아볼 수 있도록 포스팅을 계획하였다. 링크: [이전 포스팅 링크](https://hcnoh.github.io/2018-08-18-git-first-time)

이번 포스팅은 다음의 링크들을 참고하여 작성하였다:
- [출처 1](https://rogerdudler.github.io/git-guide/index.ko.html)  
- [출처 2](https://nolboo.kim/blog/2013/10/06/github-for-beginner/)  
- [출처 3](https://git-scm.com/book/ko/v2)  

## Git이란?
어떤 프로젝트를 여러 명의 개발자가 공동으로 작업하는 경우를 생각해보자. 어떤 개발자 A가 특정 소스코드를 작업하고 있을때 다른 개발자 B가 동시에 이 소스코드에서 작업을 수행하기 시작한다. A는 작업을 완료하여 저장하여 서버에 업로드를 하였고 B는 여전히 이전 소스코드에 작업을 하고 있다. B가 작업을 끝내고 업로드를 하는 순간 A가 작업한 내용은 지워지던가 아니면 중복되게 될 것이다. 따라서 이러한 공동 작업 환경에서는 버전 관리가 매우 중요한 이슈가 될 것이다. 이러한 프로젝트의 버전을 관리해주는 소프트웨어가 바로 Git이다.

## Git 작업의 구성 요소
Git 작업의 구성 요소는 크게 로컬 Repository와 온라인 Repository로 나뉘어진다. 온라인 Repository가 origin이 되어서 여러 명의 개발자들이 자신들의 로컬 Repository로 branch를 따와서 작업을 하여 자신만의 버전을 만든다. 작업을 끝낸 후에 프로젝트의 메인 디렉토리인 master branch에 자신들의 branch를 합친다.

## Git 초기 설정
일단 Git이 설치가 되었다면 자신의 로컬 컴퓨터에서 Git의 config를 설정함으로써 초기 설정을 수행할 수 있다. 이 작업을 위해서는 GitHub (현재 우리 연구소에서는 업무용으로 GitLab을 사용한다.)의 계정이 필요하다.  

다음의 명령어를 타이핑해보자.

```bash
>>> git config --global user.email "rhc0624@gmail.com"
>>> git config --global user.name "hcnoh"
```

여기서 이메일과 이름이 GitHub 계정을 생성할 때 사용한 정보들과 일치한다는 점을 기억하자. 이러한 config 설정을 통해 commit하는 과정에서 매번 저런 정보들을 타이핑할 필요가 없을 것이다.

## 온라인 Repository 생성
Git으로 작업을 수행하기 위해서는 origin이 될 온라인 Repository의 생성이 선행되어야 한다. GitHub를 사용한다면 GitHub.com에 접속하여 `new repository page`로 들어가서 새로운 Repository를 생성한다. 일반적으로 Repository을 줄여서 repo라고 부른다.

`Initialize this repository with a README.`는 체크해도 되고 하지 않아도 된다. 큰 상관은 없다. 다만 처음 생성하는 경우에는 연습삼아서 체크해 보는 것이 좋을 것이다. 체크를 하고 repo를 생성하면 repo 내에 `Readme.md`파일이 같이 생성된다.

## 로컬 Repository 생성
이제 로컬에서 작업하기 위한 로컬 repo를 생성해야 한다. 현재 온라인 repo의 프로젝트 이름과 일치하는 디렉토리를 로컬에 만들어 준다.

```bash
>>> mkdir ~/wavenet-tensorflow
```

그 다음 방금 생성된 디렉토리가 로컬 repo라고 설정을 해줘야 한다. 먼저 방금 생성된 폴더로 이동한다.

```bash
>>> cd ~/wavenet-tensorflow
```

그 다음 `git init` 명령을 통해 현재 디렉토리를 로컬 repo로 설정한다.

```bash
>>> git init
Initialized empty Git repository in /home/hcnoh/wavenet-tensorflow/.git/
```

초기화가 제대로 되었음을 확인할 수 있다. 이제 현재 디렉토리인 `~/wavenet-tensorflow`은 wavenet-tensorflow 온라인 repo 작업을 위한 로컬 repo로 설정되었다.

## Repository 받아오기
다른 프로젝트에 참여하거나 Git repo를 복사하고 싶은 경우에는 `git clone`명령을 사용한다. wavenet-tensorflow 온라인 repo를 받아오기 위해서는 다음과 같은 명령을 타이핑하면 된다.

```bash
>>> git clone https://github.com/hcnoh/wavenet-tensorflow
```

이 명령을 통해서 현재 디렉토리에 `wavenet-tensorflow`이라는 디렉토리를 생성하여 그 안에 `.git` 디렉토리를 생성한다. 또한 repo의 데이터를 모두 가져와서 자동으로 가장 최신 버전을 Checkout해 놓는다. 디렉토리의 이름을 `wavenet-tensorflow`이 아닌 다른 이름으로 설정하고 싶은 경우에는 다음의 명령을 타이핑하면 된다.

```bash
>>> git clone https://github.com/hcnoh/wavenet-tensorflow another_name
```

## 로컬 Repository와 온라인 Repository 연결
현재 생성된 로컬 repo와 온라인 repo인 `https://github.com/hcnoh/wavenet-tensorflow`을 연결하기 위해서는 `git remote` 명령어를 사용한다.

다음의 명령을 타이핑한다.

```bash
>>> git remote add origin https://github.com/hcnoh/wavenet-tensorflow.git
```

이 명령은 origin이라는 이름으로 `https://github.com/hcnoh/wavenet-tensorflow`의 repo를 remote repo로 등록하라는 명령이다.

따라서 앞으로는 `https://github.com/hcnoh/wavenet-tensorflow`처럼 긴 URL 대신에 origin으로 짧게 사용할 수 있게 된 것이다.

또한 현재 remote repo의 정보를 확인하기 위해서는 다음의 명령어를 타이핑하면 된다.

```bash
>>> git remote -v
```

## 작업 내용 Push
먼저 로컬 repo는 git이 관리하는 세 그룹으로 나뉘어져 있다는 점을 알고 넘어가자. 첫 번째 그룹은 Working directory은 실제 파일들로 이루어져 있으며, 두 번째 그룹인 Index는 준비 영역(staging area)의 역할을 한다. 마지막인 HEAD는 최종 확정본(commit)을 나타내게 된다.

먼저 변경된 파일을 Index에 추가하는 과정이 필요하다. 다음의 명령어를 통해 추가할 수 있다.

```bash
>>> git add model.py
```

또는 다음의 명령어를 통하여 현재 디렉토리의 모든 파일을 추가할 수 있다.

```bash
>>> git add .
```

현재까지가 바로 Git의 기본 작업 흐름에서 첫 단계에 해당된다. 하지만 실제로 변경 내용을 확정하려면 다음의 명령을 타이핑해야 한다.

```bash
>>> git commit -m "commit message!"
```

이 과정까지 끝내면 작업 내용이 HEAD에 반영이 되는 것이다.

하지만 실제로 remote repo에 반영하기 위해서는 push를 해주어야 한다. 다음의 명령어를 타이핑한다.

```bash
>>> git push origin master
```

이 명령은 origin 서버에 master branch로 push하라는 명령이다. master branch가 아닌 다른 branch로 push하기를 원한다면 master를 원하는 branch 이름으로 바꿔주면 된다.

## Git commit 취소
위에서 commit 명령을 통해서 remote repo에 반영하기 전에 HEAD에 먼저 반영을 한다고 하였다. 하지만 가끔 실수로 commit을 하던가 아니면 commit 단계에서의 어떤 문제로 인하여 push가 안되는 경우가 생길 수 있다. 이 경우에는 commit을 취소하여 문제를 해결하는 방법이 있을 수 있다.

```bash
>>> git push origin master
Username for 'https://github.com': hcnoh
Password for 'https://hcnoh@github.com':
Counting objects: 15, done.
Delta compression using up to 12 threads.
Compressing objects: 100% (14/14), done.
Writing objects: 100% (15/15), 203.79 MiB | 10.41 MiB/s, done.
Total 15 (delta 6), reused 0 (delta 0)
remote: Resolving deltas: 100% (6/6), completed with 3 local objects.
remote: error: GH001: Large files detected. You may want to try Git Large File Storage - https://git-lfs.gitub.com.
remote: error: Trace: 08f3b36675dd671be3f4b76f255740a4
remote: error: File models/model.ckpt.data-00000-of-00001 is 217.98 MB; this exceeds GitHub's file size limit of 100.00 MB
To https:// github.com/hcnoh/wavenet-tensorflow
 ! [remote rejected] master -> master (pre-receive hook declined)
 error: failed to push some refs to 'https://github.com/hcnoh/wavenet-tensorflow'
```

push를 한 경우 다음과 같은 에러가 발생하였다. 어떤 파일의 용량이 219MB의 크기를 가지고있고 이것은 제한 용량을 초과하기 때문에 push가 되지 않는 것이다. 이 경우 저 파일을 지우고 다시 commit을 하더라도 이전에 commit한 것들이 여전히 push에 반영되기 때문에 같은 에러가 발생할 것이다. 이 경우에는 이전에 해놓은 commit들을 삭제해야 한다.

commit을 취소하는 명령들은 다음과 같다.

- 최종 commit을 취소하되 파일은 복구/삭제하지 않는 명령

```bash
>>> git reset HEAD^
```

- 최종 commit을 취소하고 파일도 복구/삭제

```bash
>>> git reset --hard HEAD^
```

- 최종 commit을 n개 취소하되 파일은 복구/삭제하지 않는 명령

```bash
>>> git reset HEAD~n
```

- 최종 commit을 n개 취소하고 파일도 복구/삭제

```bash
>>> git reset --hard HEAD~n
```

## Git origin 변경/추가
위에서 git의 remote repo의 이름을 origin으로 설정하였다. 이 이름을 바꾸고 싶으면 다음과 같은 명령을 수행하면 된다.

```bash
>>> git remote -v
origin https://github.com/hcnoh/wavenet-tensorflow (fetch)
origin https://github.com/hcnoh/wavenet-tensorflow (push)
>>> git remote rename origin github_origin
github_origin https://github.com/hcnoh/wavenet-tensorflow (fetch)
github_origin https://github.com/hcnoh/wavenet-tensorflow (push)
```

`git remote rename origin github_origin`명령을 통해 origin이 github_origin으로 이름이 변경된 것을 확인할 수 있다.

여기서 새로운 remote repo를 추가하고 싶으면 다음의 명령을 수행하면 된다.

```bash
>>> git remote add github_origin_2 https://github.com/hcnoh/wavenet-tensorflow-2.git
```

`git remote -v`를 통해 새로운 remote repo가 추가된 것을 확인할 수 있다.

```bash
>>> git remote -v
github_origin https://github.com/hcnoh/wavenet-tensorflow (fetch)
github_origin https://github.com/hcnoh/wavenet-tensorflow (push)
github_origin_2 https://github.com/hcnoh/wavenet-tensorflow-2 (fetch)
github_origin_2 https://github.com/hcnoh/wavenet-tensorflow-2 (push)
```

만약 github_origin_2가 비어있는 repo인 상태라면 그대로 push를 해주면 기존의 remote repo인 github_origin이 복사되는 것과 같은 효과를 볼 수 있을 것이다.

```bash
>>> git push github_origin_2 master
Username for 'https://github.com/hcnoh/wavenet-tensorflow-2': hcnoh
Password for 'https://github.com/hcnoh/wavenet-tensorflow-2':
Counting objects: 120, done.
Delta compression using up to 12 threads.
Compressing objects: 100% (113/113), done.
Writing objects: 100% (120/120), 118.46 KiB | 0 bytes/s, done.
Total 120 (delta 66), reused 0 (delta 0)
remote: Resolving deltas: 100% (66/66), done.
To https://github.com/hcnoh/wavenet-tensorflow-2.git
 * [new branch]     master -> master
```
