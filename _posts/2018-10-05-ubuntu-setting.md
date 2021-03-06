---
layout: post
title: "[Ubuntu] 개발 환경 세팅"
date: 2018-10-05 18:49:38
tagline: "Ubuntu 16.04에서 TensorFlow 개발을 위한 Tmux 및 zshell 세팅 방법 정리"
categories:
- Development Memo
tags:
- linux
- tensorflow
- tmux
- zshell
- vim
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-05-ubuntu-setting
---

## pip 설치
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
>>> pip3 --version
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
apturl==0.5.2
beautifulsoup4==4.4.1
blinker==1.3
Brlapi==0.6.4
chardet==2.3.0
checkbox-support==0.22
command-not-found==0.3
cryptography==1.2.3
defer==1.0.6
feedparser==5.1.3
guacamole==0.9.2
html5lib==0.999
httplib2==0.9.1
idna==2.0
Jinja2==2.8
language-selector==0.1
louis==2.6.4
lxml==3.5.0
Mako==1.0.3
MarkupSafe==0.23
oauthlib==1.0.3
onboard==1.2.0
padme==1.1.1
pexpect==4.0.1
Pillow==3.1.2
plainbox==0.25
ptyprocess==0.5
pyasn1==0.1.9
pycups==1.9.73
pycurl==7.43.0
pygobject==3.20.0
PyJWT==1.3.0
pyparsing==2.0.3
python-apt==1.1.0b1
python-debian==0.1.27
python-systemd==231
pyxdg==0.25
reportlab==3.3.0
requests==2.9.1
screen-resolution-extra==0.0.0
sessioninstaller==0.0.0
six==1.10.0
system-service==0.3
ubuntu-drivers-common==0.0.0
ufw==0.35
unattended-upgrades==0.1
unity-scope-calculator==0.1
unity-scope-chromiumbookmarks==0.1
unity-scope-colourlovers==0.1
unity-scope-devhelp==0.1
unity-scope-firefoxbookmarks==0.1
unity-scope-gdrive==0.7
unity-scope-manpages==0.1
unity-scope-openclipart==0.1
unity-scope-texdoc==0.1
unity-scope-tomboy==0.1
unity-scope-virtualbox==0.1
unity-scope-yelp==0.1
unity-scope-zotero==0.1
urllib3==1.13.1
usb-creator==0.3.0
virtualenv==16.0.0
xdiagnose==3.8.4
xkit==0.0.0
XlsxWriter==0.7.3
```

`virtualenv==16.0.0`이 보인다. 제대로 설치가 되었다.

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
apturl==0.5.2
beautifulsoup4==4.4.1
blinker==1.3
Brlapi==0.6.4
chardet==2.3.0
checkbox-support==0.22
command-not-found==0.3
cryptography==1.2.3
defer==1.0.6
feedparser==5.1.3
guacamole==0.9.2
html5lib==0.999
httplib2==0.9.1
idna==2.0
Jinja2==2.8
language-selector==0.1
louis==2.6.4
lxml==3.5.0
Mako==1.0.3
MarkupSafe==0.23
numpy==1.15.2
oauthlib==1.0.3
onboard==1.2.0
padme==1.1.1
pexpect==4.0.1
Pillow==3.1.2
plainbox==0.25
ptyprocess==0.5
pyasn1==0.1.9
pycups==1.9.73
pycurl==7.43.0
pygobject==3.20.0
PyJWT==1.3.0
pyparsing==2.0.3
python-apt==1.1.0b1
python-debian==0.1.27
python-systemd==231
pyxdg==0.25
reportlab==3.3.0
requests==2.9.1
screen-resolution-extra==0.0.0
sessioninstaller==0.0.0
six==1.10.0
system-service==0.3
ubuntu-drivers-common==0.0.0
ufw==0.35
unattended-upgrades==0.1
unity-scope-calculator==0.1
unity-scope-chromiumbookmarks==0.1
unity-scope-colourlovers==0.1
unity-scope-devhelp==0.1
unity-scope-firefoxbookmarks==0.1
unity-scope-gdrive==0.7
unity-scope-manpages==0.1
unity-scope-openclipart==0.1
unity-scope-texdoc==0.1
unity-scope-tomboy==0.1
unity-scope-virtualbox==0.1
unity-scope-yelp==0.1
unity-scope-zotero==0.1
urllib3==1.13.1
usb-creator==0.3.0
virtualenv==16.0.0
xdiagnose==3.8.4
xkit==0.0.0
XlsxWriter==0.7.3
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

```bash
>>> cd ~/.dotfiles && python3 install.py
...(생략)
Executing: # Create ~/.gitconfig.secret and user configuration

    [!!!] Please configure git user name and email:
        git config --file ~/.gitconfig.secret user.name "(YOUR NAME)"
        git config --file ~/.gitconfig.secret user.email "(YOUR EMAIL)"


┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃ You have  3 warnings or errors --- check the logs!   ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
   # Update zgen modules and cache (the init file)
   # Change default shell to zsh
   # Create ~/.gitconfig.secret and user configuration


- Please restart shell (e.g. `exec zsh`) if necessary.
- To install some packages locally (e.g. neovim, tmux), try `dotfiles install`
```

중간에 `sudo` 계정의 패스워드를 입력하라고 한 번 뜨는데 입력해주면 잘 설치가 되는 듯 하다. 하지만 아직 끝난 것이 아니다. 다음의 명령을 실행해보자.

```bash
>>> cd ~/.dotfiles/etc
>>> ./ubuntu-setup.sh install_all
... (생략)
Setting up libevent-extra-2.0-5:amd64 (2.0.21-stable-2ubuntu0.16.04.1) ...
Setting up libevent-pthreads-2.0-5:amd64 (2.0.21-stable-2ubuntu0.16.04.1) ...
Setting up libevent-openssl-2.0-5:amd64 (2.0.21-stable-2ubuntu0.16.04.1) ...
Setting up libevent-dev (2.0.21-stable-2ubuntu0.16.04.1) ...
Setting up libtinfo-dev:amd64 (6.0+20160213-1ubuntu1) ...
Setting up libncurses5-dev:amd64 (6.0+20160213-1ubuntu1) ...
Setting up libutempter-dev:amd64 (1.1.6-3) ...
... (생략)
```

그 다음으로 다음의 명령을 수행한다. 그러면 `zshell`이 실행될 것이다.

```bash
>>> exec zsh
... (생략)
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 4 (delta 0), reused 2 (delta 0), pack-reused 0
Unpacking objects: 100% (4/4), done.
Checking connectivity... done.
-- zgen: Creating `/home/hyungcheol/.zgen/init.zsh` ...
```

만약 `exec zsh` 명령이 제대로 실행되지 않는다면 다음과 같이 `zshell`을 설치하면 된다.

```bash
>>> dotfiles install zsh
... (todfir)
mkdir -m 755 -p -- /home/hcnoh/.local/share/zsh/site-functions
mkdir -m 755 -p -- /home/hcnoh/.local/share/zsh/5.4.1/functions
mkdir -m 755 -p -- /home/hcnoh/.local/share/zsh/5.4.1/scripts
make[1]: Entering directory '/tmp/hcnoh/zsh/Doc'
/bin/sh ../mkinstalldirs /home/hcnoh/.local/share/man/man1
mkdir -m 755 -p -- /home/hcnoh/.local/share/man/man1
for file in zsh.1 zshbuiltins.1 zshcalsys.1 zshcompctl.1 zshcompwid.1 zshcompsys.1 zshcontrib.1 zshexpn.1 zshmisc.1 zshmodules.1 zshoptions.1 zshparam.1 zshroadmap.1 zshtcpsys.1 zshzftpsys.1 zshzle.1 zshall.1; do \
    test -s ./$file || exit 1; \
    /usr/bin/install -c -m 644 ./$file /home/hcnoh/.local/share/man/man1/`echo $file | sed 's|zsh|zsh|'` || exit 1; \
