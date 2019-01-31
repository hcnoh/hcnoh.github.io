---
layout: post
use_math: true
title: "Python 네임스페이스 개념 정리"
date: 2019-01-30 10:14:05
tagline: "파이썬에서 변수와 객체의 매핑 관계를 나타내는 네임스페이스의 개념 및 변수 스코프에 대한 개념 정리"
categories:
- Python
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2019-01-30-python-namespace
---

이번 포스팅은 다음의 링크들을 참고하여 작성하였다:
- [링크1](https://hashcode.co.kr/questions/3/if-__name__-__main__%EC%9D%80-%EC%99%9C%EC%93%B0%EB%82%98%EC%9A%94)
- [링크2](https://soooprmx.com/archives/5854)
- [링크3](https://blog.confirm.ch/python-namespaces/)
- [링크4](https://www.programiz.com/python-programming/namespace)

파이썬에서는 보통 메인 모듈의 구성이 다음과 같이 되어있다.

```python
def main():
    #...


if __name__ == '__main__':
    main()


```

사실 파이썬은 스크립트 언어이며 다른 컴파일 언어들과는 다르게 자동으로 실행되는 메인함수가 없다. 따라서 일단 실행을 하게되면 들여쓰기가 되지 않은 모든 코드(Level 0 코드)를 실행하게 되며, 함수 및 클래스는 정의만 하게되고 실행되지는 않는다.

따라서 위의 예제처럼 굳이 `main` 함수를 정의하고 밑에 `if` 문으로 실행시켜야 할 이유를 찾기 힘들 수 있다. 하지만 대부분의 코드들이 위의 형식을 따르고 있으며 따라서 저런식으로 구현하는 데에는 이유가 있을 것이다. 그 이유가 무엇인지 알아보는 과정에서 네임스페이스의 개념에 대해서 알게 되었고 그에 대한 내용을 정리하기로 하였다.

## 파이썬의 네임스페이스
네임스페이스(namespace, 이름공간)란 프로그래밍 언어에서 특정한 객체(Object)를 이름(Name)에 따라 구분할 수 있는 범위를 의미한다. 파이썬 내부의 모든것은 객체로 구성되며 이들 각각은 특정 이름과의 매핑 관계를 갖게 되는데 이 매핑을 포함하고 있는 공간을 네임스페이스라고 한다.

네임스페이스가 필요한 이유는 다음과 같다. 프로그래밍을 수행하다보면 모든 변수 이름과 함수 이름을 정하는 것이 중요한데 이들 모두를 겹치지 않게 정하는 것은 사실상 불가능하다.

> 따라서 프로그래밍언어에서는 네임스페이스라는 개념을 도입하여, 특정한 하나의 이름이 통용될 수 있는 범위를 제한한다. 즉, 소속된 네임스페이스가 다르다면 같은 이름이 다른 개체를 가리키도록 하는 것이 가능해진다. ([참고](https://soooprmx.com/archives/5854))

파이썬의 네임스페이스는 3가지로 분류할 수 있다:
- 전역 네임스페이스: `모듈별로` 존재하며, 모듈 전체에서 통용될 수 있는 이름들이 소속된다.
- 지역 네임스페이스: `함수 및 메서드 별로` 존재하며, 함수 내의 지역변수들의 이름들이 소속된다.
- 빌트인 네임스페이스: `기본 내장 함수 및 기본 예외`들의 이름들이 소속된다. 파이썬으로 작성된 모든 코드 범위가 포함된다.

![](/assets/img/2019-01-30-python-namespace/02.jpg)

(출처: [https://www.programiz.com/python-programming/namespace](https://www.programiz.com/python-programming/namespace))

파이썬의 네임스페이스는 다음과 같은 특징들을 가지고 있다.
- 네임스페이스는 딕셔너리 형태로 구현된다.
- 모든 이름 자체는 문자열로 되어있고 각각은 해당 네임스페이스의 범위에서 실제 객체를 가리킨다.
- 이름과 실제 객체 사이의 매핑은 가변적(Mutable)이므로 런타임동안 새로운 이름이 추가될 수 있다.
- 다만, 빌트인 네임스페이스는 함부로 추가하거나 삭제할 수 없다.

![](/assets/img/2019-01-30-python-namespace/01.png)

(출처: [https://blog.confirm.ch/python-namespaces/](https://blog.confirm.ch/python-namespaces/))

위의 그림은 파이썬의 네임스페이스가 어떤 것인지 잘 표현해준다. 파이썬 공식 홈페이지의 튜토리얼에서는 네임스페이스를 `이름들과 실제 객체들 사이의 매핑`이라고 정의한다.

## 파이썬의 변수 스코프
위에서 밝혔듯이 네임스페이스는 프로그래밍의 아무 위치에서나 접근이 가능한 것이 아니다. 변수 스코프는 이러한 네임스페이스의 접근성에 대해 고려하기 위한 개념이다.

변수 스코프(Variable Scope)란 접두어(Prefix)없이 `어떤 네임스페이스에 직접 접근이 가능한` 프로그래밍의 어떤 부분이라고 정의할 수 있다. C++처럼 중괄호(\{...\})를 통해서 블록을 나누는 경우에는 블록을 기준으로 변수 스코프가 결정된다. 따라서 중괄호 내에서 선언된 변수는 중괄호를 빠져나오면 폐기가 된다. 반면, 파이썬에서는 C++과는 다르게 블록 단위의 스코프가 존재하지 않으며 오직 전역 변수 스코프 및 지역 변수 스코프만이 존재하게 된다.

프로그래밍을 하는 도중에는 어떤 경우에도 다음과 같은 중첩된 변수 스코프를 확인할 수 있다:
- 지역 이름들을 포함하는 현재 함수의 스코프(지역 네임스페이스)
- 전역 이름들을 포함하는 현재 모듈의 스코프(전역 네임스페이스)
- 빌트인 이름들을 포함하는 최외곽의 스코프(빌트인 네임스페이스)

만약 새로운 함수가 실행된다면 그 함수의 지역 이름들을 포함하는 새로운 스코프가 생겨서 중첩되게 된다. 또한 현재 변수의 이름이 참조하는 객체가 무엇인지를 검색하는 경우에는 가장 내부의 지역 네임스페이스부터 검색해 나가며 그 다음에는 보다 바깥에 있는 지역 네임스페이스를, 그리고 마지막에는 전역 네임스페이스를 검색한 이후에 최종적으로 빌트인 네임스페이스를 검색하게 된다.

다음의 예제를 살펴보자.

```python
# namespace_example01.py
def outer_func():
    a = 20
    def inner_func():
        a = 30
        print("a = %d" % a)
    
    inner_func()
    print("a = %d" % a)

a = 10
outer_func()
print("a = %d" % a)

>>>
a = 30
a = 20
a = 10
```

위의 코드가 어떻게 동작하는지 확인해보자. 파이썬 인터프리터는 먼저 Level 0 코드부터 차근차근 실행하게 될 것이다. 맨 위에 `outer_func`가 정의되지만 인터프리터는 함수 정의부분은 정의만하고 실행하지는 않는다. 따라서 가장 먼저 실행되는 부분은 함수 정의 밑부분인 `a = 10`이 될것이다.

`a = 10`가 실행되면 이 모듈의 전역 변수로 `a`가 추가되게 된다. 그 다음으로 `outer_func`가 실행되서 함수 내부의 지역 변수로 또 다시 `a = 20`으로 선언이 된다. 그 다음 `inner_func`의 정의 부분은 다시 또 뛰어넘고 `inner_func`가 실행된다. `inner_func` 내부에서는 지역 변수로 `a = 30`이 정의가 되며 여기서 `a`를 출력해보면 외부 지역 변수 및 전역 변수 `a`들을 모두 무시하고 현재 스코프의 지역 변수 `a`가 출력되는 것을 확인할 수 있다.

마찬가지로 `outer_func`가 실행되는 부분에서도 `a`는 현재 함수의 스코프의 지역 변수인 `a`가 출력된다. 그리고 마지막으로 전역 변수 `a`가 출력되게 된다.

여기서 각 스코프의 네임스페이스를 출력해볼 수 있다. 현재 스코프의 네임스페이스를 복사해서 반환하는 메서드는 `locals`이다. 위의 예제를 다음과 같이 수정해보자.

```python
# namespace_example01.py
def outer_func():
    a = 20
    def inner_func():
        a = 30
        print(locals())
    
    inner_func()
    print(locals())

a = 10
outer_func()
print(locals())

>>>
{'a': 30}
{'inner_func': <function outer_func.<locals>.inner_func at 0x7fe5e082e840>, 'a': 20}
{'__builtins__': <module 'builtins' (built-in)>, '__file__': 'namespace_example01.py', '__spec__': None, '__loader__': <_frozen_importlib_external.SourceFileLoader object at 0x7fe5e0834c18>, 'a': 10, 'outer_func': <function outer_func at 0x7fe5e086bf28>, '__doc__': None, '__name__': '__main__', '__cached__': None, '__package__': None}
```

마찬가지로 맨 위에서부터 `inner_func`에서의 지역 네임스페이스, `outer_func`에서의 지역 네임스페이스, 그리고 모듈 전체에서의 지역 네임스페이스 순서로 출력된다. `inner_func`의 지역 네임스페이스는 지역 변수 `a = 30`만을 포함하고 있으며 `outer_func`의 지역 네임스페이스는 `inner_func` 및 `a = 20`을 포함하고 있음을 확인할 수 있다. 여기까지는 너무 당연한 얘기가 될 것이며 그 다음이 중요하다.

모듈 전체에서의 지역 네임스페이스는 사실 모듈의 전역 네임스페이스와 같다는 것을 직관적으로 생각해볼 수 있다. 검증을 위해서 아래의 예제를 실행시켜 보자.

```python
# namespace_example01.py
def outer_func():
    a = 20
    def inner_func():
        a = 30
    
    inner_func()

a = 10
outer_func()
print(locals())
print(globals())

>>>
{'a': 10, 'outer_func': <function outer_func at 0x7f90c3ac5f28>, '__package__': None, '__file__': 'namespace_example01.py', '__spec__': None, '__builtins__': <module 'builtins' (built-in)>, '__name__': '__main__', '__loader__': <_frozen_importlib_external.SourceFileLoader object at 0x7f90c3a8ec18>, '__doc__': None, '__cached__': None}                 
{'a': 10, 'outer_func': <function outer_func at 0x7f90c3ac5f28>, '__package__': None, '__file__': 'namespace_example01.py', '__spec__': None, '__builtins__': <module 'builtins' (built-in)>, '__name__': '__main__', '__loader__': <_frozen_importlib_external.SourceFileLoader object at 0x7f90c3a8ec18>, '__doc__': None, '__cached__': None}
```

확인해보니 똑같다. 모듈의 전역 네임스페이스에는 전역 변수 `a = 10`을 포함한 `outer_func` 및 기타 등등을 포함하고 있다. 여기서 유심히 살펴봐야 하는 점은 `'__name__': '__main__'`이다.

## if \_\_name\_\_ == '\_\_main\_\_':의 의미

다시 맨 처음 질문으로 되돌아가 보자. 그냥 실행시켜도 될 메인 코드를 왜 굳이 `if`문을 포함하여 `if __name__ == '__main__':`을 사용하여 구현했는지에 대한 의문점이 여기서 해결될 수 있다.

그 전에 짚고 넘어가야 되는 점이 하나 있다. 현재까지의 결과는 저 예제 코드를 우분투 커맨드 라인에서 실행한 결과이다. 예제를 작성한 스크립트의 파일명이 `namespace_example01.py`일 때, 다음과 같이 실행한 결과인 것이다.

```bash
>>> python namespace_example01.py
```

이렇게 실행하면 현재 전역 네임스페이스의 `__name__`이 `__main__`으로 설정되는 것을 확인할 수 있을 것이다. 또 다른 예제를 위하여 `namespace_example02.py` 파일을 열어서 다음과 같이 작성해보자.

```python
# namespace_example02.py
import namespace_example01

a = 10

>>>
{..., '__name__': 'namespace_example01', ...}
```

그 외에 잡다한 것이 너무 많이 출력된다. 좀 더 깔끔하게 출력되게 하기 위하여 내장 변수 `__name__`를 사용하여 위의 `namespace_example01.py`를 아래와 같이 수정해보자.

```python
# namespace_example01.py
def outer_func():
    a = 20
    def inner_func():
        a = 30
    
    inner_func()

a = 10
outer_func()
print("Namespace:", __name__)
```

```python
# namespace_example02.py
import namespace_example01

a = 10

>>>
Namespace: namespace_example01
```

참고로 `namespace_example01.py`를 아래처럼 작성하여도 같은 결과를 얻을 수 있다.

```python
# namespace_example01.py
def outer_func():
    a = 20
    def inner_func():
        a = 30
    
    inner_func()

a = 10
outer_func()
print("Namespace:", globals()["__name__"])
```

위의 예제들을 통해서 얻은 결과처럼 네임스페이스가 `'namespace_example01'`으로 출력되는 것으로 확인할 수 있다. 내장 변수 `__name__`은 현재 모듈의 전역 네임스페이스의 이름을 담고있는 내장 변수이다. 이것은 `globals()["__name__"]`과 같은 표현이다.

즉, 맨 처음 질문의 `if __name__ == '__main__':`을 해석하면 다음과 같다. 현재 모듈의 네임스페이스가 `__main__`에 해당한다면, 즉 현재 모듈이 `커맨드 라인 상에서 직접 실행되는 경우`에만 `if`문 이하를 실행하라는 의미이다.

