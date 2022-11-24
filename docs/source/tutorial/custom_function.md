
# 커스텀 함수

당신이 파이썬을 다룰 수 있다면 KoML을 이용해서 굉장히 재미있는 것을 해볼 수 있어요. 예를들어, 지금 시각을 알려주는 예시를 생각해볼게요. 

```
<< 지금 몇시야?
>> 음.. 지금 오후 8시 36분!
```

지금 시각은 정해져 있는 값이 아니기 때문에 \<template> 에 미리 입력해서 답변을 내놓기 어려워요. 이럴 때 \<func> 태그를 활용할 수 있어요.

<br>

## \<func>

\<func> 태그를 이용하기 위해서는 먼저 파이썬 함수를 정의해줘야해요. 지금 시각을 알려주는 함수를 간단히 작성해볼게요.

```python
import time

def show_date(context=None):
    tick = time.localtime()
    h, m = tick.tm_hour, tick.tm_min

    if h > 12:
        h -= 12
        am_pm = '오후'
    else:
        am_pm = '오전'
    return f'{am_pm} {h}시 {m}분'
```
>**NOTE** 모든 KoML함수의 마지막 parameter는 context 가 주어져야 해요. context는 KoML 의 전반적인 동작(앞 뒤 문맥, 단어 기억 등)에 중요한 역할을 한답니다. 자세한 내용은 **컨텍스트** 항목을 참조해주세요. 

<br>

koml 파일은 다음과 같이 작성해주세요.
```xml
<!-- custom_function.xml -->
<?xml version="1.0" encoding="UTF-8"?>
<!DOCTYPE xml>
<koml>
  <case>
    <pattern>지금 몇시야_sf</pattern>
    <template>음.. 지금 <func name='show_date'/>!</template>
  </case>
</koml>
```
<br>

이후 KomlBot 을 동작시킬 때 이 함수를 사용할거라는 것을 알려주어야 해요. `CustomBag`을 사용해서 함수를 전달할 수 있어요.
```python
from koml import KomlBot, CustomBag

bag = CustomBag(funcs={'show_date': show_date}) 

bot = KomlBot(bag)
bot.learn(['custom_function.xml'])
bot.converse()
```
>**NOTE** \<func> 태그에 name 에는 CustomBag 에 넣어준 key 값이 들어가야 해요.

<br>

다음과 같은 결과를 확인할 수 있어요.
<br>

```
<< 지금 몇시야?
>> 음.. 지금 오후 8시 36분!
```

<br>
<br>

## \<arg>

\<func> 도 파이썬 함수에 인자(Arguments)를 전달해줄 수 있어요.  \<arg>로 감싸서 필요한 인자를 \<func>태그 안에 넣어주세요. <br>
\<func>과 \<arg> 를 이용하면  인자가 필요한 함수 기능을 구현해볼 수 있어요. 
<br>
<br>
\<func> 와 \<arg>를 이용한 예시로 연산 기능을 구현해볼게요.

```
<< 3 더하기 5는 뭐야?
>> 8 이잖아ㅋㅋ 맞지?
<< 429 더하기 99는 뭐야?
>> 528 이잖아ㅋㅋ 맞지?
```

<br>

먼저 더하기 함수를 python 으로 정의해주세요. KoML 함수의 인자(Arguments)와 반환형(return type)은 str이라는 점을 유의해주세요!

```python
def plus(a: str, b: str, context=None) -> str:
    try:
        a_num = int(a)
        b_num = int(b)
        return str(a_num + b_num)
    except:
        return '???'
```
<br>
xml 파일에 다음 케이스를 추가해주세요..

```xml
<case>
  <!-- 숫자만 가져오기 -->
  <pattern><blank pos='SN'/> 더하기 <blank pos='SN'/> 뭐_e_sf</pattern>
  <template>
    <func name='plus'>
      <arg><blank idx='1'/></arg><arg><blank idx='2'/></arg>
    </func> 이잖아ㅋㅋ 맞지?
  </template>
</case>
```

<br>

마찬가지로 KomlBot 을 실행할 때 CustomBag 에 정의한 함수를 넣어서 실행해주세요.

```python
from koml import KomlBot, CustomBag

bag = CustomBag(funcs={'plus': plus})

bot = KomlBot(bag)
bot.learn(['custom_function.xml'])
bot.converse()
```
<br>

다음과 같은 결과를 확인할 수 있어요.

```
<< 8 더하기 9 는 뭐니?
>> 17 이잖아ㅋㅋ 맞지?
```
