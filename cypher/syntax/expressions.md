### 3.2.3. 표현 


- 일반적인 표현
- 문자열 참고사항
- `CASE` 표현
 
   - 간단한 ```CASE``` 양식 : 여러 값에서 표현식 비교 
   - 일반 ```CASE```양식 : 여러 조건을 표현할 수 있습니다.
   - 간단/ 일반적인 ```CASE``` 사용하는 시기 구분

#### 3.2.3.1. 일반적인 표현

Cypher 내 표현은 다음과 같이 쓸 수 있습니다:


- A decimal (integer or double) literal: `13`, `-40000`, `3.14`, `6.022E23`.


- 십진법(정수 또는 실수) 리터럴 : ```13```, ```-40000```, ```3.14```, ```6.022E23```. 
- 16진수 정수 리터럴(```0x```로 시작): ```0x13zf```, ```0xFC3A9```, ```-0x66eff```.
- 8진법 정수 리터럴(```0```으로 시작) : ```01372```, ```02127```, ```-05671```.
- 시작 리터럴: ```'Hello'```, ```"World"```.
- 논리 리터럴: ```true```, ```false```, ```TRUE```, ```FALSE```.
- 변수: ```n```, ```x```, ```rel```, ```myFancyVariable```, ````A name with weird stuff in it[]!````.
- 속성: ```n.prop```, ```x.prop```, ```rel.thisProperty```, ```myFancyVariable.'(weird property name)'```.
- 다양한 속성: ```n["prop"]```, ```rel[n.city + n.zip]```, ```map[coll[0]]```.
- 변수: ```$param```, ```$0```
- 리스트 표현: ```['a', 'b']```, ```[1, 2, 3]```, ```['a', 2, n.property, $param]```, ```[ ]```.
- 함수 호출: ```length(p)```, ```nodes(p)```.
- 합계 함수: ```avg(x.prop)```, ```count(*)```.
- 경로-패턴: ```(a)-->()<--(b)```.
- 연산 어플리케이션: ```1 + 2``` and ```3 < 4```.
- 술어 표현식은 true 또는 false를 리턴하는 표현식입니다: ```a.prop = 'Hello'```, ```length(p) > 10```,```exists(a.name)```.
- 정규 표현식: ```a.name =~ 'Tob.*'```
- 대소 문자를 구분하는 문자열과 일치하는 식 : ```a.surname STARTS WITH 'Sven'```, ```a.surname ENDS WITH 'son'``` 또는 ```a.surname CONTAINS 'son'```
- ```CASE``` 표현.


#### 3.2.3.2. 문자열 리터럴 참고사항 

문자열 리터럴은 다음 이스케이프 시퀀스를 포함할 수 있습니다. 

| 이스케이프 시퀀스 | 문자열                                                       |
| ----------------- | ------------------------------------------------------------ |
| `\t`              | 탭                                                           |
| `\b`              | 백 스페이스                                                  |
| `\n`              | 새 줄                                                        |
| `\r`              | 캐리지(Carriage) 반환                                        |
| `\f`              | 형식 피드                                                    |
| `\'`              | 작은 따음표                                                  |
| `\"`              | 큰 따음표                                                    |
| `\\`              | 역 슬래시                                                    |
| `\uxxxx`          | 유니 코드 UTF-16 코드 포인트 (4 자리 16 진수는 ```\ u```를 따라야 함) |
| `\Uxxxxxxxx`      | 유니 코드 UTF-32 코드 포인트 (8자리 16진수는  `\U`를 따라야 함) |

#### 3.2.3.3. ```CASE``` 표현 

일반적인 조건은 잘 알려진 ```CASE```을 이용해서 표현할 수 있습니다. Cypher 내 존재하는 ```CASE```의 두가지 유형: 단순 유형- 다수 값과 표현식을 비교합니다. 일반적 유형- 여러 조건문을 표현할 수 있습니다. 

그림 3.2. 그래프 

