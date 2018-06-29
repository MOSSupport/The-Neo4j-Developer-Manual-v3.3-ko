
## 3.3. 절

```
이 섹션에서는 Cypher 쿼리 언어 절과 관련된 모든 정보를 다룹니다. 
```

+ 읽기 절
+ 투영 절
+ 읽기 서브-절
+ 쓰기 절
+ 읽기/쓰기 절
+ 조작 설정
+ 데이터 임포트
+ 스키마 절

#### 읽기 절

이 절은 데이터베이스에서 데이터를 읽는 절로 구성됩니다.

Cypher 쿼리 내의 데이터 흐름은 키-값 쌍(쿼리의 변수와 데이터베이스에서 파생 된 값 사이의 가능한 바인딩 집합)을 가진 정렬되지 않은 일련의 맵입니다. 이 집합은 다음 쿼리 일부에서 구체화되고 보강됩니다.

| 절                                                           | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [MATCH](https://neo4j.com/docs/developer-manual/current/cypher/clauses/match/) | 데이터베이스에서 검색 할 패턴을 지정.                        |
| [OPTIONAL MATCH](https://neo4j.com/docs/developer-manual/3.4/cypher/clauses/merge/#query-merge-on-create-on-match) | 데이터베이스에서 검색 할 패턴을 지정하며 패턴에서 빠진 부분에 대해서는 nulls를 사용. |
| [START](https://neo4j.com/docs/developer-manual/current/cypher/clauses/start/) | 레거시 인덱스를 통해 시작점을 발견함.                        |


#### 투영 절 

이것은 결과 집합에서 반환할 식을 정의하는 절을 구성합니다. 반환된 표현은 ```AS```을 사용해서 지정할 수 있습니다. 

| 절                                                           | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [RETURN … [AS\]](https://neo4j.com/docs/developer-manual/3.4/cypher/clauses/return/) | 쿼리 결과 설정에서 추가할 것을 정의합니다.                   |
| [WITH … [AS\]](https://neo4j.com/docs/developer-manual/3.4/cypher/clauses/with/) | 쿼리 부분을 함께 연결하여, 시작 점이나 다음 기준으로 사용될 결과를 파이핑할 수 있습니다. |
| [UNWIND … [AS\]](https://neo4j.com/docs/developer-manual/3.4/cypher/clauses/unwind/) | 리스트를 일련의 행으로 확장합니다.                           |


#### 읽기 하위-절

이것은 읽기 절 일부로 작동하는 하위-절을 포함합니다. 

| 하위-절                                                      | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [WHERE](https://neo4j.com/docs/developer-manual/3.4/cypher/clauses/where/) | `MATCH` 또는 `OPTIONAL MATCH`  절 및 `WITH` 절 안의 패턴에 한계를 추가하여 결과를 필터링 합니다. |
| [ORDER BY [ASC[ENDING\] \| DESC[ENDING]]](https://neo4j.com/docs/developer-manual/3.4/cypher/clauses/order-by/) | ```RETURN``` 또는 ```WITH``` 뒤 하위 절이  오름차순 (기본값) 또는 내림차순으로 출력하도록 지정합니다. |
| [SKIP](https://neo4j.com/docs/developer-manual/3.4/cypher/clauses/skip/) | 결과의 행을 포함하여 시작할 행을 정의합니다.                 |
| [LIMIT](https://neo4j.com/docs/developer-manual/3.4/cypher/clauses/limit/) | 결과 내 행 개수를 제한합니다.                                |

#### 읽기 힌트
 
이것은 쿼리를 튜닝 할 때 플래너 힌트를 지정하는 데 사용되는 절을 포함합니다. 일반적인 쿼리 튜닝과 관련된 내용은 [섹션 3.6.4, “플래너 힌트 및 키워드 사용”](./query-tuning/using.md)에서 확인할 수 있습니다. 

| 힌트                                                         | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [USING INDEX](https://neo4j.com/docs/developer-manual/3.4/cypher/query-tuning/using/#query-using-index-hint) | 인덱스 힌트는 플래너가 시작 점으로 사용할 인덱스를 지정할 때 사용됩니다. |
| [USING SCAN](https://neo4j.com/docs/developer-manual/3.4/cypher/query-tuning/using/#query-using-scan-hint) | 검색 힌트는 인덱스를 사용하는 대신 플래너가 (필터링 작업 후) 레이블을 스캔하도록 강제 실행할 때 사용됩니다. |
| [USING JOIN](https://neo4j.com/docs/developer-manual/3.4/cypher/query-tuning/using/#query-using-join-hint) | 결합 힌트는 특정 지점에서 결합 연산을 사용하도록할 때 쓰입니다. |

 
#### 쓰기 절 
 
이 부분은 데이터베이스에 데이터를 쓰는 절로 구성됩니다.

| 절                                                           | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [CREATE](https://neo4j.com/docs/developer-manual/current/cypher/clauses/create/) | 노드 및 관계를 생성합니다.                                   |
| [DELETE](https://neo4j.com/docs/developer-manual/current/cypher/clauses/delete/) | 그래프 요소를 삭제. - 노드, 관계 또는 경로. 삭제할 노드에는 삭제 된 모든 관련 관계를 명시해야 합니다. |
| [DETACH DELETE](https://neo4j.com/docs/developer-manual/3.4/cypher/clauses/delete/) | 노드 또는 노드 집합을 제거합니다. 관련된 모든 노드는 자동으로 제거됩니다. |
| [SET](https://neo4j.com/docs/developer-manual/current/cypher/clauses/set/) | 노드의 레이블과 노드 및 관계의 속성을 업데이트합니다.        |
| [REMOVE](https://neo4j.com/docs/developer-manual/current/cypher/clauses/remove/) | 노드와 관계에서 속성과 레이블을 제거합니다.                  |
| [FOREACH](https://neo4j.com/docs/developer-manual/current/cypher/clauses/foreach/) | 경로의 구성 요소든지 집계 결과든지 목록 내의 데이터를 업데이트. |


#### 읽기/쓰기 절

이 구절은 데이터베이스에서 데이터를 읽거나 쓸 때 사용하는 것으로 구성됩니다.

| 절                                                           | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [MERGE](https://neo4j.com/docs/developer-manual/3.4/cypher/clauses/merge/) | 그래프에 패턴이 이미 존재하거나, 생성되어야 하는지  확인합니다. |
| --- [ON CREATE](https://neo4j.com/docs/developer-manual/3.4/cypher/clauses/merge/#query-merge-on-create-on-match) | ```MERGE```와 함께 사용되는 ```write``` 하위 절은 패턴을 작성할 때 수행할 작업을 지정합니다. |
| --- [ON MATCH](https://neo4j.com/docs/developer-manual/3.4/cypher/clauses/merge/#query-merge-on-create-on-match) | ```MERGE```와 함께 사용되는 ```write``` 하위 절은 패턴이 존재하는 경우 수행할 작업을 지정합니다. |
| [CALL […YIELD\]](https://neo4j.com/docs/developer-manual/3.4/cypher/clauses/call/) | 데이터베이스에서 사용되는 프로시저를 호출하고 결과를 리턴합니다. |
| [CREATE UNIQUE](https://neo4j.com/docs/developer-manual/3.4/cypher/clauses/create-unique/) | ```MATCH```와 ```CREATE```를 섞어서 일치시키고 누락 된 것은 생성합니다. |

#### 조작 설정

| 절                                                           | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [UNION](https://neo4j.com/docs/developer-manual/3.4/cypher/clauses/union/) | 많은 쿼리 결과를 단일 결과 집합으로 결합합니다. 중복은 제거합니다. |
| [UNION ALL](https://neo4j.com/docs/developer-manual/3.4/cypher/clauses/union/) | 많은 쿼리 결과를 단일 결과 집합으로 결합합니다. 중복은 유지합니다. |

#### 데이터 임포트

| 절                                                           | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [LOAD CSV](https://neo4j.com/docs/developer-manual/3.4/cypher/clauses/load-csv/) | CSV파일에서 데이터를 임포트할 때  사용합니다.                |
| --- [USING PERIODIC COMMIT](https://neo4j.com/docs/developer-manual/3.4/cypher/query-tuning/using/#query-using-periodic-commit-hint) | 이 쿼리 힌트는 ```LOAD CSV```를 사용하여 많은 양의 데이터를 임포트할 때 메모리 부족 오류가 발생하지 않게할 때 사용합니다. |


#### 스키마 절

이것은 스키마를 관리할 때 사용되는 절 입니다; 자세한 내용은 [섹션 3.5, 스키마](./schema.md)을 참조하면 됩니다. 

| 절                                                           | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [CREATE \| DROP CONSTRAINT](https://neo4j.com/docs/developer-manual/3.4/cypher/schema/constraints/) | 특정 라벨 및 속성을 사용하여 모든 노드의 인덱스 생성/제거합니다. |
| [CREATE \| DROP INDEX](https://neo4j.com/docs/developer-manual/3.4/cypher/schema/index/) | Create or drop a constraint pertaining to either a node label or relationship type, and a property.  노드 라벨, 관계 타입 및 속성에 존재하는 제약을 생성/제거합니다. |