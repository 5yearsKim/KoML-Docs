
# 이모저모


## Limitation
KoML은 형태소 분석 후 패턴을 매치시키는 방식으로 동작해서 형태소 분석기의 성능에 의존적일 수 밖에 없어요. 형태소 분석은 [MeCab](https://taku910.github.io/mecab/)을 이용하는데 Mecab의 형태소 분석 결과가 정확하지 않을 때가 종종 있어요.
<br>
<br>
예를들어 '지금 몇시야' 같은 문장의 경우

> [$지금/MAG, $_, $몇/MM, $시/NNBC, $야($이/VCP, $야/EF)/VCP+EF]

가 정확한 분석이지만 MeCab의 경우에는 
> [$지금/MAG, $_, $몇/MM, $시야/NNG]

'시야' 라는 명사(NNG)로 분석을 해버려요.
<br>
<br>

또 다른 예시로 '나의 이름은 현우야' 라는 문장에 대해

> [$나/NP, $의/JKG, $_, $이름/NNG, $은/JX, $_, $영우/NNG, $야[$이/VCP, $야/EF]/VCP+EF]

가 정확한 분석이지만 MeCab은
<br>
> [$나/NP, $의/JKG, $_, $이름/NNG, $은/JX, $_, $현/MM, $우야/NNG]

처럼 현(관형사) + 우야(명사) 로 분석을 해요.

<br>
이런식으로 MeCab이 정확하지 않은 분석결과를 내게 되면 KoML 도 부정확한 결과를 가져오기도 해요. 

<br>
<br>

## TODO
[KoNLPy](https://konlpy.org/en/latest/) 등에서 제공하는 다른 형태소 분석기들 중 MeCab이 속도나 정확도 측면에서 가장 우수하다고 판단해서 현재는 MeCab으로만 지원돼요. 추후에 여러가지 형태소 분석기들을 선택해서 KoML에 적용할 수 있게끔 확장해 볼 수 있을 것 같아요. 

<br>

## To All
KoML 프로젝트에 관한 질문 및 이슈 사항은 [issues](https://github.com/5yearsKim/KoML/issues)에 남겨주세요! 

<br>

감사합니다 :)