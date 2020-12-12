---
layout: post
title: Python Sequence(5)
subtitle: MappingProxyType, frozenset, dis, dictionary, set
tags: [python, sequence, MappingProxyType, frozenset, dis, dictionary, set]
comments: true
---

어느 백화점 백엔드는 파이썬으로 구축했는데, vvip를 dictionary로 관리했다고 합니다. 하지만 dictionary는 mutable이기 때문에 언제든지 수정이 가능합니다. vvip 목록은 절대 틀리면 안되는데 실수로 dictionary를 수정하면 어떻게 될까요?
이렇게 dictionary를 immutable로 관리하고 싶을 때 파이썬에서 만들어진 immutable dictionary로 변경할 수 있습니다.

```python
from types import MappingProxyType
d = {'key': 'value1'}
# read only
d_frozen = MappingProxyType(d)
print(d, id(d))  # {'key': 'value1'} 4463582080
# print(hash(d)) # TypeError: unhashable type: 'dict'
```

MappingProxyType은 추가, 수정이 안되는 dictionary를 만들 수 있습니다. dictionary는 mutable이기 때문에 값이 언제든 바뀔 수 있어 hash값을 생성하려고 하면 오류가 발생합니다.
(참고로 hashtable, mutable에 대해 알고 싶으면 제가 정리한 python-sequence(1), python-sequence(3)을 참고해주세요!)

```python
# 수정 불가
# d_frozen['key2'] = 'value2' / TypeError: 'mappingproxy' object does not support item assignment

# 수정 가능
d['key2'] = 'value2'
print(d) # {'key': 'value1', 'key2' : 'value2'}
```

그래서 readonly인 d_frozen을 수정하려고 하면 오류가 발생하지만, dictionary인 d는 직접 수정해도 오류가 발생하지 않습니다.

```python
s1 = {'Apple', 'Orange', 'Apple', 'Orange', 'Kiwi'}
s2 = set(['Apple', 'Orange', 'Apple', 'Orange', 'Kiwi'])
s3 = {3}
s4 = set()  # Not {} : 이건 dict
s5 = frozenset({'Apple', 'Orange', 'Apple', 'Orange', 'Kiwi'})
```

이번에는 여러 방식으로 set을 만들어보았습니다. s2처럼 리스트를 set으로 감싸면 리스트가 set으로 변경돼 s1과 같아집니다.
참고로 s4처럼 set()안이 비어있다면 {} 비어있는 set이 되지만, s4={}로 선언하면 python은 s4를 set이 아닌 dictionary로 인식합니다. 이 점을 주의해주세요! 그리고 s5는 내장 클래스를 통해 frozenset을 쉽게 만들 수 있습니다.

여기서 궁금한 점이 생기셨나요? dictionary를 readonly로 만들고 싶으면 MappingProxyType 라이브러리를 썼는데 왜 frozenset은 내장 클래스가 있을까요?
저도 공부하다가 궁금해서 찾아봤더니 이미 다른 분들도 이런 고민을 하셨더라구요! 시간날 때 보면 은근 재밌습니다.
<https://stackoverflow.com/questions/2703599/what-would-a-frozen-dict-be>
아래는 파이썬에서 frozendict 함수를 거부한 이유도 나와있습니다.
<https://www.python.org/dev/peps/pep-0416/>

```python
s1.add('Melon')
print(s1)  # {'Apple', 'Orange', 'Kiwi', 'Melon'}

# 추가 불가
# s5.add('Melon') # AttributeError: 'frozenset' object has no attribute 'add'
```

set은 같은 값이 중복만 아니면 추가가 가능하고, s5는 readonly이기 때문에 추가를 하려고 하면 오류가 발생합니다.

파이썬은 하이브리드 인터프리터입니다. 프로그램을 실행할 때 먼저 프로그램을 바이트 코드로 어셈블링 한 다음 파이썬 인터프리터(Python 가상시스템)에서 실행할 수 있습니다. 표준 라이브러리의 dis 모듈은 클래스, 메소드, 함수 및 코드 객체를 디스 어셈블하여 사람이 읽을 수있는 Python 바이트 코드를 만들 수 있습니다.
그래서 우리는 dis를 통해 바이트 코드가 어떻게 실행되는지 순서를 확인해보겠습니다.

```python
print('-------')
print(dis('{10}')) # set 코드를 직접 실행
#   1           0 LOAD_CONST               0 (10)
#              2 BUILD_SET                1
#              4 RETURN_VALUE
print('-------')
print(dis('set([10])')) # list를 set으로 변경
#  1           0 LOAD_NAME                0 (set)
#              2 LOAD_CONST               0 (10)
#              4 BUILD_LIST               1
#              6 CALL_FUNCTION            1
#              8 RETURN_VALUE
```

리스트를 set으로 변경한 바이트 코드 순서가 더 긴 것을 눈으로 직접 확인했습니다! 그러니 set을 사용할 때 list를 set으로 변경하는 것보다 직접 선언하는 것을 더 추천드립니다!
