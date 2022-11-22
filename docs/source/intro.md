
그동안은 챗봇을 쉽게 제작할 수 있는 [AIML](https://en.wikipedia.org/wiki/Artificial_Intelligence_Markup_Language)과 같은 언어가 영어에는 효과적이지만 한국어에는 적용이 어려웠어요.
<br><br>
**이제는 이렇게 써볼 수 있어요.**
<br>

```xml
<koml>
  <case>
    <pattern>너_j 이름_j 뭐야?</pattern>
    <template>내 이름은 코엠엘챗봇이야!</template>
  </case>
</koml>
```

```
<< 너 이름이 뭐야?
>> 내 이름은 코엠엘챗봇이야!
```