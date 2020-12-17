---
title: "[Python Clean Code] Chapter2. 파이썬스러운(pythonic) 코드"
date: 2020-05-01 09:38:00 +0800
categories: [Python, Python Clean Code]
tags: [python, python clean code]
toc: true
---

> Python Clean Code 책 내용 정리 노트 입니다.

# Chapter2 파이썬스러운(pythonic) 코드
파이썬스러운(pythonic) 관용구(idiom)를 사용했을때의 장점
* 더 좋은 성능을 낸다.
* 코드가 작고 이해하기 쉽다.
* 동일한 패턴과 구조에 익숙해지면 실수가 줄고, 본질적인것에 집중할 수 있다.
 
## 인덱스와 슬라이스
### 마지막 요소 인덱스
C에서 배열의 마지막 요소에 접근하려면 배열의 길이에서 1을 뺀 위치에서 요소를 가져온다.  
하지만 파이썬에서는 음수 인덱스를 사용하여 끝에서부터 접근이 가능하다.
```python
my_numbers = (4, 5, 3, 9)
my_numbers[-1] 
# 9
my_numbers[-3] 
# 5
```

### 특정 구간 슬라이스
대괄호의 시작 인덱스부터 종료 인덱스까지의 값을 가져온다.
```python
my_numbers = (1, 1, 2, 3, 5, 8, 13, 21)
my_numbers[2:5] 
#(2, 3, 5)
```
시작, 끝 인덱스를 제외할 수 있다.
```python
my_numbers[:3]
# (1, 1, 2)
my_numbers[3:]
# (3, 5, 8, 13, 21)
my_numbers[::]
# (1, 1, 2, 3, 5, 8, 13, 21)
my_numbers[1:7:2] # 1부터 7까지의 요소 중 2칸씩 점프
# (1, 3, 8)
```
시퀀스에 간격을 전달하는 것은 slice 객체를 전달하는 것과 같다.
```python
interval = slice(1, 7, 2)
my_numbers[interval]
# (1, 3, 8)

interval = slice(None, 3)
my_numbers[interval] == my_numbers[:3]
# True
```
### 자체 시쿼스 생성
인덱스와 슬라이스 기능은 \_\_getitem\_\_ 메서드를 통해 동작한다.  
사용자정의 클래스에서 __getitem__을 구현하는 경우 몇 가지 고려사항 해야될 사항이 있다.  
클래스가 표준 라이브러리 객체를 감싸는 경우 기본 객체에 위임할 수 있다.
아래의 경우 list에 존재하는 동일한 메소드를 호출한다.
```python
class Item:
    def __init__(self, *values):
        self._values = list(value)
    def __len__(self):
        return len(self._values)
    def __getitem__(self, item):
        return self._values.__getitem__(item)
```
표준 라이브러리를 사용하지 않는 경우 아래의 사항을 지켜야 한다.
1. 범위로 인덱싱하는 결과는 해당 클래스와 같은 타입의 인스턴스여야 한다.
2. slice에 의해 제공된 범위는 파이썬이 하는 것처럼 마지막 요소는 제외해야 한다.

## 컨텍스트 관리자(context manager)
특정 동작 전후 작업에 유용한 기능이다.  
일반적으로 리소스 관리를 할 때 컨텍스트 관리자를 자주 사용한다. 예를 들어, 파일을 열고 작업을 한 후 누수를 막기위해 파일을 닫아야 한다.  
일반적인 방법은 다음과 같다.
```python
fd = open(filename)
try:
    process_file(fd)
finally:
    fd.close()
```
파이썬스러운 방법은 다음과 같다.
```python
with open(filename) as fd:
    process_file(fd)
```
with문(PEP-343)은 컨텍스트 관리자로 진입하게 되고 예외가 발생해도 자동으로 닫힌다.

컨텍스트 관리자는 __enter__와 \_\_exit\_\_ 두 개의 메소드로 구성된다. with 문의 \_\_enter\_\_ 메소드를 호출하고 as 이후 변수에 할당된다. (꼭 변수에 할당할 필요는 없다.)  
with 블록의 마지막 문장이 끝나면 컨텍스트가 종료되고 __exit__가 호출된다.  

컨텍스트 관리자가 리소스관리에 자주 사용되지만, 전후 처리가 필요한 기능에 자체 컨텍스트 관리자를 구현할 수 있다.  
예를 들면, DB 백업 작업 시, 백업 전에 DB가 중지되어야 하고 백업 후에 DB가 실행되어야 하는 경우이다.
```python
def stop_database():
    run("systemctl stop postgresql.service")

def start_database():
    run("systemctl start postgresql.service")

class DBHandler:
    def __enter__(self):
        stop_database()
        return self

    def __exit__(self, exc_type, ex_value, ex_traceback):
        start_database()

def db_backup():
    run("pg_dump database")

def main():
    with DBHandler():
        db_backup()
```

### 컨텍스트 관리자 구현
표준 라이브러리 contextlib를 사용하여 컨텍스트 관리자를 쉽게 구현할 수 있다.  
contextlib.contextmanager 데코레이터를 사용하여 다음과 같이 작성할 수 있다.
```python
import contextlib

@contextlib.contextmanager
def db_handler():
    stop_database()
    yield
    start_database()

with db_handler():
    db_backup()
```
yield를 사용하면 제너레이터 함수가 되며, yield 문의 앞 부분은 __enter__이 되고, 반환값을 지정할 수 있다.  
yield 뒷 부분은 __exit__가 된다.  
이렇게 작성하면 업무 도메인이 분리가 되어 리팩토링하기 편리한 장점이 있다.

