---
layout: post
title: "Ubuntu 자주 사용하는 bash 명령어 정리"
date: 2018-10-09 21:45:48
tagline: "Ubuntu 16.04에서 작업 도중에 자주 사용되는 bash 명령어를 정리"
categories:
- Ubuntu 16.04
tags:
- linux
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-09-ubuntu-frequent-bash-command
---

## 경로 관련
- `home` 경로: `~`
  - 예제: `home`에다가 `temp`라는 이름의 디렉토리 생성

```bash
>>> mkdir ~/temp
```

- 바로 상위 경로: `..`
  - 예제: 현재 디렉토리의 상위 디렉토리로 이동

```bash
>>> cd ..
```

- 현재 경로: `.`
  - 예제: 현재 디렉토리에 `temp`라는 이름의 디렉토리 생성

```bash
>>> mkdir ./temp
```

## 파일/디렉토리 생성, 이동, 삭제
- 디렉토리 생성: `mkdir`
  - 예제: `dir_name`라는 이름의 디렉토리 생성

```bash
>>> mkdir dir_name
```

- 파일 및 디렉토리 삭제: `rm -rf`
  - 예제: `dir_name`라는 디렉토리와 하위 디렉토리 및 파일 전부 삭제

```bash
>>> rm -rf dir_name
```

- 파일 및 디렉토리 복사: `cp -r`
  - 예제: 현재 위치에 있는 `dir_name`라는 디렉토리를 `home`으로 복사

```bash
>>> cp -r dir_name ~/dir_name
```

## zip 압축 파일 관련
- zip 압축 풀기:

```bash
>>> unzip file_name.zip
```

- 특정 폴더로 zip 압축 풀기:

```bash
>>> unzip file_name.zip -d ./target
```

- 현재 폴더에 있는 내용 zip 압축 하기:

```bash
>>> zip test.zip ./*
```

- 하위 폴더까지 전부 압축:

```bash
>>> zip -r test.zip ./*
```
