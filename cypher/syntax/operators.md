### 3.2.7. Operators

- 연산자 개요

- 일반 연산자
  - ```DISTINCT``` 연산자 사용
  - ```.```연산자 사용해서 중첩된 리터럴 맵 속성에 액세스
  - ```[]``` 연산자를 사용해서 동적으로 생성된 키 필터링  

- 수리 연산자
  - 지수 연산자 ```^``` 사용
  - 단항 마이너스 연산자 ```-``` 사용 

- 비교 연산자
  - 두 가지 숫자 비교 
  - 이름 필터링을 위한 ```STARTS WITH``` 사용

- 논리 연산자
  - 숫자 필터링을 위한 논리 연산자 사용

- 문자열 연산자
  - 단어를 필터링하는 ```=~``` 정규표현식 사용

- 임시 연산자
  - *Duration*을 시간상 순간에 추가하거나 빼기
  - *Duration*을 다른 *Duration*에 추가하거나 빼기 
  - *Duration*을 다른 숫자에서 곱하거나 나누기 

- 리스트 연산자
  - ```+```을 사용하여 두 가지 리스트 연결
  - ```IN```을 사용하여 리스트 내에 숫자가 있는지 확인 
  - 더 복잡한리스트 멤버쉽 연산을 위해 ```IN```을 사용
  - ```[]``` 연산자를 사용하여 리스트 내 요소 억세스 

- 속성 연산자 
- 값 일치 및 비교
- 값 순서 및 비교
- 비교 연산자 결합

#### 3.2.7.1. 연산자 개요

| 연산자 개요             | `DISTINCT`, `.` : 속성 억세스, `[]` : 다양한 속성 억세스     |
| ----------------------- | ------------------------------------------------------------ |
| 수리 연산자             | `+`, `-`, `*`, `/`, `%`, `^`                                 |
| 비교 연산자             | `=`, `<>`, `<`, `>`, `<=`, `>=`, `IS NULL`, `IS NOT NULL`    |
| 특정-문자열 비교 연산자 | `STARTS WITH`, `ENDS WITH`, `CONTAINS`                       |
| 논리 연산자             | `AND`, `OR`, `XOR`, `NOT`                                    |
| 문자열 연산자           | `+` :연결 , `=~` 정규식 매칭                                 |
| 임시 연산자             | `+` , `-`  : 지속 및 임시 인스턴스 연산  `*`, `/`: 지속 및 숫자 연산 |
| 리스트 연산자           | `+` : 연결, `IN `: 리스트 내 요소 존재 확인  `[]` : 요소 억세스 |


#### 3.2.7.2. 일반 연산자 

일반 연산자는 다음과 같습니다:
- 중복 값을 제거합니다: ```DISTINCT```
- ```.``` 연산자를 이용해서 노드, 관계 및 리터럴 맵을 평가합니다.
- 하위 스크립트 연산자를 이용해서 다양한 속성에 접근합니다: ```[]```

##### ```DISTINCT``` 연산자 사용

```Person``` 노드에서 유일한 눈 색상을 얻습니다.

**쿼리**

```
CREATE (a:Person { name: 'Anne', eyeColor: 'blue' }),(b:Person { name: 'Bill', eyeColor: 'brown' }),(c:Person { name: 'Carol', eyeColor: 'blue' })
WITH [a, b, c] AS ps
UNWIND ps AS p
RETURN DISTINCT p.eyeColor
```


**Anne**와 **Carol** 모두 파랑색 눈을 가졌지만, **blue**는 한 번만 반환됩니다.

| p.eyeColor                                                |
| --------------------------------------------------------- |
| 2 행 노드 추가 : 3 속성 설정: 6 레이블 추가: 3 |
| `"blue"`                                                  |
| `"brown"`                                                 |

일반적으로 ```DISTINCT```는 접속사에서 [집계 함수](/cypher/functions.md)와 함께 쓰입니다. 

##### ```.```연산자 사용해서 중첩된 리터럴 맵 속성에 액세스

