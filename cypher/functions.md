### 3.4. 함수

```
이 섹션에는 Cypher 쿼리 언어의 모든 기능에 대한 정보가 포함되어 있습니다.
```
- 술어 함수 [요약](https://mossupport.github.io/developer-manual/cypher/functions/functions.html#%EC%88%A0%EC%96%B4-%ED%95%A8%EC%88%98functionspredicateshtml)
- 스칼라 함수 [요약](https://mossupport.github.io/developer-manual/cypher/functions/functions.html#%EC%8A%A4%EC%B9%BC%EB%9D%BC-%ED%95%A8%EC%88%98functionsscalarhtml)
- 리스트 함수 [요약](https://mossupport.github.io/developer-manual/cypher/functions/functions.html#%EB%A6%AC%EC%8A%A4%ED%8A%B8-%ED%95%A8%EC%88%98functionslistshtml)
- 수학 함수 [요약](https://mossupport.github.io/developer-manual/cypher/functions/functions.html#%EC%88%98%ED%95%99-%ED%95%A8%EC%88%98functionsmathematicalhtml)
  - 수치 함수 [요약](https://mossupport.github.io/developer-manual/cypher/functions/functions.html#%EC%88%98%EC%B9%98-%ED%95%A8%EC%88%98)
  - 로그 함수 [요약](https://mossupport.github.io/developer-manual/cypher/functions/functions.html#%EB%A1%9C%EA%B7%B8-%ED%95%A8%EC%88%98)
  - 삼각 함수 [요약](https://mossupport.github.io/developer-manual/cypher/functions/functions.html#%EB%A1%9C%EA%B7%B8-%ED%95%A8%EC%88%98)
- 문자열 함수 [요약](https://mossupport.github.io/developer-manual/cypher/functions/functions.html#%EB%AC%B8%EC%9E%90%EC%97%B4-%ED%95%A8%EC%88%98)
- 공간 함수 [요약](https://mossupport.github.io/developer-manual/cypher/functions/functions.html#%EA%B3%B5%EA%B0%84-%ED%95%A8%EC%88%98)
- [사용자 정의 함수](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/user-defined-function/)

Cypher 함수는 대부분 입력 매개 변수가 `null` 입니다.


##### 술어 함수

이 함수는 주어진 인수에 대해 true 또는 false를 반환합니다.

| 함수                                                         | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [all()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/predicate/#functions-all) | 조건자(predicate)가 리스트의 모든 요소에 적용되는지 여부를 테스트합니다. |
| [any()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/predicate/#functions-any) | 술어가 목록에있는 하나 이상의 요소에 대해 보유하는지 여부를 테스트합니다. |
| [exists()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/predicate/#functions-exists) | 그래프와 패턴이 일치하거나 노드, 관계 또는 맵에 속성이 있는 경우 true를 반환합니다. |
| [none()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/predicate/#functions-none) | 술어가 리스트에 요소가 없는 경우 true를 리턴합니다.          |
| [single()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/predicate/#functions-single) | 술어가 리스트의 요소 중 하나에 대해 보유하는 경우 true를 리턴합니다. |

##### 스칼라 함수

이 함수는 단일 값을 반환합니다.

| 함수                                                         | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [coalesce()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/scalar/#functions-coalesce) | 전달 된 표현식 목록의 첫 번째 `null` 값을 반환합니다.        |
| [endNode()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/scalar/#functions-endnode) | 관계의 마지막 노드를 반환합니다.                             |
| [head()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/scalar/#functions-head) | 리스트의 첫번째 요소를 반환합니다.                           |
| [id()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/scalar/#functions-id) | 관계 또는 노드의 ID를 반환합니다.                            |
| [last()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/scalar/#functions-last) | 리스트의 마지막 요소를 반환합니다.                           |
| [length()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/scalar/#functions-length) | 패스의 길이를 반환합니다.                                    |
| [properties()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/scalar/#functions-properties) | 인수가 노드 또는 관계인 경우 반환 된 맵은 해당 속성의 맵입니다. |
| [size()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/scalar/#functions-size) | 목록에있는 항목의 수를 반환합니다.                           |
| [패턴표현의 크기](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/scalar/#functions-size-of-pattern-expression) | 패턴 표현식과 일치하는 하위 그래프의 수를 반환합니다.        |
| [문자열의 크기](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/scalar/#functions-size-of-string) | 문자열의 크기를 반환합니다.                                  |
| [startNode()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/scalar/#functions-startnode) | 관계의 첫번째 노드를 돌려줍니다.                             |
| [timestamp()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/scalar/#functions-timestamp) | 현재 시간과 UTC 1970 년 1월 1일 자정 사이의 밀리 초 단위 차이를 반환합니다. |
| [toBoolean()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/scalar/#functions-toboolean) | 인수를 부울로 변환하고 결과를 반환합니다.                    |
| [toFloat()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/scalar/#functions-tofloat) | 인수를 float 로 변환하고 결과를 반환합니다.                  |
| [toInteger()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/scalar/#functions-tointeger) | 인수를 정수로 변환하고 결과를 반환합니다.                    |
| [type()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/scalar/#functions-type) | 관계 타입의 표현을 반환합니다.                               |

##### 집계 함수

이 함수는 여러 인수 값을 계산하여 반환합니다.

| 함수                                                         | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [avg()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/aggregating/#functions-avg) | 숫자 열의 평균을 구합니다.                                   |
| [collect()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/aggregating/#functions-collect) | 수집 된 모든 값을 포함하는 목록을 반환합니다.                |
| [count()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/aggregating/#functions-count) | 행수를 돌려줍니다.                                           |
| [max()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/aggregating/#functions-max) | 숫자 열에서 가장 큰 값을 반환합니다.                         |
| [min()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/aggregating/#functions-min) | 숫자 열에서 가장 작은 값을 반환합니다.                       |
| [percentileCont()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/aggregating/#functions-percentilecont) | 선형 보간법을 사용하여 그룹에 대해 주어진 값의 백분위 수를 반환합니다. |
| [percentileDisc()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/aggregating/#functions-percentiledisc) | 반올림 메서드를 사용하여 그룹에 대해 주어진 백분위 수에 가장 가까운 값을 반환합니다. |
| [stDev()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/aggregating/#functions-stdev) | 모집단 샘플에 대한 그룹에 대해 주어진 값의 표준 편차를 구합니다. |
| [stDevP()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/aggregating/#functions-stdevp) | 전체 채우기에 대해 그룹에 대해 주어진 값의 표준 편차를 구합니다. |
| [sum()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/aggregating/#functions-sum) | 수치 열의 합계를 돌려줍니다.                                 |

##### 리스트 함수

이 함수는 다른 값 목록을 반환합니다. 

| 함수                                                         | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [extract()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/list/#functions-extract) | 단일 속성 또는 노드 또는 관계 목록에서 함수 값을 반환합니다. |
| [filter()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/list/#functions-filter) | 술어를 준수하는 목록의 모든 요소를 반환합니다.               |
| [keys()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/list/#functions-keys) | 노드, 관계 또는 맵의 속성 이름에 대한 문자열 표현 목록을 반환합니다. |
| [labels()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/list/#functions-labels) | 노드에 첨부 된 레이블에 대한 문자열 표현 목록을 반환합니다.  |
| [nodes()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/list/#functions-nodes) | 경로의 모든 노드를 반환합니다.                               |
| [range()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/list/#functions-range) | 범위 내의 수치를 돌려줍니다.                                 |
| [reduce()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/list/#functions-reduce) | 목록의 개별 요소에 대해 표현식을 실행하여 표현식의 결과를 누적기에 저장합니다. |
| reverse()                                                    | 원래 목록의 모든 요소 순서가 바뀐 목록을 반환합니다.         |
| [relationships()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/list/#functions-relationships) | 경로의 모든 관계를 반환합니다.                               |
| [tail()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/list/#functions-tail) | 리스트의 최초의 요소 이외의 모든 것을 돌려줍니다.            |

##### 수학 함수

이 함수는 모두 숫자 식에만 적용되며 다른 값에 사용하면 오류를 반환합니다.

###### 수치 함수

| 함수                                                         | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [abs()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/) | 숫자의 절대 값을 반환합니다.                                 |
| [ceil()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/mathematical/numeric/#functions-ceil) | 인수보다 크거나 같은 가장 작은 정수를 구합니다.              |
| [floor()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/mathematical/numeric/#functions-floor) | 표현식보다 작거나 같은 가장 큰 정수를 반환합니다.            |
| [rand()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/mathematical/numeric/#functions-rand) | 0 이상 1 이하의 범위의 난수를 돌려줍니다.                    |
| [round()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/mathematical/numeric/#functions-round) | 가장 가까운 정수로 반올림 한 숫자 식을 반환합니다.           |
| [sign()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/mathematical/numeric/#functions-sign) | 숫자의 부호를 반환합니다. 표현식이 0이면 0, 임의의 음수는 `-1` , 양수이면 `1` 을 반환합니다. |

###### 로그 함수

| 함수                                                         | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [e()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/mathematical/logarithmic/#functions-e) | 자연 대수의 밑, `e`를 구합니다.                              |
| [exp()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/mathematical/logarithmic/#functions-exp) | `e^n` 반환합니다. 여기서 `e` 는 자연 대수의 밑이며, `n` 은 인수 표현식의 값입니다. |
| [log()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/mathematical/logarithmic/#functions-log) | 식의 자연 대수를 반환합니다.                                 |
| [log10()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/mathematical/logarithmic/#functions-log10) | 식의 공통 대수 (밑 10)를 반환합니다.                         |
| [sqrt()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/mathematical/logarithmic/#functions-sqrt) | 숫자의 제곱근을 반환합니다.                                  |

###### 삼각 함수

달리 지정되지 않는 한 모든 삼각 함수는 라디안에서 작동합니다.

| 함수                                                      ㅋ   | 설명                                                 |
| ------------------------------------------------------------ | ---------------------------------------------------- |
| [acos()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/mathematical/#functions-acos) | 표현식의 아크 코사인(arccosine)을 반환합니다.        |
| [asin()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/mathematical/#functions-asin) | 표현식의 아크 사인(arcsine)을 반환합니다.            |
| [atan()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/mathematical/#functions-atan) | 표현식의 아크 탄젠트(arctangent)를 반환합니다.       |
| [atan2()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/mathematical/#functions-atan2) | 좌표 세트의 아크 탄젠트 2(arctangent2)를 돌려줍니다. |
| [cos()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/mathematical/#functions-cos) | 표현식의 코사인(cos)을 반환합니다.                   |
| [cot()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/mathematical/#functions-cot) | 표현식의 코탄젠트(cotangent)를 반환합니다.           |
| [degrees()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/mathematical/#functions-degrees) | 라디안을 도 단위로 변환합니다.                       |
| [haversin()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/mathematical/#functions-haversin) | 표현식의 절반을 반환합니다.                          |
| [pi()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/mathematical/#functions-pi) | 수학 상수 pi를 반환합니다.                           |
| [radians()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/mathematical/#functions-radians) | 도를 라디안으로 변환합니다.                          |
| [sin()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/mathematical/#functions-sin) | 표현식에서 사인(sin)을 반환합니다.                   |
| [tan()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/mathematical/#functions-tan) | 표현식에서 탄젠트(tan)을 반환합니다.                 |

###### 문자열 함수

이 함수는 문자열을 조작하거나 다른 값의 문자열 표현을 작성하는 데 사용됩니다.

| 함수                                                         | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [left()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/string/#functions-left) | 원래 문자열의 왼쪽 n 문자를 포함하는 문자열을 반환합니다.    |
| [lTrim()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/string/#functions-ltrim) | 왼쪽에서 공백을 제거한 원래 문자열을 반환합니다.             |
| [replace()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/string/#functions-replace) | 검색 문자열이 대체 문자열로 대체 된 문자열을 반환하여 모든 항목을 대체합니다. |
| [reverse()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/string/#functions-reverse) | 원 문자열의 반대열을 반환합니다.                             |
| [right()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/string/#functions-right) | 원래 문자열의 오른쪽 n 문자를 포함하는 문자열을 반환합니다.  |
| [rTrim()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/string/#functions-rtrim) | 오른쪽에서 공백을 제거한 원래 문자열을 반환합니다.           |
| [split()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/string/#functions-split) | 분할 패턴으로 구분 된 문자열의 시퀀스를 반환합니다.          |
| [substring()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/string/#functions-substring) | 0부터 시작하는 인덱스 start 및 length를 사용하여 원본의 하위 문자열을 반환합니다. |
| [toLower()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/string/#functions-tolower) | 소문자로 된 원래 문자열을 반환합니다.                        |
| [toString()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/string/#functions-tostring) | 인수를 문자열로 변환합니다.                                  |
| [toUpper()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/string/#functions-toupper) | 대문자로 된 원래 문자열을 반환합니다.                        |
| [trim()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/string/#functions-trim) | 양쪽에서 공백을 제거한 원래 문자열을 반환합니다.             |

###### 시간 함수

날짜, 시간, 현지 시각, 날짜 시간, 현지날짜 시간과 지속 기간의 값을 나타냅니다. 

| 함수                                                         | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [date()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-date-current) | 현재 날짜를 반환합니다.                                      |
| [date.transaction()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-date-current-transaction) | `transaction` 시계를 사용해서 현재 날짜를 반환합니다.        |
| [date.statement()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-date-current-statement) | `statement` 시계를 사용해서 현재 날짜를 반환합니다.          |
| [date.realtime()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-date-current-realtime) | `realtime` 시계를 사용해서 현재 날짜를 반환합니다.           |
| [date({year [, month, day\]})](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-date-calendar) | (년-월-일) *Date* 달력을 반환합니다.                         |
| [date({year [, week, dayOfWeek\]})](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-date-week) | (년-월-일) *Date* 을 반환합니다.                             |
| [date({year [, quarter, dayOfQuarter\]})](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-date-quarter) | (년-분기-일) *Date* 분기를 반환합니다.                       |
| [date({year [, ordinalDay\]})](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-date-ordinal) | 원래 (년-일) *Date*을 반환합니다.                            |
| [date(string)](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-date-create-string) | 문자열을 파싱해서 *Date*을 반환합니다.                       |
| [date({map})](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-date-temporal) | 다른 임시 값 성분 맵에서 *Date* 을 반환합니다.               |
| [date.truncate()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-date-truncate) | 특정 성분 한계 값을 줄여서 얻어진 *Date*을 반환합니다.       |
| [datetime()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-datetime-current) | 현재 *DateTime*을 반홥합니다.                                |
| [datetime.transaction()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-datetime-current-transaction) | `transaction` 시계를 이용해서 현재 *DateTime*을 반환합니다.  |
| [datetime.statement()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-datetime-current-statement) | `statement` 시계를 이용해서 현재 *DateTime*을 반환합니다.    |
| [datetime.realtime()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-datetime-current-realtime) | `realtime` 시계를 이용해서 현재 *DateTime*을 반환합니다.     |
| [datetime({year [, month, day, …\]})](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-datetime-calendar) | (년-월-일) *DateTime* 달력을 반환합니다.                     |
| [datetime({year [, week, dayOfWeek, …\]})](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-datetime-week) | (년-주-일) *DateTime* 주를 반환합니다.                       |
| [datetime({year [, quarter, dayOfQuarter, …\]})](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-datetime-quarter) | (년-분기-일) *DateTime*분기를 반환합니다.                    |
| [datetime({year [, ordinalDay, …\]})](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-datetime-ordinal) | 원시 (년-일) *DateTime*을 반환합니다.                        |
| [datetime(string)](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-datetime-create-string) | 문자열을 파싱해서 *DateTime*을 반환합니다.                   |
| [datetime({map})](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-datetime-temporal) | 맵의 다른 임시 값 성분에서 *DateTime*을 반환합니다.          |
| [datetime({epochSeconds})](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-datetime-timestamp) | 타임스탬프에서  *DateTime* 을 반환합니다.                    |
| [datetime.truncate()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-datetime-truncate) | 특정 성분 한계 값을 줄여서 얻어진 *DateTime*을 반환합니다.   |
| [localdatetime()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-localdatetime-current) | 현재 *LocalDateTime*을 반환합니다.                           |
| [localdatetime.transaction()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-localdatetime-current-transaction) | `transaction` 시계를 이용해서 현재 *LocalDateTime*을 반환합니다. |
| [localdatetime.statement()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-localdatetime-current-statement) | `statement` 시계를 이용해서 현재 *LocalDateTime*을 반환합니다. |
| [localdatetime.realtime()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-localdatetime-current-realtime) | `realtime` 시계를 이용해서 현재 *LocalDateTime*을 반환합니다. |
| [localdatetime({year [, month, day, …\]})](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-localdatetime-calendar) | (년-월-일) *LocalDateTime* 달력을 반환합니다.                |
| [localdatetime({year [, week, dayOfWeek, …\]})](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-localdatetime-week) | (년-주-일) *LocalDateTime* 주를 반환합니다.                  |
| [localdatetime({year [, quarter, dayOfQuarter, …\]})](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-localdatetime-quarter) | (년-분기-일) *DateTime* 분기를 반환합니다.                   |
| [localdatetime({year [, ordinalDay, …\]})](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-localdatetime-ordinal) | 원시 (년-일) *LocalDateTime*를 반환합니다.                   |
| [localdatetime(string)](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-localdatetime-create-string) | 문자열을 파싱해서 *LocalDateTime*을 반환합니다.              |
| [localdatetime({map})](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-localdatetime-temporal) | 다른 임시 값 성분 맵에서 *LocalDateTime* 을 반환합니다.      |
| [localdatetime.truncate()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-localdatetime-truncate) | 특정 성분 한계 값을 줄여서 얻어진 *LocalDateTime*을 반환합니다. |
| [localtime()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-localtime-current) | 현재 *LocalTime*을 반환합니다.                               |
| [localtime.transaction()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-localtime-current-transaction) | ```transaction``` 시계를 사용해서 현재 *LocalTime*을 반환합니다. |
| [localtime.statement()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-localtime-current-statement) | `statement` 시계를 사용해서 현재 *LocalTime*을 반환합니다.   |
| [localtime.realtime()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-localtime-current-realtime) | `realtime` 시계를 사용해서 현재 *LocalTime*을 반환합니다.    |
| [localtime({hour [, minute, second, …\]})](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-localtime-create) | 특정 성분과 *LocalTime*을 반환합니다.                        |
| [localtime(string)](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-localtime-create-string) | 문자열을 파싱해서 *LocalTime*을 반환합니다.                  |
| [localtime({time [, hour, …\]})](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-localtime-temporal) | 다른 임시 값 성분 맵에서 *LocalTime* 을 반환합니다.          |
| [localtime.truncate()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-localtime-truncate) | 특정 성분 한계 값을 줄여서 얻어진 *LocalTime*을 반환합니다.  |
| [time()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-time-current) | 현재 시간을 반환합니다.                                      |
| [time.transaction()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-time-current-transaction) | ```transaction``` 시계를 사용해서 현재 *Time*을 반환합니다.  |
| [time.statement()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-time-current-statement) | `statement` 시계를 사용해서  현재 *Time*을 반환합니다.       |
| [time.realtime()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-time-current-realtime) | `realtime` 시계를 사용해서  현재 *Time*을 반환합니다.        |
| [time({hour [, minute, …\]})](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-time-create) | *Time*을 특정 성분 값과 함께 반환합니다.                     |
| [time(string)](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-time-create-string) | 문자열을 파싱해서  *Time* 을 반환합니다.                     |
| [time({time [, hour, …, timezone\]})](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-time-temporal) | 다른 임시 값 성분 맵에서 *Time* 을 반환합니다                |
| [time.truncate()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-time-truncate) | 특정 성분 한계 값을 줄여서 얻어진 *Time*을 반환합니다.       |
| [duration({map})](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-duration-create-components) | 컴퍼넌트의 맵에서  *Duration*을 반환합니다.                  |
| [duration(string)](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-duration-create-string) | 문자열을 파싱해서 *Duration*을 반환합니다.                   |
| [duration.between()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-duration-between) | 주어진 2 개의 인스턴스 차이와 동일한 *Duration*을 반환합니다. |
| [duration.inMonths()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-duration-inmonths) | 주어진 두 인스턴스 사이에서 전 월,분기 또는 연도와 차이가 같은 *Duration*을 반환합니다. |
| [duration.inDays()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-duration-indays) | 주어진 두 인스턴트 사이의 일 또는 주간 차이와 동일한 *Duration*을 반환합니다. |
| [duration.inSeconds()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/temporal/#functions-duration-inseconds) | 주어진 두 인스턴트 사이의 초 차이 및 분 수 또는 시간과 같은 *Duration* 반환합니다. |

###### 공간 함수

이 함수는 2D 좌표계에서 점을 지정하고 두 점 사이의 측지선 거리를 계산할 때 사용합니다.

| 함수                                                         | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [distance()](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/spatial/#functions-distance) | 지정된 두 점 사이의 측지선 거리를 나타내는 부동 소수점 숫자를 반환합니다. |
| [point() - cartesian 2D](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/spatial/#functions-point-cartesian) | 두 개의 좌표 값이있는 점 객체를 반환합니다.                  |
| [point() - WGS 84](https://neo4j.com/docs/developer-manual/3.1/cypher/functions/spatial/#functions-point) | WGS 84에서 두 개의 좌표 값이있는 점 객체를 반환합니다.       |
 