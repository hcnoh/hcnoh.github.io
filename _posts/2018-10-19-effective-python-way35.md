---
layout: post
title: "WAY 35. 메타클래스로 클래스 속성에 주석을 달자"
date: 2018-10-19 18:57:12
tagline: "이펙티브 파이썬 코딩의 기술 책 스터디 정리"
categories:
- 이펙티브 파이썬 스터디
tags:
- python
image: /thumbnail-mobile.png
author: "Hyungcheol Noh"
permalink: /2018-10-19-effective-python-way35
---

## 데이터베이스 예제
- 고객 데이터베이스의 로우를 표현하는 새 클래스를 정의
  - 데이터베이스 테이블의 각 칼럼에 대응하는 클래스의 프로퍼티가 있어야 함
  - 프로퍼티를 칼럼 이름과 연결하는 데 사용할 디스크립터 클래스 정의

```python
class Field(object):
    def __init__(self, name):
        self.name = name
        self.internal_name = "_" + self.name
    
    def __get__(self, instance, instance_type):
        if instance is None:
            return self
        return getattr(instance, self.internal_name, "")    # instance의 self.internal_name라는 이름의 속성을 반환, 
                                                            # 만약 그러한 속성이 없으면 ""을 반환
                                                            # 즉, getattr(instance, self.internal_name) => instance.self.internal_name
        
    def __set__(self, instance, value):
        setattr(instance, self.internal_name, value)        # instance의 self.internal_name라는 이름의 속성에 value로 할당
                                                            # 즉, setattr(instance, self.internal_name, value)
                                                            #                   => instance.self.internal_name = value
```

- `Field` 디스크립터에 저장할 칼럼 이름이 있으면 =>
  - 내장 함수 `setattr`과 `getattr`을 사용하여 모든 인스턴스별 상태를 인스턴스 딕셔너리에 보호 필드로 직접 저장
  
```python
class Customer(object):
    # 클래스 속성 => 클래스에 종속되어 인스턴스들이 모두 공유하는 속성들
    first_name = Field("first_name")
    last_name = Field("last_name")
    prefix = Field("prefix")
    suffix = Field("suffix")

# Field 디스크립터가 인스턴스 딕셔너리 __dict__를 기대한 대로 수정하는 방식 확인
foo = Customer()
print("Before:", repr(foo.first_name), foo.__dict__)
foo.first_name = "Euclid"
print("After:", repr(foo.first_name), foo.__dict__)

>>>
Before: "" {}
After: "Euclid" {"_first_name": "Euclid"}
```

>- 책에서는 중복되는 것이 마음에 들지 않는다고 주장:
>   - `first_name = Field("first_name")`에서 `first_name`을 두 번 쓰는 상황을 말하는 듯

>- 즉, 필드 이름(여기서는 `first_name`)을 `Field` 생성자에도 넘기고 싶지 않음

## 메타클래스를 이용하여 중복성을 제거
- 메타클래스를 이용 => 클래스 본문이 끝나자마자 원하는 동작을 처리
  - 필드 이름을 수동으로 여러 번 지정하지 않고,
  - 메타클래스를 사용하여 `Field.name`/`Field.internal_name`을 디스크립터에 자동으로 할당

```python
class Meta(type):
    def __new__(meta, name, bases, class_dict):
        for key, value in class_dict.items():
            if isinstance(value, Field):      # value 인스턴스가 Field 클래스인 경우
                value.name = key
                value.internal_name = "_" + key
        cls = type.__new__(meta, name, bases, class_dict)
        return cls
```

- 메타클래스를 사용하는 기반 클래스를 정의
  - 데이터베이스 로우를 표현하는 클래스가 모두 이 클래스를 상속하게 해서 모두 메타클래스를 사용하게 해야 함

```python
class DatabaseRow(object, metaclass=Meta):
    pass
```

- 메타클래스를 사용하게 해도 필드 디스크립터는 변경이 거의 없음
- 유일한 차이:
  - 더는 생성자에 인수를 넘길 필요가 없음
  - 대신 필드 스크립터의 속성은 `Meta.__new__` 메서드로 설정됨

```python
class Field(object):
    def __init__(self):
        # 메타클래스가 이 속성들을 할당함
        self.name = None
        self.internal_name = None
    # ...
```

- 이제 중복이 발생하지 않음

```python
class Bettercustomer(DatabaseRow):
    first_name = Field()
    last_name = Field()
    prefix = Field()
    suffix = Field()

foo = Bettercustomer()
print("Before:", repr(foo.first_name), foo.__dict__)
foo.first_name = "Euler"
print("After:", repr(foo.first_name), foo.__dict__)

>>>
Before: "" {}
After: "Euler" {"_first_name": "Euler"}
```
