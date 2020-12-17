---
title: "[Python Clean Code] Chapter1. 코드 포매팅과 도구"
date: 2020-05-01 09:38:00 +0800
categories: [Python, Python Clean Code]
tags: [python, python clean code]
toc: true
---

> Python Clean Code 책 내용 정리 노트 입니다.

# Chapter1 코드 포매팅과 도구

## 클린 코드의 의미
- - -
프로그래밍 언어는 생각을 컴퓨터에게 전달하는 것이 아니라 다른 개발자에게 전달하는 것이다.  
그러므로 다른 사람이 코드를 읽고 쉽게 유지보수 할 수 있어야 한다.

## 클린 코드의 중요성
- - -
민첩한 개발 및 지속적인 배포 가능하다.  
만약 코드가 클린하지 않다면 매번 리팩토링을 해야 하고, 기술 부채를 해결하기 위해 멈춰야 한다.  
코드를 지금 변경하는 것 보다 미래에 변경하는 것이 어렵기 때문에 부채라는 단어를 사용한다.  

### 클린 코드에서 코드 포매팅의 역할
클린 코드는 단지 PEP-8에 따라 포매팅하고 검사 도구 등을 사용하는 것이 아닌 그 이상을 말한다.  
클린 코드는 품질 좋은 소프트웨어를 개발하고, 견고하고 유지보수가 쉬운 시스템을 만들고, 기술 부채를 회피하는 것을 말한다.  

### 프로젝트 코딩 스타일 가이드 준수
좋은 코드 레이아웃의 가장 중요한 특징은 일관성이다. 코드가 일관되게 구조화되어 있으면 가독성이 높아지고, 오류가 발생하여도 쉽게 파악할 수 있다.  
PEP-8은 파이썬의 많은 특수성을 고려했기 때문에, PEP-8을 확장해서 사용하는 것이 좋다.  

PEP-8의 특징
* 검색 효율성 : 특정 문자열을 찾는 기능
  * parameter에 값을 할당하는 곳
    * location=current_location
  * location 변수에 값을 할당하는 곳
    * current_location = get_location()
* 일관성
  * 여러 사람이 같은 레이아웃, 문서화, 네이밍 룰 등을 지키면 새로운 사람도 쉽게 적응할 수 있다.
* 코드 품질
  * 코드를 쉽게 읽을 수 있고, 버그를 쉽게 찾을 수 있다.

## Docstring과 어노테이션
- - -
훌륭한 코드는 문서화 또한 잘 되어 있다.  
문서화하는 것은 주석을 추가하는 것과는 다르다. 주석은 가급적 피하고, 문서화를 통해 데이터 타입과 예제를 제공할 수 있어야 한다.  
파이썬은 동적으로 타입을 결정하기 때문에, 어노테이션을 통해 정보를 명시해야 다른 개발자가 쉽게 이해할 수 있다.  
또한 어노테이션을 사용하면 Mypy같은 도구를 사용해 자동화된 검증을 할 수 있다.

### Docstring
Docstring은 코드에 포함된 문서(Document)이다. 이유가 아닌 설명이며 주석(Comment)와는 다르다.  
주석을 다는 것은 나쁜 습관이다.  
첫째, 주석은 코드로 아이디어를 제대로 표현하지 못했음을 나타낸다.  
둘째, 주석은 오해의 소지가 있다. 최악의 경우는 주석과 코드가 다를 때 이다.  
외부 라이브러리에 오류가 있는 경우는 주석이 허용된다.
Docstring은 주석이 아니라 특정 컴포넌트(모듈, 클래스, 메소드)에 대한 문서화이다.  
Docstring을 작성하는 것이 좋은 이유는 파이썬이 동적 타이핑을 하기 때문에 어떤 타입을 사용하는지 알 수 없기 때문이다.  

Docstring의 예제  
>  |  update(...)
>  |      D.update(E, **F) -> None.  Update D from dict/iterable E and F.
>  |      If E has a .keys() method, does:     for k in E: D[k] = E[k]
>  |      If E lacks .keys() method, does:     for (k, v) in E: D[k] = v
>  |      In either case, this is followed by: for k in F: D[k] = F[k]

dict.update는 2가지로 사용할 수 있다.
1. key() 메소드를 가진 파라미터 전달  
```python
d = {}
d.update({1:"one", 2:"two"})
```
2. 키와 쌍을 가진 이터러블 전달
```python
d.update([(3, "three"), (4, "four")])
```

이러한 정보를 통해 메소드가 어떻게 동작하는지 알 수 있다.  
객체에 docstring이 정의되어 있으면 __doc__ 속성을 통해 접근이 가능하다.
```python
def my_function():
    """임의의 계산 수행"""
```
```
my_function.__doc__
> 임의의 계산 수행
```

Sphinx를 실행하면 프로젝트 문서화를 위한 기본 골격을 만들어준다.

