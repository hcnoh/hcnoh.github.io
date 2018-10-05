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
```

```bash
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
... (생략)
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
... (생략)
```

이번에는 에러가 나지 않는다. 아마 잘 설치가 된 듯 하다. `pip --version` 명령을 통해서 설치가 제대로 되었는지 확인해보자.

```bash
>>> pip --version
The program 'pip' is currently not installed. You can install it by typing:
sudo apt install python-pip
```

```bash
>>> pip3 --version
pip 8.1.1 from /usr/lib/python3/dist-packages (python 3.5)
```

파이썬3이기 때문에 앞으로는 `pip` 명령 대신에 `pip3` 명령을 사용해야 하는 듯 하다. `pip3` 업데이트를 한 번 해준다.

```bash
>>> pip3 install --upgrade pip
Collecting pip
  Downloading https://files.pythonhosted.org/packages/5f/25/e52d3f31441505a5f3af41213346e5b6c221c9e086a166f3703d2ddaf940/pip-18.0-py2.py3-none-any.whl (1.3MB)
    100% |████████████████████████████████| 1.3MB 1.2MB/s
Installing collected packages: pip
Successfully installed pip-8.1.1
You are using pip version 8.1.1, however version 18.0 is available.
You should consider upgrading via the 'pip install --upgrade pip' command.
```

뭔가 좀 이상하다. 다시 버전 확인을 해본다.

```bash
>>> pip3 version
Traceback (most recent call last):
  File "/usr/bin/pip3", line 9, in <module>
    from pip import main
ImportError: cannot import name 'main'
```

```bash
>>> pip --version
pip 18.0 from /home/hcnoh/.local/lib/python3.5/site-packages/pip (python 3.5)
```

아마도 업데이트 이후에는 `pip3`를 쓸 필요없이 바로 `pip`을 쓰면 되는 것 같다. 원하는대로 설치가 되었다.

## Virtualenv 설치
`virtualenv` 설치는 생각보다 간단하지는 않았다. 다음의 명령을 먼저 시도해보았다.

```bash
>>> pip install virtualenv
Collecting virtualenv
  Using cached https://files.pythonhosted.org/packages/b6/30/96a02b2287098b23b875bc8c2f58071c35d2efe84f747b64d523721dc2b5/virtualenv-16.0.0-py2.py3-none-any.whl
Installing collected packages: virtualenv
Could not install packages due to an EnvironmentError: [Errno 13] Permission denied: '/usr/local/lib/python3.5/dist-packages/virtualenv.py'
Consider using the `--user` option or check the permissions.
```

```bash
>>> sudo pip install virtualenv
sudo: pip: command not found
```

둘다 되지 않는다. 이유가 뭘까? [여기](https://www.reddit.com/r/learnpython/comments/8qwui6/could_not_install_packages_due_to_an/)에서 힌트를 얻어서 다음의 명령을 시도하였다.

```bash
>>> pip install --user virtualenv
Collecting virtualenv
  Using cached https://files.pythonhosted.org/packages/b6/30/96a02b2287098b23b875bc8c2f58071c35d2efe84f747b64d523721dc2b5/virtualenv-16.0.0-py2.py3-none-any.whl
Installing collected packages: virtualenv
Successfully installed virtualenv-16.0.0
```

제대로 된 것 같다. `pip freeze`를 통해서 마지막으로 확인해보자.

```bash
>>> pip freeze
blinker==1.3
chardet==2.3.0
cloud-init==18.3
command-not-found==0.3
configobj==5.0.6
cryptography==1.2.3
idna==2.0
Jinja2==2.8
jsonpatch==1.10
jsonpointer==1.9
language-selector==0.1
MarkupSafe==0.23
oauthlib==1.0.3
prettytable==0.7.2
pyasn1==0.1.9
pycurl==7.43.0
pygobject==3.20.0
PyJWT==1.3.0
pyserial==3.0.1
python-apt==1.1.0b1+ubuntu0.16.4.2
python-debian==0.1.27
python-systemd==231
PyYAML==3.11
requests==2.9.1
six==1.10.0
ssh-import-id==5.5
ufw==0.35
unattended-upgrades==0.1
urllib3==1.13.1
virtualenv==16.0.0
```

맨 밑에 `virtualenv==16.0.0`이 보인다. 제대로 설치가 되었다.

## numpy 설치
다음의 명령을 먼저 시도해본다.

```bash
>>> pip install numpy
Collecting numpy
  Downloading https://files.pythonhosted.org/packages/75/22/355e68c80802d6f488223788fbda75c1daab83c3ef609153676c1f17be5f/numpy-1.15.2-cp35-cp35m-manylinux1_x86_64.whl (13.8MB)
    100% |████████████████████████████████| 13.8MB 1.8MB/s
