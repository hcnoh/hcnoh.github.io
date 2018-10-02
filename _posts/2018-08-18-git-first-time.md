---
layout: post
title: "Git 처음 사용하기"
date: 2018-08-18 14:13:35
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- Git
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-08-18-git-first-time
---

최근 시작한 프로젝트로 Google DeepMind의 WAVENET 논문을 구현하는 작업을 시작했다. 프로젝트 진행은 GitHub를 통하여 백업 및 버전 관리를 할 계획인데 오늘 코드 작업 결과물인 `model.py`를 GitHub에 업로드하기 위하여 Git을 처음 사용하였고, 처음 사용하며 느낀 어려웠던 점이나 팁같은 것들을 한 번 정리해 보았다.

## Git Config 설정

일단 Git은 설치가 되어있는 상황에서 시작한다. 먼저 Git에게 나의 정보를 알려주는 과정인 Config 설정을 시작해보자. 다음의 명령어를 사용하여 Config 설정을 끝내자.

```bash
>>> git config user.email "rhc0624@gmail.com"
>>> git config user.name "hcnoh"
```

`git config -global user.name ~` 명령을 사용하여도 되지만 현재 공용 서버를 활용 중이라 global로 선언하지는 않는 걸로 하였다. 이 부분은 좀 헷갈려서 추후에 확인하고 기록하도록 하겠다.
확인 결과 global로 선언하면 현재 repo뿐 아니라 이 계정에서 생성하는 모든 repo의 config가 설정되는 것으로 파악하였다.

## GitHub Repository 생성

그 다음으로는 먼저 GitHub 상에서 `wavenet-tensorflow`이라는 이름으로 repo를 하나 만들어 준다. 이 이름은 현재 작업중인 프로젝트의 디렉토리 이름과 같다는 점을 확인하자.
현재 작업중인 디렉토리의 경로는 `~/wavenet-tensorflow`이다. 이 디렉토리 상에서 `git init` 명령을 통하여 이 디렉토리 `~/wavenet-tensorflow`이 로컬 git repository라고 선언한다.

```bash
>>> git init
Initialized empty Git repository in /home/hcnoh/wavenet-tensorflow/.git/
```

초기화가 제대로 되었음을 확인할 수 있다.

## Push & Pull

현재 `model.py`의 오늘 작업은 끝난 상황이라 바로 push를 해보려고 하였다. 로컬 저장소와 원격 origin을 연결하기 위하여 다음의 명령어를 사용하였다.

```bash
>>> git remote add origin https://github.com/hcnoh/wavenet-tensorflow.git
>>> git remote -v
origin  https://github.com/hcnoh/wavenet-tensorflow.git (fetch)
origin  https://github.com/hcnoh/wavenet-tensorflow.git (push)
```

`git remote -v` 명령을 통하여 제대로 로컬 저장소가 원격 origin에 연결되었는지 확인할 수 있다.
바로 push를 해본다.

```bash
>>> git push
fatal: The current branch master has no upstream branch.
To push the current branch and set the remote as upstream, use

    git push --set-upstream origin master
```

에러가 난다. 에러 메시지에서 추천해주는 명령어 역시 에러가 난다. 다음의 [링크](http://www.talkdev.net/git-%EA%B0%84%EB%8B%A8%ED%95%9C-%EC%82%AC%EC%9A%A9%EB%B2%95/)로부터 해결 방법을 찾아봤다.
먼저 `git add`를 해본다.

```bash
>>> git add .
```

`git add 파일명` 이지만 `.` 하나를 찍어서 현재 디렉토리 전체 파일을 add할 수 있었다. 다시 push를 해보자.

```bash
>>> git push --set-upstream origin master
error: src refspec master does not match any.
error: failed to push some refs to 'https://github.com/hcnoh/wavenet-tensorflow.git'
```

에러 메시지를 검색해보니 다음 [링크](http://yjoo00.tistory.com/111)와 같은 팁을 확인할 수 있었다. 위 링크에 의하면 repo에 아직 commit을 한 적이 없기 때문에 원격 origin에 추가할 branch 또한 존재하지 않아서 발생한 에러라고 한다. 해결하기 위하여 다음의 명령어를 차례로 쳐보았다.

```bash
>>> touch initial
>>> git add initial
>>> git commit -m "initial commit"
[master (root-commit) fa9e359] initial commit
 2 files changed, 160 insertions(+)
 create mode 100644 initial
 create model 100644 model.py
```

제대로 commit은 되는 것 같다. 그 다음 단계로

```bash
>>> git push -u origin master
Username for 'https://github.com': hcnoh
Password for 'https://hcnoh@github.com':
```

위의 명령을 실행하였으나

```bash
To https://github.com/hcnoh/wavenet-tensorflow.git
 ! [rejected]       master -> master (fetch first)
 error: failed to push some refs to 'https://github.com/hcnoh/wavenet-tensorflow.git'
 hint: Updates were rejected because the remote contains work that you do
 hint: not have locally. This is usually caused by another repository pushing
 hint: to the same ref. You may want to first integrate the remote changes
 hint: (e.g., 'git pull ...') before pushing again.
 hint: See the 'Note about fast-forwards' in 'git push --help' for details.
```

또 에러가 발생하였다. 문제의 원인은 원격 origin 상에 있는 README.md 파일이었다. 이 파일이 로컬 저장소에는 없지만 원격 origin 상에 존재하기 때문에 이 에러가 발생한 것이다. 해결법은 다음과 같다. 먼저 push 전에 pull을 통하여 로컬에 없는 파일을 가져와 주고 push를 하면 되는 것이다.

```bash
>>> git pull origin master
warning: no common commits
remote: Counting objects: 3, done.
remote: Compressing objects: 100% (2/2), done.
remote: Total 3 (delta 0), reused 0 (delta 0), pack-reused 0
Unpacking objects: 100% (3/3), done.
From https://github.com/hcnoh/wavenet-tensorflow
 * branch           master      -> FETCH_HEAD
 * [new branch]     master      -> origin/master
Merge made by the 'recursive' strategy.
 README.md | 2 ++
 1 file changed, 2 insertions(+)
 create mode 100644 README.md
```

```bash
>>> git push origin master
Username for 'https://github.com': hcnoh
Password for 'https://hcnoh@github.com':
Counting objects: 6, done.
Delta compression using up to 12 threads.
Compressing objects: 100% (5/5), done.
Writing objects: 100% (6/6), 2.56 KiB | 0 bytes/s, done.
Total 6 (delta 0), reused 0 (delta 0)
To https://github.com/hcnoh/wavenet-tensorflow.git
    2e4d61d..03a125c    master -> master
```

```bash
>>> ls
initial  model.py  README.md
```
![](https://guides.github.com/activities/hello-world/branching.png)

![](https://github.com/hcnoh/blog_temp/blob/master/assets/img/2018-08-18-git-first-time/01.png)

로컬 및 origin이 동기화가 제대로 된 것을 확인할 수 있다.