![alt](https://neo4j.com/docs/developer-manual/3.4/images/%60CASE%60%20expressions-1.svg)

##### 단일 ```CASE``` 유형: 다양한 값에 대하여 표현 비교 

표현은 ```WHEN```절 과 함께 일치 항목이 발견될 때까지 계산되고 비교됩니다. 일치하는 항목이 없을 경우, ```ELSE```절의 표현식이 반환됩니다. ```ELSE``` 케이스가 없고 일치하는 항목이 없을 경우 ```null```을 반환합니다. 


**구문론:**

```
CASE test
 WHEN value THEN result
  [WHEN ...]
  [ELSE default]
END
```

**변수:**

| 이름      | 설명                                                         |
| --------- | ------------------------------------------------------------ |
| `test`    | 가능한 표현식                                                |
| `value`   | `test`와 비교될 표현 결과                                    |
| `result`  | 만약 ```value```가 ```test```와 일치할 경우 이 표현을 출력합니다. |
| `default` | 일치하는 항목이 없을 경우, ```default```를 반환합니다.       |


**쿼리**

```
MATCH (n)
RETURN
CASE n.eyes
WHEN 'blue'
THEN 1
WHEN 'brown'
THEN 2
ELSE 3 END AS result
```

| result |
| ------ |
| 5 rows |
| `2`    |
| `1`    |
| `3`    |
| `2`    |
| `1`    |

##### Generic `CASE` form: allowing for multiple conditionals to be expressed

The predicates are evaluated in order until a `true` value is found, and the result value is used. If no match is found, the expression in the `ELSE` clause is returned. However, if there is no `ELSE` case and no match is found, `null` will be returned.

**Syntax:**

```
CASE
WHEN predicate THEN result
  [WHEN ...]
  [ELSE default]
END
```

**Arguments:**

| Name        | Description                                                  |
| ----------- | ------------------------------------------------------------ |
| `predicate` | A predicate that is tested to find a valid alternative.      |
| `result`    | This is the expression returned as output if `predicate` evaluates to `true`. |
| `default`   | If no match is found, `default` is returned.                 |

Query. 

```
MATCH (n)
RETURN
CASE
WHEN n.eyes = 'blue'
THEN 1
WHEN n.age < 40
THEN 2
ELSE 3 END AS result
```

| result |
| ------ |
| 5 rows |
| `2`    |
| `1`    |
| `3`    |
| `3`    |
| `1`    |

##### Distinguishing between when to use the simple and generic `CASE` forms

Owing to the close similarity between the syntax of the two forms, sometimes it may not be clear at the outset as to which form to use. We illustrate this scenario by means of the following query, in which there is an expectation that `age_10_years_ago` is `-1` if `n.age` is `null`:

Query. 

```
MATCH (n)
RETURN n.name,
CASE n.age
WHEN n.age IS NULL THEN -1
ELSE n.age - 10 END AS age_10_years_ago
```

However, as this query is written using the simple `CASE` form, instead of `age_10_years_ago` being `-1` for the node named `Daniel`, it is `null`. This is because a comparison is made between `n.age` and `n.age IS NULL`. As `n.age IS NULL` is a boolean value, and `n.age` is an integer value, the `WHEN n.age IS NULL THEN -1` branch is never taken. This results in the `ELSE n.age - 10` branch being taken instead, returning `null`.

| n.name      | age_10_years_ago |
| ----------- | ---------------- |
| 5 rows      |                  |
| `"Alice"`   | `28`             |
| `"Bob"`     | `15`             |
| `"Charlie"` | `43`             |
| `"Daniel"`  | `<null>`         |
| `"Eskil"`   | `31`             |

The corrected query, behaving as expected, is given by the following generic `CASE` form:

Query. 

```
MATCH (n)
RETURN n.name,
CASE
WHEN n.age IS NULL THEN -1
ELSE n.age - 10 END AS age_10_years_ago
```

We now see that the `age_10_years_ago` correctly returns `-1` for the node named `Daniel`.

| n.name      | age_10_years_ago |
| ----------- | ---------------- |
| 5 rows      |                  |
| `"Alice"`   | `28`             |
| `"Bob"`     | `15`             |
| `"Charlie"` | `43`             |
| `"Daniel"`  | `-1`             |
| `"Eskil"`   | `31`             |