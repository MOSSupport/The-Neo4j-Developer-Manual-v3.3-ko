### 3.2.11. Maps

```
Cypher는 Map을 확실하게 지원합니다.
```

+ Maps 리터럴
+ Map 투영
	+ Map 투영 예시 
 
아래 그래프는 다음 예에서 쓰입니다.

그림 3.5. 그래프

![alt](https://neo4j.com/docs/developer-manual/3.3/images/Maps-1.svg)


```.``` and `[]` 와 같은 속성 억세스 연산자는 [이곳](./operators.md)에서 확인할 수 있습니다. ```null```과 관련된 ```[]``` 연산자와 관련된 내용은 [이곳](./working-with-null.md)에서 확인할 수 있습니다. 

#### 3.2.11.1. 리터럴 maps

Cypher에서 maps도 생성할 수 있습니다. REST를 통해서 JSON 객체를 얻습니다; Java에서 이들은 ```java.util.Map<String,Object>```가 됩니다. 

**쿼리**

```
RETURN { key: 'Value', listKey: [{ inner: 'Map1' }, { inner: 'Map2' }]}
```

| { key: 'Value', listKey: [{ inner: 'Map1' }, { inner: 'Map2' }]} |
| ------------------------------------------------------------ |
| 1 row                                                        |
| `{listKey -> [{inner -> "Map1"},{inner -> "Map2"}], key -> "Value"}` |

#### 3.2.11.2. Map 투영

Cypher는 "map 투영"이라고 불리는 개념을 지원합니다. 이것은 노드에서 map 투영 생성, 관계 및 다른 amp 값을 쉽게 생성하도록 합니다.

map 투영은 투영될 그래프 속성과 관련된 변수로 시작하고 ```{``` 및 ```}```로 묶인 쉼표로 구분된 map 요소 본문을 포함합니다.  

```map_variable {map_element, [, …n]}```
 
map 요소는 한개 이상의 키-값 쌍을 map에 투영합니다. 여기에는 4가지 종류의 map 투영 요소가 있습니다: 

+ 프로퍼티 선택장치 - 프로퍼티 이름을 키로 투영하고, ```map_variable``` 값을 투영합니다.
+ 문자열 엔트리(entry) - 이것은 키-값 쌍으로, `key: <expression>` 값을 임의로 표현합니다. 
+ 변수 선택자 - 변수 이름을 키로 사용하여 변수를 투영하며 변수가 가리키는 값을 투사합니다. 구문은 변수 일뿐입니다.
+ 모든-속성 선택자 - ```map_variable```값에서 모든 키-값 쌍을 투영합니다. 

```map_variable```가 ```null```값을 가르킨다면, 모든 map 투영이 ```null``` 값을 평가할 것 입니다.  

##### map 투영 예시

**Charlie Sheen**을 찾고 그와 관련된 데이터 및 그가 연기했던 영화를 반환합니다. 이 예시는 리터널 엔트리가 있는 map을 투영한 예를 나타내며, 또한 ```collect()```내에 map 투영법을 사용합니다.  
 
**쿼리**

```
MATCH (actor:Person { name: 'Charlie Sheen' })-[:ACTED_IN]->(movie:Movie)
RETURN actor { .name, .realName, movies: collect(movie { .title, .year })}
```

| actor                                                        |
| ------------------------------------------------------------ |
| 1 row                                                        |
| `{name -> "Charlie Sheen", movies -> [{title -> "Apocalypse Now", year -> 1979},{title -> "Red Dawn", year -> 1984},{title -> "Wall Street", year -> 1987}], realName -> "Carlos Irwin Estévez"}` |

영화에 출현한 모든 사람을 찾고 각 사람 번호를 나타냅니다. 이 예시는 변수를 번호로 나타내고, 값을 투영하기 위해서 변수 선택자를 사용합니다. 

**쿼리**

```
MATCH (actor:Person)-[:ACTED_IN]->(movie:Movie)
WITH actor, count(movie) AS nrOfMovies
RETURN actor { .name, nrOfMovies }
```

| actor                                        |
| -------------------------------------------- |
| 2 rows                                       |
| `{name -> "Martin Sheen", nrOfMovies -> 2}`  |
| `{name -> "Charlie Sheen", nrOfMovies -> 3}` |

다시, **Charlie Sheen**에 집중해서 다른 노드에서 모든 속성 값을 반환합니다. 여기서 모든-속성 선택자를 사용해서 모든 노드 속성을 투영하고, 추가적으로 ```age```속성을 명시적으로 투영합니다. 

**쿼리**

```
MATCH (actor:Person { name: 'Charlie Sheen' })
RETURN actor { .*, .age }
```

| actor                                                        |
| ------------------------------------------------------------ |
| 1 row                                                        |
| `{name -> "Charlie Sheen", realName -> "Carlos Irwin Estévez", age -> <null>}` |