**쿼리**

```
WITH { person: { name: 'Anne', age: 25 }} AS p
RETURN p.person.name
```

| p.person.name |
| ------------- |
| 1 row         |
| `"Anne"`      |

##### ```[]``` 연산자를 사용해서 동적으로 생성된 키 필터링  

**쿼리**

```
CREATE (a:Restaurant { name: 'Hungry Jo', rating_hygiene: 10, rating_food: 7 }),(b:Restaurant { name: 'Buttercup Tea Rooms', rating_hygiene: 5, rating_food: 6 }),(c1:Category { name: 'hygiene' }),(c2:Category { name: 'food' })
WITH a, b, c1, c2
MATCH (restaurant:Restaurant),(category:Category)
WHERE restaurant["rating_" + category.name]> 6
RETURN DISTINCT restaurant.name
```

| restaurant.name                                          |
| -------------------------------------------------------- |
| 1 행 추가 : 4 속성 설정: 8 라벨 추가: 4 |
| `"Hungry Jo"`                                            |

다양한 속성 억세스에 관련된 자세한 내용은 [섹션 3.3.7.2, “기본 사용”](/cypher/clauses.md)을 참조하면 됩니다. 

```null```과 관련된 `[]` 연산자 내용은 [이곳](/cypher/syntax/working-with-null.md)에서 확인할 수 있습니다. 

#### 3.2.7.3. 수리 연산자

수학 연산자는 다음과  같이 구성됩니다.

- 덧셈: `+`
- 뺄셈 및 단항 뺄셈: `-`
- 곱셈: `*`
- 나눗셈: `/`
- 모듈 나눗셈: `%`
- 지수: `^`

##### 지수 연산자 ```^``` 사용

**쿼리** 

```
WITH 2 AS number, 3 AS exponent
RETURN number ^ exponent AS result
```

| result |
| ------ |
| 1 row  |
| `8.0`  |

##### 단항 마이너스 연산자 ```-``` 사용 

**쿼리** 

```
WITH -3 AS a, 4 AS b
RETURN b - a AS result
```

| result |
| ------ |
| 1 row  |
| `7`    |

#### 3.2.7.4. 비교 연산자

비교 연산자는 다음과 같습니다:

- 같음: `=`
- 같지 않음: `<>`
- 작은: `<`
- 큰: `>`
- 작거나 같은: `<=`
- 크거나 같은: `>=`
- `IS NULL`
- `IS NOT NULL`

##### 특정 문자열 비교 연산자는 다음과 같습니다:

- `STARTS WITH`: 문자열에서 대/소문자를 구분하는 접두어 검색 수행
- `ENDS WITH`: 문자열에서 대/소문자를 구분하는 접미사 검색 수행
- `CONTAINS`: 문자열에서 대/소문자 포함을 검색 수행 

##### 두 가지 숫자 비교 

**쿼리** 

```
WITH 4 AS one, 3 AS two
RETURN one > two AS result
```

| result |
| ------ |
| 1 row  |
| `true` |


##### 이름을 필터링할 때 ```STARTS WITH```사용 

**쿼리** 

```
WITH ['John', 'Mark', 'Jonathan', 'Bill'] AS somenames
UNWIND somenames AS names
WITH names AS candidate
WHERE candidate STARTS WITH 'Jo'
RETURN candidate
```

| candidate    |
| ------------ |
| 2 rows       |
| `"John"`     |
| `"Jonathan"` |

[섹션 3.3.7.3, “문자열 매칭”](/cypher/clauses.md)에는 특정-문자열 비교 연산자 및 사용되는 예시를 확인할 수 있습니다. 

#### 3.2.7.5. 논리 연산자 

논리 연산자는 다음을 포함합니다:

- 접속서: `AND`
- 분리: `OR`,
- 배타 분리: `XOR`
- 부정: `NOT`

`AND`, `OR`, `XOR` 및 `NOT`의 진리표 입니다. 


