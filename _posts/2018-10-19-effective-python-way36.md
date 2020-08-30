---
layout: post
title: "[Effective Python] WAY 36. 자식 프로세스를 관리하려면 subprocess를 사용하자"
date: 2018-10-19 19:59:27
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- Development Study
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

## 파이프
- 파이프(`pipe`):
  - 파이썬에서 데이터를 서브프로세스로 보낸 다음 서브프로세스의 결과를 받아올 수 있게 해줌
  - 다른 프로그램을 활용하여 작업을 병렬로 수행할 수 있음
- 데이터 암호화 예제
  - `openssl` 명령줄 도구 사용

```python
def run_openssl(data):
    env = os.environ.copy()
    env["password"] = b'\xe24U\n\xd0Ql3S\x11'
    proc = subprocess.Popen(["openssl", "enc", "-des3", "-pass", "env:password"],
                             env=env,
                             stdin=subprocess.PIPE,
                             stdout=subprocess.PIPE)
    proc.stdin.write(data)
    proc.stdin.flush()    # 자식 프로세스가 입력을 반드시 받게 함
    return proc
```

- 위의 예제에서는 파이프로 암호화 함수에 임의의 바이트를 전달
  - 하지만 실전에서는 사용자 입력, 파일 핸들, 네트워크 소켓 등을 전달할 것

```python
procs = []
for _ in range(3):
    data = os.urandom(10)     # 임의의 바이트 전달
    proc = run_openssl(data)
    procs.append(proc)
```

- 자식 프로세스는 병렬로 실행되고 입력을 소비함
- 다음 예제에서는 자식 프로세스가 종료할 때까지 대기하고 최종 결과를 받음

```python
for proc in procs:
    out, err = proc.communicate()
    print(out[-10:])

>>>
b'o4,G\x91\x95\xfe\xa0\xaa\xb7'
b'\x0b\x01\\\xb1\xb7\xfb\xb2C\xe1b'
b'ds\xc5\xf4;j\x1f\xd0c-'
```

- 한 자식 프로세스의 결과를 다른 프로세스의 입력으로 연결 => 병렬 프로세스 체인(`chain`)을 생성할 수도 있음
- 자식 프로세스를 시작하여 `md5` 명령줄 도구에서 입력 스트림을 소비하는 함수 예제

```python
def run_md5(input_stdin):
    proc = subprocess.Popen(["md5"],
                            stdin=input_stdin,
                            stdout=subprocess.PIPE)
    return proc
```

- 이제 데이터를 암호화하는 `openssl` 프로세스 집합과 암호화된 결과를 `md5`로 해시(`hash`)하는 프로세스 집합을 시작

```python
input_procs = []
hash_procs = []
for _ in range(3):
    data = os.urandom(10)
    proc = run_openssl(data)
    input_procs.append(proc)
    hash_proc = run_md5(proc.stdout)
    hash_procs.append(hash_proc)
```

- 일단 자식 프로세스들이 시작 => 이들 사이의 I/O는 자동으로 일어남 => 그냥 기다리면 됨

```python
for proc in input_procs:
    proc.communicate()
    
for proc in hash_procs:
    out, err = proc.communicate()
    print(out.strip())

>>>

b'7a1822875dcf9650a5a71e5e41e77bf3'
b'd41d8cd98f00b204e9800998ecf8427e'
b'1720f581cfdc448b6273048d42621100'
```

- 자식 프로세스가 종료되지 않거나 입력 또는 출력 파이프에서 블록될 염려가 있다면
  - `communicate` 메서드에 `timeout` 파라미터를 넘겨야 함
  - 이렇게 하면 자식 프로세스가 일정한 시간 내에 응답하지 않을 때 예외가 일어나서 오동작하는 자식 프로세스를 종료할 기회를 얻음
  
```python
proc = run_sleep(10)
try:
    proc.communicate(timeout=0.1)
except subprocess.TimeoutExpired:
    proc.terminate()
    proc.wait()

print("Exit status", proc.poll())

>>>
Exit status -15
```

- `timeout` 파라미터는 파이썬 3.3과 그 이후 버전에서 사용할 수 있음
  - 이전 버전에서 I/O 타임아웃을 강제하려면 내장 모듈 `select`를 `proc.stdin, proc.stdout, proc.strderr`에 사용해야 함