done
make[1]: Leaving directory '/tmp/hcnoh/zsh/Doc'
make[1]: Entering directory '/tmp/hcnoh/zsh/Doc'
if test x"/home/hcnoh/.local/share/zsh/5.4.1/help" != x""; then \
    /bin/sh ../mkinstalldirs /home/hcnoh/.local/share/zsh/5.4.1/help; \
    /usr/bin/install -c -m 644 ./help/* /home/hcnoh/.local/share/zsh/5.4.1/help; \
    while read from to; do \
        rm -f /home/hcnoh/.local/share/zsh/5.4.1/help/$to || : ; \
        ln -s $from /home/hcnoh/.local/share/zsh/5.4.1/help/$to; \
    done < ./help.txt; \
fi
mkdir -m 755 -p -- /home/hcnoh/.local/share/zsh/5.4.1/help
make[1]: Leaving directory '/tmp/hcnoh/zsh/Doc'
zsh 5.4.1 (x86_64-unknown-linux-gnu)
[*] Installation successful: zsh


[*] Installation(s) successful. You may need to run `exec zsh` to reflect changes in PATH.
```

이제 `exec zsh`을 실행한다.

```bash
>>> exec zsh
Initializing zgen plugins ...
Cloning into '/home/hcnoh/.zgen/sorin-ionescu/prezto-master'...
remote: Enumerating objects: 233, done.
remote: Counting objects: 100% (233/233), done.
remote: Compressing objects: 100% (220/220), done.
remote: Total 233 (delta 9), reused 112 (delta 4), pack-reused 0
Receiving objects: 100% (233/233), 124.26 KiB | 2.39 MiB/s, done.
Resolving deltas: 100% (9/9), done.
Submodule 'modules/autosuggestions/external' (https://github.com/zsh-users/zsh-autosuggestions.git) registered for path 'modules/autosuggestions/external'
Submodule 'modules/completion/external' (https://github.com/zsh-users/zsh-completions.git) registered for path 'modules/completion/external'
Submodule 'modules/fasd/external' (https://github.com/clvv/fasd.git) registered for path 'modules/fasd/external'
Submodule 'modules/history-substring-search/external' (https://github.com/zsh-users/zsh-history-substring-search.git) registered for path 'modules/history-substring-search/external'
Submodule 'modules/prompt/external/agnoster' (https://github.com/agnoster/agnoster-zsh-theme.git) registered for path 'modules/prompt/external/agnoster'
Submodule 'modules/prompt/external/async' (https://github.com/mafredri/zsh-async.git) registered for path 'modules/prompt/external/async'
Submodule 'modules/prompt/external/powerlevel9k' (https://github.com/bhilburn/powerlevel9k.git) registered for path 'modules/prompt/external/powerlevel9k'
Submodule 'modules/prompt/external/powerline' (https://github.com/davidjrice/prezto_powerline.git) registered for path 'modules/prompt/external/powerline'
Submodule 'modules/prompt/functions/pure' (https://github.com/sindresorhus/pure.git) registered for path 'modules/prompt/external/pure'
Submodule 'modules/syntax-highlighting/external' (https://github.com/zsh-users/zsh-syntax-highlighting.git) registered for path 'modules/syntax-highlighting/external'
Cloning into '/home/hcnoh/.dotfiles/zsh/zgen/sorin-ionescu/prezto-master/modules/autosuggestions/external'...
remote: Enumerating objects: 65, done.        
remote: Counting objects: 100% (65/65), done.        
remote: Compressing objects: 100% (28/28), done.        
remote: Total 1943 (delta 37), reused 65 (delta 37), pack-reused 1878        
Receiving objects: 100% (1943/1943), 450.20 KiB | 895.00 KiB/s, done.
Resolving deltas: 100% (1221/1221), done.
Cloning into '/home/hcnoh/.dotfiles/zsh/zgen/sorin-ionescu/prezto-master/modules/completion/external'...
remote: Enumerating objects: 3984, done.        
remote: Total 3984 (delta 0), reused 0 (delta 0), pack-reused 3984        
Receiving objects: 100% (3984/3984), 1.44 MiB | 2.07 MiB/s, done.
Resolving deltas: 100% (2482/2482), done.
Cloning into '/home/hcnoh/.dotfiles/zsh/zgen/sorin-ionescu/prezto-master/modules/fasd/external'...
remote: Enumerating objects: 1028, done.        
remote: Total 1028 (delta 0), reused 0 (delta 0), pack-reused 1028        
Receiving objects: 100% (1028/1028), 233.81 KiB | 449.00 KiB/s, done.
Resolving deltas: 100% (506/506), done.
Cloning into '/home/hcnoh/.dotfiles/zsh/zgen/sorin-ionescu/prezto-master/modules/history-substring-search/external'...
remote: Enumerating objects: 374, done.        
remote: Total 374 (delta 0), reused 0 (delta 0), pack-reused 374        
Receiving objects: 100% (374/374), 121.59 KiB | 368.00 KiB/s, done.
Resolving deltas: 100% (180/180), done.
Cloning into '/home/hcnoh/.dotfiles/zsh/zgen/sorin-ionescu/prezto-master/modules/prompt/external/agnoster'...
remote: Enumerating objects: 100, done.        
remote: Total 100 (delta 0), reused 0 (delta 0), pack-reused 100        
Receiving objects: 100% (100/100), 6.07 MiB | 3.90 MiB/s, done.
Resolving deltas: 100% (34/34), done.
Cloning into '/home/hcnoh/.dotfiles/zsh/zgen/sorin-ionescu/prezto-master/modules/prompt/external/async'...
remote: Enumerating objects: 12, done.        
remote: Counting objects: 100% (12/12), done.        
remote: Compressing objects: 100% (8/8), done.        
remote: Total 619 (delta 5), reused 8 (delta 4), pack-reused 607        
Receiving objects: 100% (619/619), 155.09 KiB | 667.00 KiB/s, done.
Resolving deltas: 100% (389/389), done.
Cloning into '/home/hcnoh/.dotfiles/zsh/zgen/sorin-ionescu/prezto-master/modules/prompt/external/powerlevel9k'...
remote: Enumerating objects: 11038, done.        
remote: Total 11038 (delta 0), reused 0 (delta 0), pack-reused 11038        
Receiving objects: 100% (11038/11038), 4.12 MiB | 2.28 MiB/s, done.
Resolving deltas: 100% (7263/7263), done.
Cloning into '/home/hcnoh/.dotfiles/zsh/zgen/sorin-ionescu/prezto-master/modules/prompt/external/powerline'...
remote: Enumerating objects: 38, done.        
remote: Total 38 (delta 0), reused 0 (delta 0), pack-reused 38        
Cloning into '/home/hcnoh/.dotfiles/zsh/zgen/sorin-ionescu/prezto-master/modules/prompt/external/pure'...
remote: Enumerating objects: 9, done.        
remote: Counting objects: 100% (9/9), done.        
remote: Compressing objects: 100% (7/7), done.        
remote: Total 854 (delta 3), reused 7 (delta 2), pack-reused 845        
Receiving objects: 100% (854/854), 593.57 KiB | 1.35 MiB/s, done.
Resolving deltas: 100% (450/450), done.
Cloning into '/home/hcnoh/.dotfiles/zsh/zgen/sorin-ionescu/prezto-master/modules/syntax-highlighting/external'...
remote: Enumerating objects: 64, done.        
remote: Counting objects: 100% (64/64), done.        
remote: Compressing objects: 100% (42/42), done.        
remote: Total 5344 (delta 29), reused 45 (delta 22), pack-reused 5280        
Receiving objects: 100% (5344/5344), 1.10 MiB | 2.59 MiB/s, done.
Resolving deltas: 100% (3527/3527), done.
Submodule path 'modules/autosuggestions/external': checked out 'a7f0106b31c2538a36cab30428e6ca65d9a2ae60'
Submodule path 'modules/completion/external': checked out 'cf565254e26bb7ce03f51889e9a29953b955b1fb'
Submodule path 'modules/fasd/external': checked out '48220241e764fdf46b075cd7fe723468aaadde58'
Submodule path 'modules/history-substring-search/external': checked out 'aae3388491c2312c4efb2e86bcb999927bb2900e'
Submodule path 'modules/prompt/external/agnoster': checked out '6bba672c7812a76defc3efed9b6369eeee2425dc'
Submodule path 'modules/prompt/external/async': checked out '58f7ba70f05e75802299848e7e31c7d7a7fd0c97'
Submodule path 'modules/prompt/external/powerlevel9k': checked out '2f4b15041fe31d85dc9ef705b818c3a0e6985da3'
Submodule 'shunit2' (https://github.com/kward/shunit2.git) registered for path 'modules/prompt/external/powerlevel9k/shunit2'
Cloning into '/home/hcnoh/.dotfiles/zsh/zgen/sorin-ionescu/prezto-master/modules/prompt/external/powerlevel9k/shunit2'...
remote: Enumerating objects: 2986, done.        
remote: Total 2986 (delta 0), reused 0 (delta 0), pack-reused 2986        
Receiving objects: 100% (2986/2986), 549.60 KiB | 958.00 KiB/s, done.
Resolving deltas: 100% (1434/1434), done.
Submodule path 'modules/prompt/external/powerlevel9k/shunit2': checked out '07bb3292048a4982aad7247bdd7890f2bf532ece'
Submodule path 'modules/prompt/external/powerline': checked out 'c48e4c69ef5c368ea7cda961ed9d0e298a5ae1fc'
Submodule path 'modules/prompt/external/pure': checked out '7aade5fca2acae3f10e260d0c076af6356da9dc6'
Submodule path 'modules/syntax-highlighting/external': checked out '1e34c4aa0bcbdde5173aab15600784edf0a212fd'
Cloning into '/home/hcnoh/.zgen/mafredri/zsh-async-master'...
remote: Enumerating objects: 10, done.
remote: Counting objects: 100% (10/10), done.
remote: Compressing objects: 100% (9/9), done.
remote: Total 10 (delta 0), reused 5 (delta 0), pack-reused 0
Unpacking objects: 100% (10/10), done.
Cloning into '/home/hcnoh/.zgen/wookayin/pure-master'...
remote: Enumerating objects: 12, done.
remote: Counting objects: 100% (12/12), done.
remote: Compressing objects: 100% (9/9), done.
remote: Total 12 (delta 0), reused 9 (delta 0), pack-reused 0
Unpacking objects: 100% (12/12), done.
Cloning into '/home/hcnoh/.zgen/zdharma/fast-syntax-highlighting-master'...
remote: Enumerating objects: 88, done.
remote: Counting objects: 100% (88/88), done.
remote: Compressing objects: 100% (87/87), done.
remote: Total 88 (delta 29), reused 33 (delta 1), pack-reused 0
Unpacking objects: 100% (88/88), done.
For style globbing-ext, went for fallback style double-quoted-argument
For style here-string-text, went for fallback style subtle-bg
For style here-string-var, went for fallback style back-or-dollar-double-quoted-argument
For style subcommand, went for fallback style reserved-word
Missing style: single-sq-bracket
Missing style: double-sq-bracket
Missing style: double-paren
For style optarg-string, went for fallback style double-quoted-argument
For style optarg-number, went for fallback style mathnum
Missing style: recursive-base
Switched to theme `wook' (current session, and future sessions)
Cloning into '/home/hcnoh/.zgen/wookayin/fzf-fasd-master'...
remote: Enumerating objects: 4, done.
remote: Counting objects: 100% (4/4), done.
remote: Compressing objects: 100% (4/4), done.
remote: Total 4 (delta 0), reused 2 (delta 0), pack-reused 0
Unpacking objects: 100% (4/4), done.
Cloning into '/home/hcnoh/.zgen/zsh-users/zsh-autosuggestions-master'...
remote: Enumerating objects: 67, done.
remote: Counting objects: 100% (67/67), done.
remote: Compressing objects: 100% (59/59), done.
remote: Total 67 (delta 7), reused 34 (delta 3), pack-reused 0
Unpacking objects: 100% (67/67), done.
-- zgen: Creating `/home/hcnoh/.zgen/init.zsh` ...
-- zgen: Initializing completions ...
```

![](/assets/img/2018-10-05-development-environment-setting/01.png)

`zshell`이 제대로 실행이 된 모습이다. `tmux`를 실행해봤으나 실행이 제대로 되지 않는다.

```bash
>>> tmux
zsh: correct tmux to tmuxp [nyae]? 
```

해결하기 위해서 일단 `dotfiles`를 업데이트해주자.

```bash
>>> dotfiles update
...(생략)
Executing: # Create ~/.gitconfig.secret and user configuration

    [!!!] Please configure git user name and email:
        git config --file ~/.gitconfig.secret user.name "(YOUR NAME)"
        git config --file ~/.gitconfig.secret user.email "(YOUR EMAIL)"


┏━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┓
┃ You have  2 warnings or errors --- check the logs!   ┃
┗━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━━┛
   ~/.tmux/plugins/tpm/bin/install_plugins
   # Create ~/.gitconfig.secret and user configuration


- Please restart shell (e.g. `exec zsh`) if necessary.
- To install some packages locally (e.g. neovim, tmux), try `dotfiles install`
+ git stash pop
No stash found.
[*] dotfiles is up-to-date (71b71e1).
```

```bash
>>> dotfiles install tmux
...(생략)
make  install-exec-hook
make[2]: Entering directory '/tmp/hyungcheol/tmux/tmux-2.5'
if test xmdoc = xmdoc; then \
	sed -e "s|@SYSCONFDIR@|/etc|g" ./tmux.1 \
		>./tmux.1.mdoc; \
else \
	sed -e "s|@SYSCONFDIR@|/etc|g" ./tmux.1| \
		gawk -f./mdoc2man.awk >./tmux.1.man; \
fi
/bin/mkdir -p /home/hyungcheol/.local/share/man/man1
/usr/bin/install -c -m 644 ./tmux.1.mdoc \
	/home/hyungcheol/.local/share/man/man1/tmux.1
make[2]: Leaving directory '/tmp/hyungcheol/tmux/tmux-2.5'
make[1]: Nothing to be done for 'install-data-am'.
make[1]: Leaving directory '/tmp/hyungcheol/tmux/tmux-2.5'
tmux 2.5
[*] Installation successful. You may need to run `exec zsh` to reflect changes in PATH.
```

```bash
>>> exec zsh
Initializing zgen plugins ...
For style globbing-ext, went for fallback style double-quoted-argument
For style here-string-text, went for fallback style subtle-bg
For style here-string-var, went for fallback style back-or-dollar-double-quoted-argument
For style subcommand, went for fallback style reserved-word
Missing style: single-sq-bracket
Missing style: double-sq-bracket
Missing style: double-paren
Switched to theme `wook' (current session, and future sessions)
-- zgen: Creating `/home/hyungcheol/.zgen/init.zsh` ...
```

```bash
>>> tmux
```

![](/assets/img/2018-10-05-development-environment-setting/02.png)

`tmux`도 제대로 실행이 되었다. 하지만 기능이 원하는대로 적용이 되지 않은 듯 하다. 다시 `dotfiles update`와 `exec zsh`을 실행한 후에 터미널을 다시 실행해본다.

![](/assets/img/2018-10-05-development-environment-setting/03.png)

이제 기본값으로 `zshell`이 설정되었지만 `tmux`를 실행하면 여전히 `zshell`이 등장하지 않는다. [여기](https://stackoverflow.com/questions/23318284/change-tmux-default-to-zsh)에서 해답을 얻었다. `.tmux.conf` 파일에서 `default shell`에 대한 내용을 추가하는 것이다. 먼저 다음의 명령을 통해 `.tmux.conf` 파일을 실행하자.

```bash
>>> vi .tmux.conf
```

파일의 맨 밑에 다음과 같이 추가해주자.

```shell
# set shell
set -g default-shell /bin/zsh
```

![](/assets/img/2018-10-05-development-environment-setting/04.png)

모든 `tmux` 세션을 종료한 후 터미널을 재시작하고 `tmux`를 실행하면 이제 `zshell`로 실행되는 것을 확인할 수 있을 것이다.

![](/assets/img/2018-10-05-development-environment-setting/05.png)

이제 글자가 깨지는 것을 방지하기 위해서 폰트를 설치해준다. 다음의 작업을 순서대로 따라하면 된다.

```bash
>>> git clone https://github.com/powerline/fonts.git --depth=1
Cloning into 'fonts'...
remote: Enumerating objects: 310, done.
remote: Counting objects: 100% (310/310), done.
remote: Compressing objects: 100% (236/236), done.
remote: Total 310 (delta 76), reused 256 (delta 71), pack-reused 0
Receiving objects: 100% (310/310), 10.34 MiB | 1.64 MiB/s, done.
Resolving deltas: 100% (76/76), done.
Checking connectivity... done.
```

```bash
>>> cd fonts
>>> ./install.sh
Copying fonts...
Resetting font cache, this may take a moment...
Powerline fonts installed to /home/hyungcheol/.local/share/fonts
```
```bash
>>> cd ..
>>> rm -rf fonts
... (생략)
removed 'fonts/Meslo Dotted/Meslo LG L DZ Italic for Powerline.ttf'
removed 'fonts/Meslo Dotted/Meslo LG S DZ Italic for Powerline.ttf'
removed 'fonts/Meslo Dotted/Meslo LG S DZ Regular for Powerline.ttf'
removed 'fonts/Meslo Dotted/fonts.scale'
removed 'fonts/Meslo Dotted/LICENSE.txt'
removed directory 'fonts/Meslo Dotted'
removed directory 'fonts'
```

![](/assets/img/2018-10-05-development-environment-setting/06.png)

설치가 완료되면 글자가 더이상 깨지지않고 잘 나오는 것을 확인할 수 있다.

이렇게 개발 환경 세팅이 완료되었다.

## 문제 해결: vim 색상 적용 문제

`vim` 색상이 아래처럼 제대로 나오지 않는 경우 문제 해결 방법을 정리하였다.

![](/assets/img/2018-10-05-development-environment-setting/07.png)

먼저 `dotfiles update` 명령을 실행해본다.

```bash
>>> dotfiles update
```

아마 `neovim`을 설치하라고 나올 것이다. `neovim`은 `dotfiles install neovim` 명령을 통해서 설치가 가능하다.

```bash
>>> dotfiles install neovim
... (생략)
NVIM v0.3.2-671-g384770556
Build type: RelWithDebInfo
LuaJIT 2.0.5
Compilation: /usr/bin/gcc-5 -Wconversion -U_FORTIFY_SOURCE -D_FORTIFY_SOURCE=1 -O2 -g -DMIN_LOG_LEVEL=3 -Og -g -Wall -Wextra -pedantic -Wno-unused-parameter -Wstrict-prototypes -std=gnu99 -Wvla -fstack-protector-strong -fdiagnostics-color=auto -Wno-array-bounds -DINCLUDE_GENERATED_DECLARATIONS -D_GNU_SOURCE -DNVIM_MSGPACK_HAS_FLOAT32 -DNVIM_UNIBI_HAS_VAR_FROM -I/home/travis/build/neovim/bot-ci/build/neovim/build/config -I/home/travis/build/neovim/bot-ci/build/neovim/src -I/home/travis/build/neovim/bot-ci/build/neovim/.deps/usr/include -I/usr/include -I/home/travis/build/neovim/bot-ci/build/neovim/build/src/nvim/auto -I/home/travis/build/neovim/bot-ci/build/neovim/build/include
Compiled by travis@travis-job-3150864c-8c48-4f93-b409-0d73d1b696d3

Features: +acl +iconv +jemalloc +tui 
See ":help feature-compile"

   system vimrc file: "$VIM/sysinit.vim"
  fall-back for $VIM: "/share/nvim"

Run :checkhealth for more info
[*] Installation successful. You may need to run `exec zsh` to reflect changes in PATH.
```

그 다음을 `exec zsh` 명령을 통해서 `zshell`을 재시작한다.

```bash
>>> exec zsh
Initializing zgen plugins ...
For style globbing-ext, went for fallback style double-quoted-argument
For style here-string-text, went for fallback style subtle-bg
For style here-string-var, went for fallback style back-or-dollar-double-quoted-argument
For style subcommand, went for fallback style reserved-word
Missing style: single-sq-bracket
Missing style: double-sq-bracket
Missing style: double-paren
Switched to theme `wook' (current session, and future sessions)
-- zgen: Creating `/home/hyungcheol/.zgen/init.zsh` ...
```

이제 `vim`을 실행해본다.

![](/assets/img/2018-10-05-development-environment-setting/08.png)

더 이상해졌다. [링크](https://github.com/wookayin/dotfiles/pull/9)를 통해서 확인한 결과, 터미널이 24비트 색상을 지원하지 않는 경우에 발생하는 문제라고 한다. [링크](https://github.com/wookayin/dotfiles/pull/9)를 참고하여서 `~/.vimrc` 파일의 맨 밑에 다음을 추가해주었다.

```bash
set notermguicolors
```

![](/assets/img/2018-10-05-development-environment-setting/09.png)

추가적으로 `vim`을 실행할 시 `pylint` 및 `pycodestyle`를 설치해주라는 경고가 나올 수도 있다. 이 경우에는 아래의 명령을 통해서 설치해주면 된다.

```bash
>>> pip install pylint
>>> pip install pycodestyle
```

`--user` 옵션을 추가해주라는 에러가 발생하면 다음과 같이 옵션을 추가하여 실행시키면 된다.

```bash
>>> pip install --user pylint
>>> pip install --user pycodestyle
```

제대로 나온다. 글자가 조금 깨지는 듯 하지만 이유는 추후에 알아내서 포스팅하도록 하겠다.

