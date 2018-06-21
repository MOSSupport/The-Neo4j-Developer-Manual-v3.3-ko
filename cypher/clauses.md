## 3.3. 절

#### 읽기 절

이 부분은 데이터베이스에서 데이터를 읽는 절로 구성됩니다.

Cypher 쿼리 내의 데이터 흐름은 키-값 쌍(쿼리의 변수와 데이터베이스에서 파생 된 값 사이의 가능한 바인딩 집합)을 가진 정렬되지 않은 일련의 맵입니다. 이 집합은 쿼리의 후속 부분에 의해 구체화되고 보강됩니다.

| 절                                                           | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [MATCH](https://neo4j.com/docs/developer-manual/current/cypher/clauses/match/) | 데이터베이스에서 검색 할 패턴을 지정.                        |
| [OPTIONAL MATCH](https://neo4j.com/docs/developer-manual/current/cypher/clauses/optional-match/) | 데이터베이스에서 검색 할 패턴을 지정하며 패턴에서 빠진 부분에 대해서는 nulls를 사용. |
| [WHERE](https://neo4j.com/docs/developer-manual/current/cypher/clauses/where/) | MATCH 또는 OPTIONAL MATCH 절의 패턴에 제약 조건을 추가하거나 WITH 절의 결과를 필터링. |
| [START](https://neo4j.com/docs/developer-manual/current/cypher/clauses/start/) | 레거시 인덱스를 통해 시작점을 발견함.                        |
| [Aggregation](https://neo4j.com/docs/developer-manual/current/cypher/clauses/aggregation/) | count(), sum(), avg(), max(), min(), collect() 등을 포함하는 집계 함수. DISTINCT 포함. |
| [LOAD CSV](https://neo4j.com/docs/developer-manual/current/cypher/clauses/load-csv/) | CSV 파일에서 데이터를 가져올 때 사용.                        |

#### 쓰기 절

이 부분은 데이터베이스에 데이터를 쓰는 절로 구성됩니다.

| 절                                                           | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [CREATE](https://neo4j.com/docs/developer-manual/current/cypher/clauses/create/) | 노드 및 관계를 생성.                                         |
| [MERGE](https://neo4j.com/docs/developer-manual/current/cypher/clauses/merge/) | 그래프에 패턴이 존재하는지 확인. 패턴이 이미 있는지 생성될 필요가 있는지 여부 |
| [SET](https://neo4j.com/docs/developer-manual/current/cypher/clauses/set/) | 노드의 레이블과 노드 및 관계의 속성을 업데이트.              |
| [DELETE](https://neo4j.com/docs/developer-manual/current/cypher/clauses/delete/) | 그래프 요소를 삭제. - 노드, 관계 또는 경로                   |
| [REMOVE](https://neo4j.com/docs/developer-manual/current/cypher/clauses/remove/) | 노드와 관계에서 속성과 레이블을 제거.                        |
| [FOREACH](https://neo4j.com/docs/developer-manual/current/cypher/clauses/foreach/) | 경로의 구성 요소든지 집계 결과든지 목록 내의 데이터를 업데이트. |
| [CREATE UNIQUE](https://neo4j.com/docs/developer-manual/current/cypher/clauses/create-unique/) | MATCH와 CREATE의 혼합. 가능한 것을 매칭하고 빠진 것을 생성.  |
| [Importing CSV files with Cypher](https://neo4j.com/docs/developer-manual/current/cypher/clauses/importing-csv-files-with-cypher/) | LOAD CSV를 사용하여 CSV 파일에서 데이터를 가져 오는 방법.    |
| [PERIODIC COMMIT](https://neo4j.com/docs/developer-manual/current/cypher/clauses/using-periodic-commit/) | PERIODIC COMMIT의 사용 방법 및 시기                          |

#### 일반 절

| 절                                                           | 설명                                                         |
| ------------------------------------------------------------ | ------------------------------------------------------------ |
| [RETURN](https://neo4j.com/docs/developer-manual/current/cypher/clauses/return/) | 쿼리 결과 집합에 포함 할 내용을 정의.                        |
| [ORDER BY](https://neo4j.com/docs/developer-manual/current/cypher/clauses/order-by/) | 출력을 특정 방식으로 정렬되도록 지정하는 RETURN 또는 WITH 뒤에 오는 하위 절. |
| [LIMIT](https://neo4j.com/docs/developer-manual/current/cypher/clauses/limit/) | 출력할 행 수를 제한.                                         |
| [SKIP](https://neo4j.com/docs/developer-manual/current/cypher/clauses/skip/) | 어느 행부터 출력의 행에 포함할 것인지 정의.                  |
| [WITH](https://neo4j.com/docs/developer-manual/current/cypher/clauses/with/) | 다음의 시작점 또는 기준으로 사용되도록 결과를 넘겨, 쿼리 파트가 함께 연결되게 함. |
| [UNWIND](https://neo4j.com/docs/developer-manual/current/cypher/clauses/unwind/) | 목록을 일련의 행으로 확장.                                   |
| [UNION](https://neo4j.com/docs/developer-manual/current/cypher/clauses/union/) | 여러 쿼리의 결과를 결합.                                     |
| [CALL](https://neo4j.com/docs/developer-manual/current/cypher/clauses/call/) | 데이터베이스에 배포 된 프로시저를 호출.                      |