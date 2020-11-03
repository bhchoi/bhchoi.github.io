---
title: "[Python Tricks]chapter2. Patterns for Cleaner Python"
date: 2020-11-03 13:11:00 +0800
categories: [Python]
tags: [python, Python Tricks]
toc: true
---

# 2.5 A Shocking Truth About String Formatting
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