| a       | b       | a `AND` b | a `OR` b | a `XOR` b | `NOT` a |
| ------- | ------- | --------- | -------- | --------- | ------- |
| `false` | `false` | `false`   | `false`  | `false`   | `true`  |
| `false` | `null`  | `false`   | `null`   | `null`    | `true`  |
| `false` | `true`  | `false`   | `true`   | `true`    | `true`  |
| `true`  | `false` | `false`   | `true`   | `true`    | `false` |
| `true`  | `null`  | `null`    | `true`   | `null`    | `false` |
| `true`  | `true`  | `true`    | `true`   | `false`   | `false` |
| `null`  | `false` | `false`   | `null`   | `null`    | `null`  |
| `null`  | `null`  | `null`    | `null`   | `null`    | `null`  |
| `null`  | `true`  | `null`    | `true`   | `null`    | `null`  |

##### 숫자 필터링할 때 쓰는 논리 연산자

**쿼리** 

```
WITH [2, 4, 7, 9, 12] AS numberlist
UNWIND numberlist AS number
WITH number
WHERE number = 4 OR (number > 6 AND number < 10)
RETURN number
```

| number |
| ------ |
| 3 rows |
| `4`    |
| `7`    |
| `9`    |

#### 3.2.7.6. 문자열 연산자 

문자열 연산자는 다음을 포함합니다:

- 문자열 연결 : `+`
- 정규 표현식 매칭 : `=~`

##### 단어를 필터링할 때 ```=~```와 정규 표현식 사용 

**쿼리** 

```
WITH ['mouse', 'chair', 'door', 'house'] AS wordlist
UNWIND wordlist AS word
WITH word
WHERE word =~ '.*ous.*'
RETURN word
```

| word      |
| --------- |
| 2 rows    |
| `"mouse"` |
| `"house"` |

필터링에서 정규 표현식이 포함된 곳 관련 정보는 [섹션 3.3.7.4, “정규 표현식”](/cypher/clauses.md)에서 확인할 수 있습니다. 

#### 3.2.7.7. 임시 연산자 

임시 연산자는 다음을 포함합니다:

- [*Duration*](/cypher/temporal.md)을 [임시 인스턴스](/syntax/temporal.md) 또는 다른 *Duration*에 추가 : `+`
- *Duration*을 다른 임시 인스턴스나 다른 *Duration*에서 제거 : ```-```
- *Duration*을 숫자와 곱하기 : ```*```
- *Duration*을 숫자로 나누기 : ```/```

아래 표는 각 연산자 및 피연산 함수 종류의 결합을 나타내며, 각 임시 연산자 어플리케이션에서 값 종류를 반환합니다. 

| 연산자 | 왼쪽 피연산자       | 오른쪽 피연산자     | 결과 유형           |
| ------ | ------------------- | ------------------- | ------------------- |
| `+`    | 즉각적인 시간       | 지속 기간(Duration) | 즉각적인 시간 유형  |
| `+`    | 지속 기간(Duration) | 즉각적인 시간       | 즉각적인 시간 유형  |
| `-`    | 즉각적인 시간       | 지속 기간(Duration) | 즉각적인 시간 유형  |
| `+`    | 지속 기간(Duration) | 지속 기간(Duration) | 지속 기간(Duration) |
| `-`    | 지속 기간(Duration) | 지속 기간(Duration) | 지속 기간(Duration) |
| `*`    | 지속 기간(Duration) | 숫자                | 지속 기간(Duration) |
| `*`    | 숫자                | 지속 기간(Duration) | 지속 기간(Duration) |
| `/`    | 지속 기간(Duration) | 숫자                | 지속 기간(Duration) |

 
##### 임시 인스턴스에서 *Duration*을 시간상 순간에 추가하거나 빼기

**쿼리** 

```
WITH localdatetime({ year:1984, month:10, day:11, hour:12, minute:31, second:14 }) AS aDateTime, duration({ years: 12, nanoseconds: 2 }) AS aDuration
RETURN aDateTime + aDuration, aDateTime - aDuration
```

