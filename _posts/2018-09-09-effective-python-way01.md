---
layout: post
title: "WAY 1. 사용중인 파이썬의 버전을 알자"
date: 2018-09-09 20:3:59
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-09-09-effective-python-way01
---

앞으로 스터디에서 다를 예제들의 파이썬 버전은 파이썬 3.4가 될 것이다. 또한 파이썬 2.7과의 비교를 위한 예제들 역시 제공될 것이다.

이번 포스팅은 다음의 링크를 참고하여 작성하였다.
- [출처 1](http://khanrc.tistory.com/entry/%EB%8B%A4%EC%96%91%ED%95%9C-Python%EB%93%A4#fnref-f1)

## 커맨드 라인 상에서 파이썬의 버전 확인:
- 기본적으로 CPython 런타임 환경의 여러 버전이 미리 설치
- 커맨드 라인 상에서 사용되는 기본 파이썬의 버전 확인: `--version` 플래그를 이용
- 파이썬 3는 특별히 `python 3` 명령 사용
- 파이썬 2.7의 버전 확인:

```bash
>>> python --version
Python 2.7.8
```

- 파이썬 3의 버전 확인:

```bash
>>> python3 --version
Python 3.4.2
```

## 파이썬 런타임 버전 확인:
- 런타임에 사용중인 파이썬 버전 확인: 파이썬 내장 `sys` 모듈
- 이것을 이용하여 런타임마다 모듈 임포트를 다르게 설정해줄 수 있음

```python
import sys
print(sys.version_info)
print(sys.version)

>>>
sys.version_info(major=3, minor=4, micro=2, releaselevel='final', serial=0)
3.4.2 (default, Oct 19 2014, 17:52:17)
[GCC 4.2.1 Compatible Apple LLVM 6.0 (clang-600.0.51)]
```

## 파이썬 2 or 3?
- 파이썬 2와 3 모두 파이썬 커뮤니티에서 적극적으로 유지보수 중
- 하지만 파이썬 2의 유지보수는 사실상 중지
- `2to3`, `six` 같은 도구를 이용하면 3으로 쉽게 옮겨갈 수 있음

## 파이썬 런타임 환경 정리
- CPython
1. C로 작성된 인터프리터
2. 우리가 쓰는 일반적인 파이썬 인터프리터
3. GIL(Global Interpreter Lock)로 인한 멀티스레딩 이슈가 있음
- Jython
1. 자바 위에서 돌아가는 인터프리터
2. GIL 이슈 없음
-	IronPython
1. 닷넷 위에서 돌아가는 인터프리터
2. GIL 이슈 없음
- PyPy
1. 파이썬으로 작성된 인터프리터
2. 현재 가장 각광받는 파이썬 구현체
3. Stackless Python도 PyPy에 의하여 거의 잡아 먹힌 상황
- 결론
1. 기본적으로는 CPython
2. 퍼포먼스가 필요한 경우는 PyPy
3. 호환성이 필요한 경우는 Jython/IronPython

## GIL이란? (멀티스레드?)
- 추가 예정
