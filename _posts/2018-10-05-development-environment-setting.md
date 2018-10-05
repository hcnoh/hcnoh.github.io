---
layout: post
title: "개발 환경 세팅"
date: 2018-10-05 18:49:38
tagline: "Window 10 및 Ubuntu 16.04에서 TensorFlow 개발 환경 세팅 방법 정리"
categories:
- Ubuntu 16.04
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-05-development-environment-setting
---

## Window Bash 이용
먼저 파이썬이 설치되어 있는지 확인해 본다.

```bash
>>> python --version
The program 'python' can be found in the following packages:
 * python-minimal
 * python3
Try: sudo apt install <selected package>

>>> python3 --version
Python 3.5.2
```

파이썬2는 깔려있지 않지만 파이썬3는 깔려있는 것으로 확인된다. `3.5.2` 버전을 사용할 것이기 때문에 진행하도록 한다.

`pip`이 설치되어 있는지 확인해보자. `pip freeze` 명령을 통해서 `pip`으로 관리되고 있는 패키지들의 목록을 확인할 수 있다.

```bash
>>> pip freeze
The program 'pip' is currently not installed. You can install it by typing:
sudo apt install python-pip
```

에러 메시지를 보니 `apt-get`을 통하여 `pip`을 설치할 수 있는 모양이다. `apt-get`을 이용하여 `pip`을 설치해보자. 우리는 파이썬3를 사용하기 때문에 다음과 같은 명령을 사용하였다. 단, `sudo` 권한이 없으면 설치가 불가능할 것이다.

```bash
>>> sudo apt-get install python3-pip
... 생략
E: Failed to fetch http://archive.ubuntu.com/ubuntu/pool/main/l/linux/linux-libc-dev_4.4.0-131.157_amd64.deb  404  Not Found [IP: 91.189.88.149 80]

E: Unable to fetch some archives, maybe run apt-get update or try with --fix-missing?
```

잘 되다가 설치가 안되는 듯 하다. `apt-get`을 업데이트 해보기로 한다.

```bash
>>> sudo apt-get update
Hit:1 http://archive.ubuntu.com/ubuntu xenial InRelease
Get:2 http://security.ubuntu.com/ubuntu xenial-security InRelease [107 kB]
Get:3 http://archive.ubuntu.com/ubuntu xenial-updates InRelease [109 kB]
Get:4 http://archive.ubuntu.com/ubuntu xenial-backports InRelease [107 kB]
Get:5 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 Packages [856 kB]
Get:6 http://security.ubuntu.com/ubuntu xenial-security/main amd64 Packages [562 kB]
Get:7 http://security.ubuntu.com/ubuntu xenial-security/main Translation-en [237 kB]
Get:8 http://archive.ubuntu.com/ubuntu xenial-updates/main Translation-en [349 kB]
Get:9 http://security.ubuntu.com/ubuntu xenial-security/universe amd64 Packages [387 kB]
Get:10 http://archive.ubuntu.com/ubuntu xenial-updates/universe amd64 Packages [690 kB]
Get:11 http://security.ubuntu.com/ubuntu xenial-security/universe Translation-en [147 kB]
Get:12 http://security.ubuntu.com/ubuntu xenial-security/multiverse amd64 Packages [3,460 B]
Get:13 http://archive.ubuntu.com/ubuntu xenial-updates/universe Translation-en [279 kB]
Get:14 http://archive.ubuntu.com/ubuntu xenial-updates/multiverse amd64 Packages [16.4 kB]
Get:15 http://archive.ubuntu.com/ubuntu xenial-backports/universe amd64 Packages [7,568 B]
Get:16 http://archive.ubuntu.com/ubuntu xenial-backports/universe Translation-en [4,048 B]
Fetched 3,862 kB in 14s (263 kB/s)
Reading package lists... Done
```

다시 설치를 시도해본다.

```bash
>>> sudo apt-get install python3-pip
Reading package lists... Done
Building dependency tree
Reading state information... Done
The following package was automatically installed and is no longer required:
  libfreetype6
Use 'sudo apt autoremove' to remove it.
The following additional packages will be installed:
  binutils build-essential cpp cpp-5 dpkg-dev fakeroot g++ g++-5 gcc gcc-5 libalgorithm-diff-perl
  libalgorithm-diff-xs-perl libalgorithm-merge-perl libasan2 libatomic1 libc-dev-bin libc6-dev libcc1-0 libcilkrts5
  libdpkg-perl libexpat1-dev libfakeroot libfile-fcntllock-perl libgcc-5-dev libgomp1 libisl15 libitm1 liblsan0
  libmpc3 libmpx0 libpython3-dev libpython3.5-dev libquadmath0 libstdc++-5-dev libtsan0 libubsan0 linux-libc-dev make
  manpages-dev python-pip-whl python3-dev python3-setuptools python3-wheel python3.5-dev
Suggested packages:
  binutils-doc cpp-doc gcc-5-locales debian-keyring g++-multilib g++-5-multilib gcc-5-doc libstdc++6-5-dbg
  gcc-multilib autoconf automake libtool flex bison gdb gcc-doc gcc-5-multilib libgcc1-dbg libgomp1-dbg libitm1-dbg
  libatomic1-dbg libasan2-dbg liblsan0-dbg libtsan0-dbg libubsan0-dbg libcilkrts5-dbg libmpx0-dbg libquadmath0-dbg
  glibc-doc libstdc++-5-doc make-doc python-setuptools-doc
The following NEW packages will be installed:
  binutils build-essential cpp cpp-5 dpkg-dev fakeroot g++ g++-5 gcc gcc-5 libalgorithm-diff-perl
  libalgorithm-diff-xs-perl libalgorithm-merge-perl libasan2 libatomic1 libc-dev-bin libc6-dev libcc1-0 libcilkrts5
  libdpkg-perl libexpat1-dev libfakeroot libfile-fcntllock-perl libgcc-5-dev libgomp1 libisl15 libitm1 liblsan0
  libmpc3 libmpx0 libpython3-dev libpython3.5-dev libquadmath0 libstdc++-5-dev libtsan0 libubsan0 linux-libc-dev make
  manpages-dev python-pip-whl python3-dev python3-pip python3-setuptools python3-wheel python3.5-dev
0 upgraded, 45 newly installed, 0 to remove and 30 not upgraded.
Need to get 3,160 kB/77.4 MB of archives.
After this operation, 201 MB of additional disk space will be used.
Do you want to continue? [Y/n] Y
Get:1 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 binutils amd64 2.26.1-1ubuntu1~16.04.7 [2,309 kB]
Get:2 http://archive.ubuntu.com/ubuntu xenial-updates/main amd64 linux-libc-dev amd64 4.4.0-137.163 [850 kB]
Fetched 3,160 kB in 15s (199 kB/s)
Extracting templates from packages: 100%
...생략
```