컨텍스트 관리자를 구현하는 또 다른 방법은 contextlib.ContextDecorator이다.
```python
class dbhandler_decorator(contextlib.ContextDecorator):
    def __enter__(self):
        stop_database()
    
    def __exit__(self, exc_type, ex_value, ex_traceback):
        start_database()

@dbhandler_decorator()
def offline_backup():
    run("pg_dump database")
```
이 방법에는 with 문이 없다. 함수를 호출하면 자동으로 컨텍스트 관리자 안에서 실행된다.  
이 방법의 장점이자 단점은 완전히 독립적이라는 것이다. 데코레이터 함수에 대해 모르고 반대도 마찬가지이다. 대신 컨텍스트 관리자 내에서 사용할 수 있는 객체를 얻을 수 없다.  

마지막으로 contextlib.suppress에 대해 알아보자.  
contextlib.suppress는 try/catch와 동일한 효과를 준다.
```python
import contextlib

with contextlib.suppress(DataConversionException):
    parse_data(input_json_or_dict)
```

## 프로퍼티, 속성과 객체 메서드의 다른 타입들
파이썬 객체의 모든 프로퍼티와 메소드는 public이다. 강제사항은 없지만 몇 가지 규칙이 존재한다.   
예를 들어, 밑줄로 시작하는 속성은 private으로 취급한다. 밑줄에 대해 자세히 알아보자.

### 파이썬에서의 밑줄
```python
class Connector:
    def __init__(self, source):
        self.source = source
        self._timeout = 60

conn = Connector("postgresql://localhost")
print(conn.source)
# postgresql://localhost
print(conn._timeout)
# 60
conn.__dict__
# {'source': 'postgresql://localhost', '_timeout': 60}
```
source는 public, _timeout은 private이지만 접근이 가능하다.  
_timeout은 connector 내부에서만 사용하고 외부에서 호출되어서는 안된다.  
이러한 규칙을 지킨다면 유지보수가 쉬워지고 견고한 코드를 작성할 수 있다.

이중 밑줄을 사용하여 private을 만들 수 있다는 오해가 있다.
```python
class Connector:
    def __init__(self, source):
        self.source = source
        self.__timeout = 60

conn = Connector("postgresql://localhost")
print(conn.source)
# postgresql://localhost
print(conn.__timeout)

Trackback (most recent call last):
    File "<stdin>", line 1, in <module>
    AttributeError: 'Connector' object has no attribute '__timeout'
```
일부 개발자들은 이런 방식으로 속성을 숨길 수 있다고 생각하지만, AttributeError는 private이라서 발생하는 에러가 아니라 속성이 존재하지 않을 때 발생하는 에러이다.  
밑줄 두 개를 사용하면 파이썬은 다른 이름을 만든다. 이를 name mangling이라고 한다.  
'_<class-name>__<attribute-name>" 속성이 만들어 진다.
```python
print(conn._Connector__timeout)
# 60
```

### 프로퍼티
객체의 어떤 속성에 접근할 때 다른 언어는 getter/setter를 사용하지만 파이썬은 프로퍼티를 사용한다.

```python
import re

EMAIL_FORMAT = re.compiler(r"[^@]+@[^@]+[^@]+")

def is_valid_email(potentially_valid_email: str):
    return re.match(EMAIL_FORMAT, potentially_valid_email) is not None

class User:
    def __init__(self, username):
        self.username = username
        self._email = None

@property
def email(self):
    return self._email

@email.setter
def email(self, new_email):
    if not is_valid_email(new_email):
        raise ValueError(f"유효한 이메일이 아니므로 {new_email} 값을 사용할 수 없음")
    self._email = new_email
```

@property 메소드는 private인 email 값을 반환한다.  
@email.setter는 user.email = <new_email>이 실행될 때 호출되는 메소드이다.
```python
u1 = User("jsmith")
u1.email = "jsmith@"
Trackback (most recent call last):
...
유효한 이메일이 아니므로 jsmith@ 값을 사용할 수 없음

u1.email = "jsmith@g.co"
u1.email
# jsmith@g.co
```
일반적인 경우는 속성을 직접 사용하고, 속성 값을 가져오거나 수정할때 로직이 필요한 경우에 프로퍼티를 사용한다.

프로퍼티는 명령-쿼리 분리 법칙(command and query separation - CC08)을 따르는 좋은 방법이다.   
명령과 쿼리 중 하나만 수행해야지 둘 다 동시에 수행하면 안된다는 것이다.  
예를 들어, set_email("a@j.com")의 의미가 이메일을 설정하는 것인지, 확인하는 것인지, 둘 다 하는 것인지 혼동될 수 있다.  
프로퍼티를 사용하면 @property는 응답을 위한 쿼리이고, @<property_name>.setter는 무언가를 하기 위한 command이다.
추가적으로 메소드는 한 가지만 수행 해야하고, 작업을 처리한 다음 상태를 확인하려면 메소드를 분해야하 한다.

## 이터러블 객체
파이썬에서는 자체 이터러블을 만들 수 있다. 반복은 이터러블 프로토콜을 사용해 동작하며 다음 두 가지를 충족해야 한다.
* 객체가 __next__나 \_\_iter\_\_ 이터레이터 메소드 중 하나를 포함하는지 여부
* 객체가 시퀀스이고 __len__과 __getitem__을 모두 가졌는지 여부

### 이터러블 객체 만들기
객체를 반복할때 iter()를 호출하여 \_\_iter__ 메소드가 있는지 확인한다.
다음은 일정 기간의 날짜를 하루 간격으로 반복하는 객체의 코드이다.

```python
from datetime import timedelta

class DateRangeIterable:
    """자체 이터레이터 메소드를 가지고 있는 이터러블
    
    """