Installing collected packages: numpy
Could not install packages due to an EnvironmentError: [Errno 13] Permission denied: '/usr/local/lib/python3.5/dist-packages/numpy'
Consider using the `--user` option or check the permissions.
```

아까랑 같은 에러가 뜬다. 다시 `virtualenv`를 설치했을 때처럼 `--user` 옵션을 걸어준다.

```bash
>>> pip install --user numpy
Collecting numpy
  Using cached https://files.pythonhosted.org/packages/75/22/355e68c80802d6f488223788fbda75c1daab83c3ef609153676c1f17be5f/numpy-1.15.2-cp35-cp35m-manylinux1_x86_64.whl
Installing collected packages: numpy
Successfully installed numpy-1.15.2
```

```bash
>>> pip freeze
blinker==1.3
chardet==2.3.0
cloud-init==18.3
command-not-found==0.3
configobj==5.0.6
cryptography==1.2.3
idna==2.0
Jinja2==2.8
jsonpatch==1.10
jsonpointer==1.9
language-selector==0.1
MarkupSafe==0.23
numpy==1.15.2
oauthlib==1.0.3
prettytable==0.7.2
pyasn1==0.1.9
pycurl==7.43.0
pygobject==3.20.0
PyJWT==1.3.0
pyserial==3.0.1
python-apt==1.1.0b1+ubuntu0.16.4.2
python-debian==0.1.27
python-systemd==231
PyYAML==3.11
requests==2.9.1
six==1.10.0
ssh-import-id==5.5
ufw==0.35
unattended-upgrades==0.1
urllib3==1.13.1
virtualenv==16.0.0
```

`numpy==1.15.2`가 보인다. 제대로 설치가 되었다.

## Git 설치
다음의 명령을 시도해본다.

```bash
>>> sudo apt install git-all
... (생략)
Processing triggers for libc-bin (2.23-0ubuntu10) ...
Processing triggers for systemd (229-4ubuntu21.4) ...
Processing triggers for ureadahead (0.100.0-19) ...
Processing triggers for ufw (0.35-0ubuntu2) ...
Processing triggers for libgdk-pixbuf2.0-0:amd64 (2.32.2-1ubuntu1.5) ...
Processing triggers for dbus (1.10.6-1ubuntu3.3) ...
```

좀 시간이 걸렸으나 무사히 깔린 것 같다. 마지막으로 확인해보자.

```bash
>>> git --version
git version 2.7.4
```

## Dotfiles 세팅
[이 곳](https://github.com/wookayin/dotfiles)의 `dotfiles`를 이용하였다. 다음을 통해서 먼저 `git clone`을 해온다.

```bash
>>> git clone --recursive https://github.com/wookayin/dotfiles.git ~/.dotfiles
... (생략)
remote: Enumerating objects: 5448, done.
remote: Total 5448 (delta 0), reused 0 (delta 0), pack-reused 5448
Receiving objects: 100% (5448/5448), 1.09 MiB | 594.00 KiB/s, done.
Resolving deltas: 100% (3384/3384), done.
Checking connectivity... done.
Submodule path 'zsh/zplug': checked out 'cd82438f89f3d17351bc78cdd424558552e3fb3c'
```