docstring의 단점은 지속적으로 수작업을 해야하며, 코드가 변경되면 업데이트를 해야된다는 것이다.  


### 어노테이션
PEP-3107에서 어노테이션을 소개하고 있다. 어노테이션은 함수 인자로 어떤 값이 와야 하는지 알려주는 방법이다.  
[https://www.python.org/dev/peps/pep-3107/](https://www.python.org/dev/peps/pep-3107/){:target="_blank"}

```python
class Point:
    def __init__(self, lat, long):
        self.lat = lat
        self.long = long

    def locate(latitude:float, longitude:float) -> Point:
```
latitude와 longitute에 float를 지정함으로써 타입을 예상할 수 있고, 리턴 값도 Point instance라는 것을 알 수 있다. 

```python
class Point:
    lat: float
    long: float
```
메소드의 파라미터, 리턴 뿐만 아니라 변수에 직접 타입을 지정할 수 있다.(PEP-526)  
어노테이션은 타입, 리턴 뿐만 아니라 다양하게 사용할 수 있다.  
어노테이션을 사용하면 __annotations__ 속성이 생기고, 아래와 같이 출력된다.
```
location.__annotations__
{'latitude':float, 'longitude':float, 'return':__main__.Point}
```

PEP-484를 적용하면 어노테이션을 통해 코드를 확인할 수 있다.  
타입 힌팅(Mypy 등)을 사용하면 올바른 타입이 사용되었는지 자동으로 체크할 수 있다.
[https://www.python.org/dev/peps/pep-0484/](https://www.python.org/dev/peps/pep-0484/){:target="_blank"}

### 어노테이션은 docstring을 대체하는 것일까?
docstring을 간편하게 어노테이션으로 표현을 할 수 있지만 대체할 수 있는 것은 아니다.  

다음과 같은 예제가 있다.
```python
def data_from_response(response: dict) -> dict:
    if response['status'] != 200:
        raise ValueError
    return {"data": response["payload"]}
```
파라미터와 리턴 타입을 알 수 있지만 메소드의 상세내용은 알 수가 없다.
docstring을 아래와 같이 추가해 보자.
```python
def data_from_response(response: dict) -> dict:
    """response에 문제가 없다면 response의 payload를 반환
    - response dict의 예제::
    {
        "status": 200, # <int>
        "timestamp": "...", # 현재 시간의 ISO 포맷 문자열
        "payload": "..." # 반환하려는 사전 데이터
    }
    
    - 반환 사전 값의 예제::
    {"data": {...}}

    - 발생 가능한 예외:
    - HTTP status가 200이 아닌 경우 ValueError 발생
    """

    if response['status'] != 200:
        raise ValueError
    return {"data": response["payload"]}

```

### 기본 품질 향상을 위한 도구 설정
모든 검사는 자동화되어야 하고, 빌드의 한 부분이 되어야 한다. 검사에 실패하면 빌드도 실패하도록 설정 해야 한다.

#### Mypy를 사용한 타입 힌팅
mypy([http://mypy-lang.org/](http://mypy-lang.org/){:target="_blank"})는 정적 타입 검사 도구이다. 모든 파일의 타입 불일치를 검사할 수 있다.  

설치
```bash
pip install mypy 
```

실행
```bash
mypy {파일명}
```
제외
```python
type_to_ignore = "something" # type: ignore
```

#### Pylint를 사용한 코드 검사
Pylint([http://www.pylint.org/](http://mypy-lang.org/)는 PEP-8을 준수했는지 체크하는 도구이다.  
.pylintrc 파일을 통해 설정 값을 바꿀 수 있다.  

설치
```bash
pip install pylint 
```
실행
```bash
pylint
```

#### 자동 검사 설정
makefile을 통해 검사 도구를 자동화할 수 있다.
```cmake
typehint:
mypy src/ tests/

test:
pytest tests/

lint:
pylint src/ tests/

checklist: lint typehint test

.PHONY: typehint test lint checklist
```

```bash
make checklist
```

위 makefile은 아래의 순서로 동작한다.
1. 코딩 가이드라인 검사(ex, PEP-8)
2. 올바른 타입을 사용했는지 검사
3. 최종적으로 테스트 실행

빌드 중 검사하는 것 외에 컨벤션이나 코드 구조화를 자동화할 수도 있다. Black([https://github.com/psf/black](https://github.com/psf/black){:target="_blank"})을 사용하면 자동으로 코드를 포매팅한다.
아래 코드는 PEP-8에 의하면 올바은 코드이지만 Black의 컨벤션을 따르지 않았다.
```python
def my_function(name):
    """
    >>> my_function('black')
    'received_Black'
    """
    return 'received {0}'.format(name.title())
```
Black을 적용하면 아래와 같이 따옴표가 쌍따옴표로 바뀐 것을 확인할 수 있다.
```python
def my_function(name):
    """
    >>> my_function('black')
    'received_Black'
    """
    return "received {0}".format(name.title())
```