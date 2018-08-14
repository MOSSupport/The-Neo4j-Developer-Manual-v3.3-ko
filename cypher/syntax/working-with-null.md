### 3.2.12. ```null```로 작업

- Cypher에서 ```null``` 도입 
- ```null```와 논리 연산자 
- ```IN``` 연산자 및 ```null```
- ```[]``` 연산자 및 ```null```
- ```null```을 반환하는 표현 

#### 3.2.12.1. Cypher에서 ```null``` 도입

Cypher에서 ```null```은 없어지거나 정의되지 않은 값을 나타냅니다. 개념적으로, ```null```은 `알려지지 않은 누락된 값`을 의미하고 다른 값과 다르게 처리합니다. 예를 들어서, 속성이 없는 노드에서 속성을 얻을 때 ```null```을 생성합니다. 대부분 ```null```을 입력으로하는 표현은 ```null```을 생산할 것 입니다. 이것은 ```WHERE``` 절에서 술부로 사용되며 논리 표현을 포합합니다. 이 경우, ```true```가 아닌 값은 거짓을 의미합니다. 

```null```은 ```null``` 값과 같지 않습니다. 두 값을 모르는 경우 이들이 같은 값이라는 것을 의미하지는 않습니다. 따라서 표현 ```null```=```null```은 ```true```가 아닌 ```null```을 출력합니다. 

#### 3.2.12.2. ```null```와 논리 연산자 

논리연산자(`AND`, `OR`, `XOR`, `NOT`)은 ```null```을 세 가지 논리 값 중 `unknown`으로 취급합니다. 

아래는 `AND`, `OR`, `XOR` 및 `NOT`의 진리표 입니다.

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

#### 3.2.12.3. ```IN``` 연산자 및 ```null```

```IN``` 연산자는 비슷한 로직을 따릅니다. Cypher가 리스트에 무언가 존재하는 것을 알고 있을 경우 결과는 ```true```일 것 입니다. ```null```을 포함하는 리스트에서 매치되는 요소가 없는 것은 ```null```을 반환합니다. 그렇지 않으면, 결과는 ```false```일 것 입니다. 


| 표현               | 결과  |
| ------------------------ | ------- |
| 2 IN [1, 2, 3]           | `true`  |
| 2 IN [1, `null`, 3]      | `null`  |
| 2 IN [1, 2, `null`]      | `true`  |
| 2 IN [1]                 | `false` |
| 2 IN []                  | `false` |
| `null` IN [1, 2, 3]      | `null`  |
| `null` IN [1, `null`, 3] | `null`  |
| `null` IN []             | `false` |


`all`, `any`, `none`, 및 `single`도 비슷한 규칙을 따릅니다. 만약 결과 계산이 정확하다면 ```true``` 또는 ```false```가 반환될 것 입니다. 그렇지 않으면 ```null```이 생성됩니다. 

#### 3.2.12.4. ```[]``` 연산자 및 ```null```

리스트 또는 map을 ```null```로 평가하면 ```null``` 결과 값을 가집니다. 

| 표현              | 결과 |
| ----------------------- | ------ |
| [1, 2, 3][`null`]       | `null` |
| [1, 2, 3, 4][`null`..2] | `null` |
| [1, 2, 3][1..`null`]    | `null` |
| {age: 25}[`null`]       | `null` |

매개 변수를 사용하여 ```a [$ lower .. $ upper]```와 같은 바운드를 전달하면 하위 및 상위 바운드 (또는 둘 다)에 대해 ```null```결과를 가질 수도 있습니다. 아래 해결책은 완전한 최소 및 최대 한계 값을 설정하여 이 현상을 예방합니다. 

```
a[coalesce($lower,0)..coalesce($upper,size(a))]
```

#### 3.2.12.5. ```null```을 반환하는 표현

- 리스트에서 누락된 요소 얻기: `[][0]`, `head([])`
- 노드 또는 관계에서 존재하지 않는 속성에 억세스 : ```n.missingProperty```
- 양쪽이 ```null```일 때의 비교 : ```1 <null``` 
- ```null```을 포함하는 산술식 : ```1 + null``` 
- 인수가 ```null```인 함수 호출 : ```sin (null)```