| aDateTime + aDuration           | aDateTime - aDuration           |
| ------------------------------- | ------------------------------- |
| 1 row                           |                                 |
| `1996-10-11T12:31:14.000000002` | `1972-10-11T12:31:13.999999998` |

시간 순간에 적용되지 않는 [*Duration* 구성 요소](/syntax/temporal.md)는 무시됩니다. 예를 들어 *Duration*을 *Date*에 추가하면 *Duration*의 *hours*, *minutes*, *seconds* 및 *nanoseconds*는 무시됩니다. (*Time*도 비슷하게 동작) 

**쿼리** 

```
WITH date({ year:1984, month:10, day:11 }) AS aDate, duration({ years: 12, nanoseconds: 2 }) AS aDuration
RETURN aDate + aDuration, aDate - aDuration
```

| aDate + aDuration | aDate - aDuration |
| ----------------- | ----------------- |
| 1 row             |                   |
| `1996-10-11`      | `1972-10-11`      |

시간적 순간에 두 개의 지속 시간을 추가하는 것은 결합 연산이 아닙니다. 존재하지 않는 날짜가 존재하는 날짜 중에 가까운 날로 잘리기 때문입니다.

**쿼리** 

```
RETURN (date("2011-01-31")+ duration("P1M"))+ duration("P12M") AS date1, date("2011-01-31")+(duration("P1M")+ duration("P12M")) AS date2
```

| date1        | date2        |
| ------------ | ------------ |
| 1 row        |              |
| `2012-02-28` | `2012-02-29` |

##### *Duration*을 다른 *Duration*에 추가하거나 빼기 

**쿼리** 

```
WITH duration({ years: 12, months: 5, days: 14, hours: 16, minutes: 12, seconds: 70, nanoseconds: 1 }) AS duration1, duration({ months:1, days: -14, hours: 16, minutes: -12, seconds: 70 }) AS duration2
RETURN duration1, duration2, duration1 + duration2, duration1 - duration2
```

| duration1                       | duration2           | duration1 + duration2       | duration1 - duration2       |
| ------------------------------- | ------------------- | --------------------------- | --------------------------- |
| 1 row                           |                     |                             |                             |
| `P12Y5M14DT16H13M10.000000001S` | `P1M-14DT15H49M10S` | `P12Y6MT32H2M20.000000001S` | `P12Y4M28DT24M0.000000001S` |

##### *Duration*을 다른 숫자에서 곱하거나 나누기 

이러한 연산은 단순히 나누기(및 분수와 곱하기)의 경우 평균 단위 길이를 기반으로 더 작은 단위로 오버플로되는 구성 요소 별 연산으로 해석합니다.

**쿼리** 

```
WITH duration({ days: 14, minutes: 12, seconds: 70, nanoseconds: 1 }) AS aDuration
RETURN aDuration, aDuration * 2, aDuration / 3
```

| aDuration               | aDuration * 2           | aDuration / 3            |
| ----------------------- | ----------------------- | ------------------------ |
| 1 row                   |                         |                          |
| `P14DT13M10.000000001S` | `P28DT26M20.000000002S` | `P4DT16H4M23.333333333S` |

#### 3.2.7.8. 리스트 연선자 

리스트 연산자는 다음을 포함합니다:

- 연결 리스트 : `l1` 와 `l2`: `[l1] + [l2]`
- 리스트 ```l```에 ```e``` 요소가 있는지 확인: ```e IN [I]```
- 첨자 연산자를 이용해서 리스트 내 요소 억세스 : ```[]```

