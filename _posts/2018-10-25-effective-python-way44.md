---
layout: post
title: "WAY 45. copyreg로 pickle을 신뢰할 수 있게 만들자"
date: 2018-10-25 10:56:25
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-25-effective-python-way44
---

## 내장 모듈 pickle
- 직렬화(파이썬 객체 => 바이트 스트림) 또는 역직렬화(바이트 스트림 => 파이썬 객체)하는데 사용
- `pickle`을 사용할 때 주의사항:
  - 신뢰할 수 없는 부분과 통신하는 데 사용하면 안됨
- `pickle`의 문제점:
  - `pickle` 모듈의 직렬화 포맷은 설계 관점에서 안전하지 못함
    - 직렬화한 데이터는 원래의 파이썬 객체를 재구성하는 데 필요한 프로그램을 담기 때문 => `pickle` 페이로드(`payload`)
    - `pickle` 페이로드는 파이썬 프로그램에서 해당 페이로드를 역직렬화하는 부분을 망가뜨릴 수 있음
  - `json` 모듈은 설계 관점에서는 안전
    - 직렬화된 `JSON` 데이터는 객체 계층에 대한 간단한 설명을 포함
    - `JSON` 데이터를 역직렬화한다고 해서 파이썬 프로그램이 추가적인 위협에 노출되지는 않음
    - 따라서 서로 신뢰하지 않는 프로그램이나 사람 간에 통신할 목적으로 사용 가능
- 게임에서 플레이어의 진행 상태를 파이썬 객체로 표현하려는 경우:
  - 게임 상태: 플레이어의 레벨, 남은 생명 수

```python
class GameState(object):
    def __init__(self):
        self.level = 0
        self.lives = 4
# 프로그램은 게임이 실행 중일 때 이 객체를 수정
state = GameState()
state.level += 1 # 플레이어가 레벨을 통과함
state.lives -= 1 # 플레이어가 재도전해야 함
```

- 사용자가 게임을 끝냄 => 게임의 상태를 파일에 저장해서 나중에 제개할 수 있게 함 => `pickle` 모듈 이용

```python
# GameState 객체를 파일에 직접 덤프하는 코드
state_path = "/tmp/game_state.bin"
with open(state_path, "wb") as f:
    pickle.dump(state, f)
```

- 나중에 파일을 로드하고 직렬화한 적이 없는 것처럼 `GameState` 객체를 복원

```python
with open(state_path, "rb") as f:
    state_after = pickle.load(f)
print(state_after.__dict__)

>>>
{"lives": 3, "level": 1}
```

- 위 방법의 문제점:
  - 시간이 지남에 따라 게임의 기능을 확장하기 어려움
- 플레이어가 포인트를 쌓아가게 하고 싶은 경우
  - 플레이어의 포인트를 추적 => `GameState` 클래스에 새로운 필드를 추가

```python
class GameState(object):
    def __init__(self):
        # ...
        self.points = 0
```

- `pickle`로 `GameState` 클래스의 새로운 버전을 직렬화하는 기능은 이전과 동일하게 동작
- 하지만 오래 전에 저장된 `GameState` 객체를 이용하여 게임을 재개할 수는 없음

```python
state = GameState()
serialized = pickle.dumps(state)
state_after = pickle.loads(serialized)
print(state_after.__dict__)

>>>
{"lives": 4, "level": 0, "points": 0} # 직렬화는 잘 동작
```

```python
with open(state_path, "rb") as f:
    state_after = pickle.load(f)
print(state_after.__dict__)

>>>
{"lives": 3, "level": 1}  # points 속성이 빠짐
```

- 또한 위에서 반환된 객체가 새로 만든 `GameState` 클래스의 인스턴스

```python
assert isinstance(state_after, GameState)
```

## 내장 모듈 copyreg
- 위의 상황에서 봤을때 `pickle`을 단순한 용도 이상으로 사용하게되면 모듈의 기능이 망가짐
- 내장 모듈 `copyreg`를 이용하면 이 문제를 간단히 해결할 수 있음
- 파이썬 객체를 직렬화할 함수를 등록 => `pickle`의 동작을 제어 => `pickle`을 더 신뢰할 수 있게 만듦

## 기본 속성 값
- `GameState` 객체가 언피클링 후에 항상 모든 속성을 담음을 보장하기를 원함
  - 가장 간단한 방법: 기본 인수가 있는 생성자를 사용
  - Better way 19 "키워드 인수로 선택적인 동작을 제공하자" 참고
- 이 방법으로 생성자 재정의

```python
class GameState(object):
    def __init__(self, level=0, lives=4, points=0):
        self.level = level
        self.lives = lives
        self.points = points
```

- 이 생성자를 피클용으로 사용하기를 원함
  - `GameState` 객체를 받아 `copyreg` 모듈용 파라미터 튜플로 변환하는 헬퍼 함수를 정의

```python
def pickle_game_state(game_state):
    kwargs = game_state.__dict__
    return unpickle_game_state, (kwargs,)
```

