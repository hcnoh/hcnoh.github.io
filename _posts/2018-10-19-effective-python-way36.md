---
layout: post
title: "WAY 36. 자식 프로세스를 관리하려면 subprocess를 사용하자"
date: 2018-10-19 19:59:27
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-19-effective-python-way36
---

## 병행성과 병렬성
- 병행성(`concurrency`):
  - 컴퓨터가 여러 일을 마치 동시에 하듯이 수행하는 것
  - 프로그램을 교대로 실행하여 프로그램들이 동시에 실행하는 것처럼 보이게 함
  - 작업 속도가 향상되지는 않음
- 병렬성(`parallelism`):
  - 실제로 여러 작업을 동시에 실행: CPU 코어가 여러 개
  - 각 CPU 코어가 각기 다른 프로그램의 명령어(`instruction`)를 실행 => 각 프로그램이 같은 순간에 실행하게 해줌
  - 작업 속도가 향상
- 단일 프로그램 안에서 병행성을 잘 활용하면 특정 유형의 문제를 더욱 쉽게 해결할 수 있음
- 파이썬을 이용하면 병행 프로그램을 쉽게 작성할 수 있음

## 파이썬의 자식 프로세스
- 파이썬은 실전에서 단련된 자식 프로세스 실행과 관리용 라이브러리를 갖추고 있음
  - 명령줄(`command line`) 유틸리티 같은 도구들과 연계하기 좋은 언어
- 파이썬의 서브프로세스 실행 방법: `popen`, `popen2`, `os.exec*` 등
- 최근에는 가장 간단한 내장 모듈 `subprocess`를 많이 사용

## subprocess 모듈
- 예제
  - `Popen` 생성자: 프로세스를 시작
  - `communicate` 메서드: 자식 프로세스의 출력을 읽어옴, 자식 프로세스가 종료할 때까지 대기

```python
proc = subprocess.Popen(["echo", "Hello from the child!"],  # 이 부분 확인 필요: 쉘 스크립트
                        stdout=subprocess.PIPE)
out, err = proc.communicate()
print(out.decode("utf-8"))

>>>
Hello from the child!
```

- 자식 프로세스는 부모 프로세스와 파이썬 인터프리터와는 독립적으로 실행
- 자식 프로세스의 상태는 파이썬이 다른 작업을 하는 동안 주기적으로 폴링(`polling`)됨

```python
proc = subprocess.Popen(["sleep", "0.3"])
while proc.poll() is None:
    print("Working...")
    # 시간이 걸리는 작업 몇 개를 수행함
    # ...
print("Exit status", proc.poll())

>>>
Working...
Working...
Exit status 0
```

- 부모에서 자식 프로세스를 떼어냄 => 부모 프로세스가 자유롭게 여러 자식 프로세스를 병렬로 실행할 수 있음
- 자식 프로세스를 떼어내려면?
  - 모든 자식 프로세스를 먼저 시작하면 됨

```python
def run_sleep(period):
    proc = subprocess.Popen(["sleep", str(period)])
    return proc

start = time()
procs = []
for _ in range(10):
    proc = run_sleep(0.1)
    procs.append(proc)
```

- 이후에는 `communicate` 메서드로 자식 프로세스들이 I/O를 마치고 종료하기를 기다림

```python
for proc in procs:
    proc.communicate()
end = time()
print("Finished in %.3f seconds" % (end - start))

>>>
Finished in 0.177 seconds
```