```null``과 관련된 ```IN``` 및 ```[]``` 연산자와 관련된 상세 정보는 [이곳](/syntax/working-with-null.md)에서 확인할 수 있습니다. 
 
##### 두 가지 리스트 연결 : `+` 사용 

**쿼리** 

```
RETURN [1,2,3,4,5]+[6,7] AS myList
```

| myList                                 |
| -------------------------------------- |
| 1 row                                  |
| `JavaListWrapper(1, 2, 3, 4, 5, 6, 7)` |


##### 리스트 내 숫자 존재 여부 확인 : `IN` 사용

**쿼리** 

```
WITH [2, 3, 4, 5] AS numberlist
UNWIND numberlist AS number
WITH number
WHERE number IN [2, 3, 8]
RETURN number
```

| 숫자 |
| ------ |
| 2 rows |
| `2`    |
| `3`    |

##### ```IN```을 사용하여 리스트 내에 숫자가 있는지 확인 

일반적인 규칙 ```IN```연산자는 주어진 리스트가 왼쪽 피연산자와 같은 *type 및 contents(또는 값)*을 갖는 요소를 포함하면 ```true```로 평가할 것 입니다. 
 
리스트는 오직 다른 리스트와 비교할 수 있으며, 리스트의 원소는 ```l```의 첫번째 원소에서 ```l```의 마지막 원소까지 오름차순 쌍으로 비교합니다. 

아래 쿼리는 리스트 ```[2,1]```이  ```[1, [2, 1], 3]``` 리스트 원소인지 확인합니다. 

**쿼리** 

```
RETURN [2, 1] IN [1,[2, 1], 3] AS inList
```

오른쪽 리스트가 왼쪽 피연산자와 동일한 내용(주어진 순서대로 ```1```,```2```)을 포함하는 같은 유형 리스트에 ```[1, 2]```를 원소로 포함하고 있으므로 쿼리는 ```true```로 평가됩니다. 
 
왼쪽 피연산자 값이 ```[2, 1]```가 아닌 ```[1,2]```라면, 쿼리는 ```false```를 반환합니다. 

| inList |
| ------ |
| 1 row  |
| `true` |

왼쪽 피연산자와 오른쪽 피연산자는 아래 쿼리와 같이 언뜻 보기에 동일하게 보입니다. 


**쿼리** 

```
RETURN [1, 2] IN [1, 2] AS inList
```

그러나, 오른쪽 피연산자는 왼쪽 피연산자와 같은 *유형* 즉 *목록* 원소를 포함하지 않으므로 ```IN```은 ```false```로 평가 됩니다. 


| inList  |
| ------- |
| 1 row   |
| `false` |

다음 쿼리는 [labels ()](/cypher/functions.md) 함수에서 가져온 리스트 `llhs`가 다른 목록 `lrhs`에 있는 요소를 한 개 이상 포함하는지 확인할 때 쓰입니다.  
 
```
MATCH (n)
WHERE size([l IN labels(n) WHERE l IN ['Person', 'Employee'] | 1]) > 0
RETURN count(n)
```

```labels(n)```이 ```Person``` 또는 ```Employee``` (또는 두개 모두)를 반환하는 한, 쿼리는 0이상 값을 반환합니다. 

##### ```[]``` 연산자를 사용하여 리스트 내 요소 억세스 

**쿼리** 

```
WITH ['Anne', 'John', 'Bill', 'Diane', 'Eve'] AS names
RETURN names[1..3] AS result
```

대괄호는 시작 인덱스 '1'에서 끝 인덱스 '3'까지 요소를 추출합니다.
 
| 결과                        |
| ----------------------------- |
| 1 row                         |
| `JavaListWrapper(John, Bill)` |

리스트 관련 내용은 [섹션 3.2.11.1, “일반적인 리스트”](syntax/lists.md)에서 확인할 수 있습니다. 

#### 3.2.7.9. 속성 연산자 

버전 2.0 이후, 이전에 속성 연산자 ```?```와 ```!```는 더이상 지원되지 않습니다. 이 구문은 더이상 지원하지 않습니다. 누락된 속성은 이제 ```null```로 반환됩니다. 

```?```연산자의 이전 행위를 알려면 ```(NOT(exists(<ident>.prop)) OR <ident>.prop=<value>)```를 사용하십시오. 또한, 선택적 관계에 쓰이는 ```?```는 ```OPTIONAL MATCH```가 생기면서 제거되었습니다. 


#### 3.2.7.10. 값의 일치 및 비교 

##### 일치 

Cypher는  `=` 와 `<>` 연산자를 이용해서 값이 일치하는지 확인합니다. ([섹션 3.2.1, “값 및 유형”](/syntax/values.md) 참조) 

이들이 같은 값을 가질 경우에만 같은 유형의 값 입니다. (예. `3 = 3` , `"x" <> "xy"`)

맵은 정확히 같은 키를 값과 맵핑할 경우에만 같으며, 리스트는 동일한 값과 같은 순서를 가질 때만 같습니다. (예. `[3, 4] = [1+2, 8/2]`).
 

서로 다른 종류의 값은 다음 규칙에 따라 동일하게 간주합니다. 

- 경로는 경로 노드의 리스트로 여기며 인련의 노드와 관계의 동일한 순서가 포함된 모든 리스트와 동일합니다. 
- ```null``` 값에 대하여 ```=```와 ```<>```를 테스트할 때 연산자는 항상 ```null```입니다. 이것은 ```null=null```와 ```null <> null```을 포함합니다. 

값 ```v```가 ```null```인지 정확히 확인하려면 특수 ```v IS NULL``` 또는 ```v IS NOT NULL``` 등가 연산자를 사용하면 됩니다. 
 
다른 모든 조합의 값 유형은 서로 비교할 수 없습니다. 특히 노드, 관계 및 리터럴 맵은 서로 비교할 수 없습니다. 

비교할 수 없는 값을 비교하는 것은 에러입니다. 


#### 3.2.7.11. 값 순서 및 비교 

비교 연산자 ```<=```, ```<``` (오름차순) 및 ```>=```, ```>``` (내림차순)은 순서에 대한 값을 비교할 때 사용됩니다.

아래는 비교가 수행되는 방법을 자세히 나타냅니다. 
 
- 숫자 순서를 사용하여 순서를 비교할 때 숫자 값을 비교합니다. (예 : '3 <4').
- 특수 값 ```java.lang.Double.NaN```은 모든 다른 숫자보다 큰 것으로 간주합니다. 
- 문자열 값은 사전 순서로 비교합니다. (즉, "x" < "xy").
- 논리 값은 ```false < true```와 같은 순서로 비교됩니다.

- 공간 값 **비교** :
  - 포인트 값은 같은 좌표 참조 시스템(Coordinate Reference System (CRS)) 내에서 비교할 수 있습니다.- 그러힞 않으면, 결과 값은 ```null```입니다.
  - 같은 CRS 내에 ```a```와 ```b```는 ```a.x > b.x```이고 ```a.y > b.y```(3D 포인트에서 ```a.z > b.z```)로 간주합니다.
  - ```a.x < b.x```이고 ```a.y < b.y```(3D 포인트에서 ```a.z < b.z```)일 경우 ```a```는 ```b```보다 작다고 간주합니다. 
  - 위의 어떤 것도 참이 아닐 경우 포인트는 비교할 수 없다고 간주되며 비교 연산자 사이의 값은 ```null```을 반환 합니다. 


- 공간 값 **정렬** :

  - ```ORDER BY```을 사용하려면, 모든 값을 정렬할 수 있어야 합니다. 
  - 포인트는 배열 다음과 임시 유형 앞에 정렬됩니다.
  - 다른 CRS 포인트는 CRS 코드에서 정렬됩니다. (SRID 필드의 값). 현재 지원하는 설정 [좌표 참조 시스템](/syntax/spatial.md)에서 이것은 순서를 의미합니다: 4326, 4979, 7302, 9157
  - 같은 CRS의 포인트는 ```x```, ```y```, ```z``` 순서로 각 조합 값을 반환합니다.   
  - 이 순서는 공간 채우기 곡선 순서가 될 공간 인덱스에서 반환된 순서와는 다릅니다. 

- 시간 값 **비교** :

  - [순간 시간 값](/syntax/temporal.md)은 같은 유형 내에 비교할 수 있습니다. 인스턴스는 그 순간 전에 발생하면 다른 인스턴스보다 작은 것으로 간주되며, 반대의 경우는 큰 것으로 간주됩니다.  
  - 동일한 시점에 발생하지만 다른 시간대가 있는 값은 다른 값으로 간주하므로 예측 가능한 순서를 지정해야합니다. Cypher은 그 시간 대에 주요 순서 이후 서쪽에서(UTC로부터의 음수 오프셋) 동쪽으로(UTC로부터의 양수 오프셋)까지 유효 시간대 오프셋에 따라 값을 정렬하도록 규정합니다. 이는 같은 시간을 나타내는 지역 시간이 가장 이른 시간으로 정렬되는 효과가 있습니다. 두 인스턴스 값이 같은 시점을 나타내고 같은 시간대 간격 띄우기를 가지지만 시간대 이름이 다를 경우(*DateTime*만 해당), 값은 같지 않은 것으로 간주됩니다. 그렇기 때문에 세번째 순서 구성 요소는 시간대 식별자에 의해 알파벳 순으로 정렬됩니다. 
  - [*Duration*](/syntax/temporal.md) *day*, *month* 또는 *year* 값을 모른다면 이 길이는 알 수 없으므로 값을 비교할 수 없습니다. *Duration* 값은 비교할 수 없으므로, 두 *Duration* 사이에 비교 연산자를 넣으면 결과 값은 ```null```이 됩니다. 특정 시점, 시간, 오프셋 및 시간대 이름이 모두 같으면 값은 동일하며, 순서 차이는 확인할 수 없습니다.
 
- 시간 값 **순서** :

  - `ORDER BY`는 모든 값 모든 값을 정렬할 수 있어야합니다. 
  - 임시 인스턴스는 공간 인스턴스와 문자열 앞에 정렬됩니다.
  - 비교가능한 값은 그 비교 연산자와 동일한 순서대로 정렬되어야합니다.
  - 임시 인스턴스 값은 유형별로 정렬되며, 그 유형 내에 비교 순서로 정렬됩니다. 
  - *Duration* 값에 완전한 비교 순서를 정의 할 수 없으므로 ```ORDER BY``` 순서를 구체적으로 정의합니다.
    - *Duration* 값은 모든 요소를 해가 ```365.2425```일(```PT8765H49M12S```)이고, 모든 월이 ```30.436875```(한 해의 ```1/12```)일(```PT730H29M06S```)이고, 매일 ```24```시간이 되도록 표준화해서 정렬할 수 있습니다. 

- 한 요소가 ```null```일 때 순서를 비교 합니다. (즉, ```null < 3``` 은 ```null```)


#### 3.2.7.12. 비교 연산자 결합 

비교는 임의로 결합할 수 있습니다. (즉, ```x < y <= z```는 ```x < y AND y <= z```와 같습니다.)

만약 ```a, b, c, ..., y, z```이 표현이고, ```op1, op2, ..., opN```이 비교연산자라면 ```a op1 b op2 c ... y opN z```는  ```a op1 b and b op2 c and ... y opN z```와 같습니다. 
 
```a op1 b op2 c```는 ```a```와 ```c``` 사이의 비교를 나타내지 않습니다. 그래서 ```x < y > z``` 사용은 합법적 입니다. 

예시:

```
MATCH (n) WHERE 21 < n.age <= 30 RETURN n
```

이는 아래와 같습니다:

```
MATCH (n) WHERE 21 < n.age AND n.age <= 30 RETURN n
```

그러므로 이것은 21와 30살 사이의 모든 모드에 매치될 것 입니다.

이 구문은 모든 같거나 부등호를 비교할 뿐만 아니라 3이상 연결된 것까지 확장됩니다.

예시:

```
a < b = c <= d <> e
```

이는 아래와 같습니다:

```
a < b AND b = c AND c <= d AND d <> e
```

 