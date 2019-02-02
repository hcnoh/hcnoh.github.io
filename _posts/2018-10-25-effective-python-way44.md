---
layout: post
title: "[Effective Python] WAY 44. copyreg로 pickle을 신뢰할 수 있게 만들자"
date: 2018-10-25 10:56:25
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- Effective Python
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

- `unpickle_game_state` 헬퍼도 정의

```python
def unpickle_game_state(kwargs):
    return GameState(**kwargs)
```

- 이제 내장 모듈 `copyreg`로 `GameState` 객체와 직렬화 함수를 등록

```python
copyreg.pickle(GameState, pickle_game_state)
```

- 이전과 동일하게 동작하는 것 확인

```python
state = GameState()
state.points += 1000
serialized = pickle.dumps(state)
state_after = pickle.loads(serialized)
print(state_after.__dict__)

>>>
{"lives": 4, "level": 0, "points": 1000}
```

- 등록 이후에는 `GameState`의 정의를 변경 => 플레이어에게 사용할 마법의 개수를 부여 => 즉, `magic` 필드를 추가

```python
class GameState(object):
    def __init__(self, level=0, lives=4, points=0, magic=5):
        # ...
```

- 객체를 역직렬화 해보면 => 빠진 속성 없이 올바른 게임 데이터를 만들어 냄
  - `unpickle_game_state`가 `GameState` 생성자를 직접 호출하기 때문
  - 생성자의 키워드 인수는 파라미터가 빠지면 기본값을 가지게 됨 => 새로 추가된 `magic` 필드는 기본값

```python
state_after = pickle.loads(serialized)
print(state_after.__dict__)

>>>
{"level": 0, "points": 1000, "magic": 5, "lives": 4}
```

## 클래스 버전 관리
- 필드를 제거하여 파이썬 객체가 하위 호환성을 유지하지 않게 하기를 원함
  - 기본 인수를 사용한 직렬화는 동작하지 않음

- 게임에서 생명이라는 개념을 없애고 싶은 경우 => `GameState`가 생명 필드를 더는 포함하지 않도록 재정의

```python
class GameState(object):
    def __init__(self, level=0, points=0, magic=5):
        # ...
```

- 문제점? 이전 게임 데이터의 역직렬화를 깨뜨림
  - 이전 데이터에 있던 모든 필드는 (그 중 하나가 클래스에서 삭제되어도) `unpickle_game_state` 함수에 의해 `GameState` 생성자로 넘겨짐

```python
pickle.loads(serialized)

>>>
TypeError: __init__() got an unexpected keyword argument "lives"
```

- 해결책:
  - `copyreg`에 제공하는 함수에 버전 파라미터를 추가

```python
def pickle_game_state(game_state):
    kwargs = game_state.__dict__
    kwargs["version"] = 2   # 버전 파라미터 추가
    return unpickle_game_state, (kwargs,)
```

- 데이터의 이전 버전에는 `version` 인수가 없음 => 이에 맞춰 `GameState` 생성자에 넘길 인수를 조작

```python
def unpickle_game_state(kwargs):
    version = kwargs.pop("version", 1)  # version 인수를 pop하고 없으면 1로 디폴트
    if version == 1:
        kwargs.pop("lives")
    return GameState(**kwargs)
```

- 이전 객체를 역직렬화하는 기능이 제대로 동작하는지 확인

```python
copyreg.pickle(GameState, pickle_game_state)
state_after = pickle.loads(serialized)
print(state_after.__dict__)

>>>
{"magic": 5, "level": 0, "points": 1000}
```

## 안정적인 임포트 경로
- `pickle`을 사용하다 부딪힐 만한 또 다른 문제
  - 클래스의 이름을 변경하면 기능이 망가짐
- `GameState` 클래스의 이름을 `BetterGameState`로 변경 => 프로그램 전체에서 이전 클래스를 삭제하기를 원함

```python
class BetterGameState(object):
    def __init__(self, level=0, points=0, magic=5):
        # ...
```

- `GameState` 객체를 역직렬화하려고 해보면? 실패

```python
pickle.loads(serialized)

>>>
AttributeError: Can't get attribute "GameState" on <module "__main__" from "my_code.py">
```

- 이러한 예외가 발생하는 이유?
  - 직렬화된 객체의 클래스를 임포트할 경로가 피클 데이터에 인코드되어 있기 때문

```python
print(serialized[:25])

>>>
b'\x80\x03c__main__\nGameState\nq\x00)'
```

- 해결책? `copyreg`를 이용 => 객체를 언피클하는 데 사용할 함수의 안정적인 식별자를 설정
  - 이 방식으로 역직렬화할 때 다른 클래스로 변환할 수 있음

```python
copyreg.pickle(BetterGameState, pickle_game_state)
```

- 이렇게 하면 직렬화된 데이터에 `BetterGameState`가 아닌 `pickle_game_state`의 임포트 경로가 인코드되어 있음

```python
state = BetterGameState()
serialized = pickle.dumps(state)
print(serialized[:35])

>>>
b'\x80\x03c__main__\unpickle_game_state\nq\x00}'
```

- 위 방법의 유일한 문제:
  - `unpickle_game_state` 함수가 존재하는 모듈의 경로를 변경할 수 없음
  - 어떤 함수로 데이터를 직렬화한 후에는 나중에 역직렬화할 때 사용할 수 있도록 해당 임포트 경로에 함수를 남겨둬야 함
