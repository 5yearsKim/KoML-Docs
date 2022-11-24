# 컨텍스트 

context 는 KomlBot 이 대화에 필요한 문맥과 기억을 모두 저장하는 객체에요. KomlBot은 사용자와 context 를 주고받으며 대화를 기록해요.

## 구성 요소
```python
class Context:
    def __init__(self) -> None:
        self.history: list[Chat] = []
        self.memo: dict[str, str] = {}
```

### memo

\<get>/\<set>을 할 때 사용되는 dict 에요. 해당되는 키-값을 조회해서 \<get>/\<set>을 실행해요.


다음은 커스텀 함수에서 memo 를 조회하는 예시에요.
```python
def know(name: str, context: Context|None=None) -> str:
    if name in context.memo:
        return 'true'
    else:
        return 'false'
```

<br>

### history

대화 기록을 저장해놓는 list 에요. list 의 아이템인 Chat 의 구성 요소는 다음과 같아요.
```python
class Chat:
    def __init__(self, question: str, answer: str, cid: str|None=None) -> None:
        self.question: str = question
        self.answer:str = answer
        self.cid: str|None = cid
```
<br>

## 커스텀 함수에서 활용
**커스텀 함수**에서 context를 함수의 마지막 인자로 넘겨주었던거 기억하나요? 함수 인자로 받은 context를 활용하면 대화 문맥을 고려한 로직 처리를 할 수 있어요. 예를 들면 이런 것이 가능해요.

```
<< 내 이름 뭔지 알아?
>> 아니.. 뭔데?
<< 어니언이야
>> 아 오키ㅋㅋ 이제 기억할게! 
<< 내 이름 뭔지 알아?
>> 응ㅋㅋ 알지
<< 뭔데?
>> 어니언이잖아ㅋㅋ 날 뭘로 보고
```
<br>

context 의 memo 를 조회해 KomlBot 이 <set>을 통해 특정 키워드를 알고 있는지 여부를 판단하는 함수를 다음과 같이 작성할 수 있어요.

```python
def know(key: str, context= None) -> str:
    if key in context.memo:
        return 'true'
    else:
        return 'false'
```
<br>

이후 xml 파일에 다음 케이스들을 추가해주세요.
```xml
<case id = 'know_username'>
  <pattern>* 내 이름_j 뭔지 알아_s</pattern>
  <template>
    <switch>
      <!-- 'username' 을 아는지 여부를 pivot 으로 제공 -->
      <pivot><func name='know'><arg>username</arg></func></pivot>
      <scase pivot='true'>응ㅋㅋ 알지</scase>
      <default>아니.. 뭔데?</default>
    </switch>
  </template>
</case>

<!-- 이름을 아는 경우 -->
<case>
  <follow cid='know_username'>응 *</follow>
  <pattern>
    <li>* 뭔데_s</li>
    <li>뭐야_s</li>
  </pattern>
  <template><get key='username'/>_ix잖아ㅋㅋ 날 뭘로 보고</template>
</case>

<!-- 이름을 모르는 경우 -->
<case>
  <follow cid='know_username'>아니 *</follow>
  <pattern>
    <li>내 이름은 <blank pos='N'/></li>
    <li><blank pos='N'/></li>
  </pattern>
  <template>아 오키ㅋㅋ 이제 기억할게! <think><set key='username'><blank/></set></think></template>
</case>
```
<br>

정의한 `know` 함수를 CustomBag 에 넣어서 실행해주세요.

```python
from koml import KomlBot, CustomBag

bag = CustomBag(funcs={'know': know})

bot = KomlBot(bag)
bot.learn(['custom_function.xml'])
bot.converse()
```

<br>

다음과 같은 결과를 확인할 수 있어요.

```
<< 너 내 이름이 뭔지 알아?
>> 아니.. 뭔데?
<< 홍길동
>> 아 오키ㅋㅋ 이제 기억할게! 
<< 너 내 이름 뭔지 알아?
>> 응ㅋㅋ 알지
<< 뭐야
>> 홍길동이잖아ㅋㅋ 날 뭘로 보고
```

<br>
<br>


## Json 직렬화

context 는 json 직렬화를 지원해요. json 데이터로 쉽게 주고받을 수 있어요.


```json
// context.json
{
  "history": [{"question": "뭐야", "answer": "뭐긴 뭐야"}],
  "memo": {
    "user_name": "어니언",
    "user_age": 26 
  }
}
```

```python
import json
from koml import Context

with open('context.json', 'r') as fr:
    ctx_json= json.load(fr)
    context = Context.from_json(ctx_json)

print(context)
# ==================================
# Context:
#     - history
#       Chat(뭐야 -> 뭐긴 뭐야)
#     - memo
#       {'user_name': '어니언', 'user_age': 26}
# ===================================

json_ctx = context.to_json()
print(json_ctx)
# {'history': [{'question': '뭐야', 'answer': '뭐긴 뭐야'}], 'memo': {'user_name': '어니언', 'user_age': 26}}
```

<br>
<br>

## context 를 이용한 대화처리 
`bot.converse()` 를 통해서 뿐만 아니라 context 만 가지고 있다면 어떤 KomlBot 으로부터 `bot.respond()`를 통해 답변을 받을 수 있어요.

```python
import json
from koml import KomlBot, Context

with open('context.json', 'r') as fr:
    ctx_json= json.load(fr)
    context = Context.from_json(ctx_json)

bot = KomlBot()
bot.learn(['hello.xml'])

# context의 memo에서 key=user_name 조회
rsp = bot.respond('내 이름 뭐게', context)

print(rsp)
# 어니언이잖아ㅋㅋ 
print(context)
# ==================================
# Context:
#     - history
#       Chat(내 이름 뭐게 -> 어니언이잖아ㅋㅋ)
#       Chat(뭐야 -> 뭐긴 뭐야)
#     - memo
#       {'user_name': '어니언', 'user_age': 26}
# ===================================
```



