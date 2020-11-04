---
title: "[Python Tricks]chapter2. Patterns for Cleaner Python"
date: 2020-11-03 13:11:00 +0800
categories: [Python]
tags: [python, Python Tricks]
toc: true
---

# 2.4 Underscores, Dunders, and More
파이썬 variable과 method에 사용하는 underscore에 대한 의미를 알아보자.

## Single Leading Underscore: "\_var"
prefix로 underscore를 붙이는 것은 내부적으로만 사용한다는 hint다. 
java처럼 private/public 구분이 없기 때문에, underscore를 prefix로 붙여서 사용한다.
그래서 실제로 underscore가 prefix로 붙은 것은 접근이 가능하다.
```python
class Test:
    def __init__(self):
        self.foo = 11
        self._bar = 23
```
```
>>> t = Test()
>>> t.foo
11
>>> t._bar
23
```
한가지, import하여 사용할때 특이사항이 있다. 아래 처럼 methond가 있다고 가정하자.
```python
# my_module.py

def external_func():
    return 23

def _internal_func():
  return 42
```

my_module을 전체 import하면 underscore가 prefix로 붙은 method는 import 되지 않는다.
```
>>> from my_module import *
>>> external_func()
23
>>> _internal_func()
NameError: "name '_internal_func' is not defined"
```

하지만, 직접 접근은 가능하다.
```
>>> import my_module
>>> my_module.external_func()
23
>>> my_module._internal_func()
42
```


# 2.5 A Shocking Truth About String Formatting(포맷팅)
아래 변수들을 이용하여 다음과 같이 출력하려면 어떻게 해야 될까?
```python
errno = 50159747054
name = 'Bob'
```
```
'Hey Bob, there is a 0xbadc0ffee error!'
```

## Old Style String Formatting
% Operation을 이용하여 하는 방법
```python
'Hey %s, there is a 0x%x error!' % (name, errno)
'Hey %(name)s, there is a 0x%(errno)x error!' % {"name": name, "errno": errno }
```

## New Style String Formatting
python3에서 사용가능한 format()을 이용하는 방법
```python
'Hello, {}'.format(name)
'Hey {name}, there is a 0x{errno:x} error!'.format(name=name, errno=errno)
```

## Literal String Interpolation
python3.6에 추가된 Formatted String Literals 방법
```python
f'Hello, {name}!'
f"Hey {name}, there's a {errno:#x} error!"
```

## Template Strings
Template를 이용하는 방법
```python
from string import Template
t = Template('Hey, $name!')
t.substitute(name=name)
```
```python
templ_string = 'Hey $name, there is a $error error!'
Template(templ_string).substitute(name=name, error=hex(errno))
```



