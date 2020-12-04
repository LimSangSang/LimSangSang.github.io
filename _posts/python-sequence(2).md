---
layout: post
title: Python Sequence(2)
subtitle: packing, unpacking, mutable, immutable, sort, sorted
tags: [python, sequence]
comments: true
---

## Unpacking

이번엔 Unpacking에 대해 얘기해보겠습니다. a, b를 교차해볼까요?

```python
b, a = a, b
```

다른 언어는 임시 변수를 만들어서 a, b를 각각 할당했다가 그 다음 교차해주는게 필요합니다. 하지만 python은 바로 할당 가능이 가능합니다!

```python
print(divmod(100, 9))  # (11, 1)
# print(divmod((100, 9))) TypeError: divmod expected 2 arguments, got 1
```

divmod(a, b) 함수는 a를 b로 나눈 몫과 나머지를 반환해주는 함수입니다. 여기에선 100을 9로 나누었기 때문에 (11, 1)이 출력되는 거죠!  
하지만 divmod((100, 9))는 왜 에러가 나는 걸까요? (100, 9)를 a, b로 인식하는게 아니라 하나의 a로 인식하기 때문입니다. b가 없으니 당연히 오류가 나겠죠? 그리고 인수에 튜플을 넣을 수 없습니다.

```python
print(divmod(*(100, 9)))  # (11, 1)
print(*(divmod(100, 9)))  # 11 1
```

divmod(*(100, 9))은 튜플을 풀어서 넣어주었기 때문에 오류가 나지 않습니다. divmod(100, 9)의 결과값은 (11, 1)이지만 *를 붙여 결과값 tuple을 unpacking했기 때문에 결과값이 11 1이 나옵니다.

```python
packing = 1, 2, 3, 4, 5
1, 2, 3, 4, 5 = unpacking
```

참고로 unpacking할 때 두 시퀀스의 두 시퀀스의 길이가 일치해야 합니다.

```python
# x, y, rest = range(10) # ValueError: too many values to unpack (expected 3)
```

만약 길이가 일치하지 않는다면 위와 같은 오류가 유의해야 합니다. 하지만 만약 range(100)처럼 큰 값이면 일일이 변수 선언하는게 힘들지 않을까요?

파이썬에서 유용하게 쓰이는 \*, \*\*에 대해 설명해보겠습니다. 파이썬을 공부하다가 많이 보지 않았나요?
코드로 바로 설명해보겠습니다.

```python
x, y, *rest = range(10)
print(x, y, rest)  # 0 1 [2, 3, 4, 5, 6, 7, 8, 9]
x, y, *rest = range(2)
print(x, y, rest)  # 0 1 []
x, y, *rest = 1, 2, 3, 4, 5
print(x, y, rest)  # 1 2 [3, 4, 5]
```

변수들을 선언해서 대입하고 남은 변수는 *로 대입해 리스트를 만듭니다. 만약 변수 길이를 다 채우고 *로 묶으면 빈 리스트가 채워지는 것을 알 수 있습니다.

## mutable vs immutable

```python
l = (15, 20, 25)  # tuple 불변
m = [15, 20, 25]  # list 가변

print(l, id(l))  # (15, 20, 25) 140361051709568
print(m, id(m))  # [15, 20, 25] 140361052258432
```

l과 m을 선언해서 id를 확인해보겠습니다.

```python
l = l * 2
m = m * 2

print(l, id(l))  # (15, 20, 25, 15, 20, 25) 140361051798496
print(m, id(m))  # [15, 20, 25, 15, 20, 25] 140361052258368
```

분명 같은 변수인데 초기 선언한 l, m과 id가 다르네요? l과 m 각각 곱하기 2를 하고 새로운 변수를 재할당했기 때문에 id값이 전부 다르기 때문입니다.

```python
l *= 2
m *= 2

print(l, id(l))
# (15, 20, 25, 15, 20, 25, 15, 20, 25, 15, 20, 25) 140361015516176
print(m, id(m))
# [15, 20, 25, 15, 20, 25, 15, 20, 25, 15, 20, 25] 140361052258368
```

l은 바로 위에서 선언했던 id값과 다릅니다. 불변형은 한 번 id값을 할당하면 수정을 할 수 없기 때문에 id가 재할당 이루어집니다.  
m은 가변형이기 때문에 자기 id값에 추가를 해 id가 같습니다.

## sort vs sorted

정렬 기능인 sort과 sorted를 알아보겠습니다.
reverse, key=len, key=str.Lower, key=func... 등 여러가지로 정렬할 수 있습니다.

먼저 sorted부터 알아보겠습니다.
sorted는 정렬 후 새로운 객체를 반환하고 원본은 수정하지 않습니다.

```python
# sorted : 정렬 후 새로운 객체 반환(원본 수정x)
f_list = ['orange', 'apple', 'mango',
          'papaya', 'lemon', 'strawberry', 'coconut']
# sorted -  ['apple', 'coconut', 'lemon', 'mango', 'orange', 'papaya', 'strawberry']
print('sorted - ', sorted(f_list))
# sorted -  ['strawberry', 'papaya', 'orange', 'mango', 'lemon', 'coconut', 'apple']
print('sorted - ', sorted(f_list, reverse=True))
# sorted -  ['apple', 'mango', 'lemon', 'orange', 'papaya', 'coconut', 'strawberry']
print('sorted - ', sorted(f_list, key=len))  # 길이순
# sorted -  ['papaya', 'orange', 'apple', 'lemon', 'mango', 'coconut', 'strawberry']
print('sorted - ', sorted(f_list, key=lambda x: x[-1]))  # 단어 끝 글자부터 정렬
# print('sorted - ', sorted(f_list, key=lambda x: x[-1], reverse=True))
# print(f_list)
```

정렬 값에 따라 달라지는 것이 보이나요? sorted로 정렬값을 다르게 한다고 해서 원본 f_list값이 변하는 것은 아닙니다. 만약 새로운 정렬 값은 사용하고 싶다면 변수를 새로 만들어야 합니다.

이어서 sort에 대해 알아보겠습니다. sort는 sorted와 달리 새로운 객체를 반환하는 것이 아니라 원본을 수정합니다.

```python
f_list = ['orange', 'apple', 'mango',
          'papaya', 'lemon', 'strawberry', 'coconut']
print('sort - ', f_list.sort(), f_list)
# sort -  None ['strawberry', 'papaya', 'orange', 'mango', 'lemon', 'coconut', 'apple']
print('sort - ', f_list.sort(reverse=True), f_list)
# sort -  None ['papaya', 'orange', 'apple', 'lemon', 'mango', 'coconut', 'strawberry']
print('sort - ', f_list.sort(key=lambda x: x[-1]), f_list)
# sort -  None ['strawberry', 'coconut', 'mango', 'lemon', 'orange', 'apple', 'papaya']
print('sort - ', f_list.sort(key=lambda x: x[-1], reverse=True), f_list)
```

sorted와 같이 속성 값에 따라 정렬이 됩니다. 그런데 f_list.sort()로 반환한 값은 왜 None으로 출력되고 f_list에서만 정렬된 값이 보일까요?
sort는 원본은 바로 수정하지만 반환값을 바로 확인할 수 없습니다. 그래서 정렬된 값을 확인하려면 변수를 직접 출력해서 확인해야 합니다.

이번 기회로 sort와 sorted에 대해 알아봤습니다. 오늘도 수고하셨습니다!
