## 3.7. 실행계획

```
Cypher 쿼리 언어로 쿼리를 실행하기 위한 실행 계획의 일부로 사용되는 연산자에 대해 설명하려고 합니다.
```

Neo4j는 질의를 수행하는 작업을 연산자라고 하는 작은 부분으로 나눕니다. 각 연산자는 전체 쿼리의 작은 부분에 대한 책임이 있습니다. 연산자는 실행 계획이라는 패턴으로 함께 연결됩니다.

각 연산자에는 통계가 주석으로 표시됩니다.

```Rows```
연산자가 생성한 행 수입니다. 쿼리가 프로파일링 된 경우에만 사용할 수 있습니다.

```EstimatedRows```
Neo4j가 비용 기반 컴파일러를 사용했다면 연산자에 의해 생성될 예상 행 수가 표시됩니다. 컴파일러는 이 추정치를 사용하여 적절한 실행 계획을 선택합니다.

```DbHits```
각 운영자는 Neo4j 스토리지 엔진에 데이터 검색 또는 업데이트와 같은 작업을 수행하도록 요청합니다. 데이터베이스 히트는 이 스토리지 엔진 작업의 추상 단위입니다. 쿼리의 실행 계획을 보는 방법은 [3.6.2. "쿼리 프로파일링"](https://mossupport.github.io/developer-manual/cypher/query-tuning/how-do-i-profile-a-query.html)을 참조하세요.

각 운영자의 작동 방식에 대한 자세한 내용은 관련 섹션을 참조하십시오. 운영자는 상위 수준 범주로 그룹화됩니다. 쿼리가 실행되는 실제 데이터베이스의 통계는 사용된 계획을 결정한다는 것을 기억하십시오. 동일한 질의가 항상 동일한 계획으로 해결된다는 보장은 없습니다.

+ [출발점 연산자](https://mossupport.github.io/developer-manual/cypher/execution-plans/starting-point-operators.html)
+ [연산자 확장](https://mossupport.github.io/developer-manual/cypher/execution-plans/expand-operators.html)
+ [연산자 결합](https://mossupport.github.io/developer-manual/cypher/execution-plans/combining-operators.html)
+ [행 연산자](https://mossupport.github.io/developer-manual/cypher/execution-plans/row-operators.html)
+ [연산자 업데이트](https://mossupport.github.io/developer-manual/cypher/execution-plans/update-operators.html)
+ [최단 경로 계획](https://mossupport.github.io/developer-manual/cypher/execution-plans/shortestpath-planning.html)