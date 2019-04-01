---
layout: post
use_math: true
title: "[Ubuntu] SSH 서버 구축 방법"
date: 2019-03-25 10:19:43
tagline: "내 Ubuntu PC를 SSH를 이용하여 서버화하여 망 내의 다른 로컬에서 접속할 수 있게끔 서버 구축 방법 정리"
categories:
- Ubuntu
tags:
- linux
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2019-03-25-ubuntu-ssh-server
---

이번 포스팅은 아래의 링크를 참고하여 작성하였다.
- [링크1](https://cupjoo.tistory.com/98)

## SSH 설치
Ubuntu SSH 서버를 이용하기 위해서는 먼저 SSH가 설치되어 있어야 한다. 설치 방법은 간단하게 아래와 같다.

```bash
>>> sudo apt-get install ssh
```

설치가 이루어질 것이다.

```bash
...(생략)
Setting up ssh (1:7.2p2-4ubuntu2.8) ...
Setting up ncurses-term (6.0+20160213-1ubuntu1) ...
Setting up ssh-import-id (5.5-0ubuntu1) ...
Processing triggers for systemd (229-4ubuntu21.16) ...
Processing triggers for ureadahead (0.100.0-19) ...
Processing triggers for ufw (0.35-0ubuntu2) ...
```

## SSH 서비스 시작
SSH 서비스를 시작하기 위해서는 관리자 권한인 `root`로 로그인이 가능하게 설정되어 있어야 한다. `root`로 로그인 설정이 되어있는 상태라면 아래와 같이 SSH 서비스를 시작할 수 있을 것이다.

```bash
>>> sudo service ssh start
```

## SSH 서버 상태 확인
다음의 명령을 통해서 서비스 중인 SSH 서버의 상태를 확인할 수 있다.

```bash
>>> service ssh status
● ssh.service - OpenBSD Secure Shell server
   Loaded: loaded (/lib/systemd/system/ssh.service; enabled; vendor preset: enab
   Active: active (running) since 수 2019-03-06 10:41:48 KST; 6min ago
 Main PID: 14421 (sshd)
   CGroup: /system.slice/ssh.service
           └─14421 /usr/sbin/sshd -D

 3월 06 10:41:48 hyungcheol_noh systemd[1]: Starting OpenBSD Secure Shell server
 3월 06 10:41:48 hyungcheol_noh sshd[14421]: Server listening on 0.0.0.0 port 22
 3월 06 10:41:48 hyungcheol_noh sshd[14421]: Server listening on :: port 22.
 3월 06 10:41:48 hyungcheol_noh systemd[1]: Started OpenBSD Secure Shell server.
 3월 06 10:48:28 hyungcheol_noh systemd[1]: Started OpenBSD Secure Shell server.
```

또한 다음의 명령을 통해서 서버 상에서 실행되고 있는 프로세스를 확인할 수도 있다.

```bash
>>> ps -ef |grep sshd
root     14421     1  0 10:41 ?        00:00:00 /usr/sbin/sshd -D
hcnoh    14852 13364  0 10:49 pts/20   00:00:00 grep --color=auto sshd
```

또한 실행중인 서버의 IP 주소 및 포트 번호를 확인할 수 있다.

```bash
>>> sudo netstat -ntlp |grep sshd
tcp        0      0 0.0.0.0:22              0.0.0.0:*               LISTEN      14421/sshd      
tcp6       0      0 :::22                   :::*                    LISTEN      14421/sshd
```

위의 결과는 22번 포트를 가리키고 있는 것으로 확인이 된다. 만약 포트 번호를 바꾸고 싶다면 `/etc/ssh/sshd_config` 파일을 수정하여 바꿀 수 있다. 또한 이 파일에서 `PermitRootLogin prohibit-password`을 `PermitRootLogin yes`로 수정하여 위에서 말한 `root` 로그인이 가능하게끔 설정할 수도 있다.

마지막으로 `ifconfig` 명령을 통해서 다른 컴퓨터에서 현재 SSH 서버에 접속하기 위해서 서버에 할당된 IP 주소를 확인할 수 있다.

```bash
>>> ifconfig
enp2s0    Link encap:Ethernet  HWaddr b0:6e:bf:35:a1:f2  
          inet addr:192.2.24.2  Bcast:192.2.255.255  Mask:255.255.0.0
          inet6 addr: fe80::ffa8:3b0b:faf5:c62e/64 Scope:Link
          UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
          RX packets:28898912 errors:0 dropped:63316 overruns:0 frame:0
          TX packets:5839363 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:12366544149 (12.3 GB)  TX bytes:1417776292 (1.4 GB)

lo        Link encap:Local Loopback  
          inet addr:127.0.0.1  Mask:255.0.0.0
          inet6 addr: ::1/128 Scope:Host
          UP LOOPBACK RUNNING  MTU:65536  Metric:1
          RX packets:221597 errors:0 dropped:0 overruns:0 frame:0
          TX packets:221597 errors:0 dropped:0 overruns:0 carrier:0
          collisions:0 txqueuelen:1000 
          RX bytes:38069657 (38.0 MB)  TX bytes:38069657 (38.0 MB)
```
