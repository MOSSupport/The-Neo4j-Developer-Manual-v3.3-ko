
## 3.7. 실행계획

```
이 섹션에서는 Cypher 쿼리 언어로 쿼리를 실행하기 위한 실행 계획의 일부로 사용되는 연산자에 대해 다룹니다.
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




### 3.7.1. 실행 연산 플랜 확인

이 표는 사전 순으로 정렬된 모든 실행 연산 플랜으로 구성됩니다. 

+ 일반적으로 *Leaf* 연산자는 시작 노드와 쿼리를 실행할 때 필요한 관계를 위치시킵니다. 
+ *Updating* 연산자는 그래프를 업데이트하는 쿼리에서 쓰입니다. 


| 이름                                                         | 설명                                                         | Leaf? | 업데이트? |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ----- | --------- |
| [AllNodesScan](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-all-nodes-scan) | 노드 스토어의 모든 노드를 읽어옵니다.                        | Y     |           |
| [AntiConditionalApply](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-anti-conditional-apply) | 중첩 루프를 실행합니다. 변수가 ```null```이라면, 오른쪽이 실행됩니다. |       |           |
| [AntiSemiApply](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-anti-semi-apply) | 중첩 루프를 실행합니다. 패턴 서술부 부재를 검사합니다.       |       |           |
| [Apply](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-apply) | 중첩 루프를 실행합니다. 왼쪽/오른쪽 연산자의 행을 모두 나타냅니다. |       |           |
| [Argument](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-argument) | 변수가 ```Apply``` 연산자의 오른쪽에서 명령어로 쓰이는 것을 나타냅니다. | Y     |           |
| [AssertSameNode](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-assert-same-node) | 고유 제한 조건을 위반하지 않도록 보장할 때 사용합니다.       |       |           |
| [CartesianProduct](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-cartesian-product) | 입력 값의 왼쪽 및 오른쪽 연산자 데카르트 곱을 생성합니다.    |       |           |
| [ConditionalApply](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-conditional-apply) | 중첩 루프를 실행합니다. 변수가 ```null```이라면, 오른쪽을 실행합니다. |       |           |
| [CreateIndex](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-create-index) | 특정 라벨을 가진 모든 노드의 인덱스를 생성합니다.            | Y     | Y         |
| [CreateNode](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-create-node) | 노드를 생성합니다.                                           | Y     | Y         |
| [CreateNodeKeyConstraint](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-create-node-key-constraint) | 특정 라벨을 가진 모든 노드 특성 집합에서 노드 키를 생성합니다. | Y     | Y         |
| [CreateNodePropertyExistenceConstraint](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-create-node-property-existence-constraint) | 특정 라벨을 가진 모든 노드의 제약 조건을 생성합니다.         | Y     | Y         |
| [CreateRelationship](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-create-relationship) | 릴레이션(relationship)을 생성합니다.                         |       | Y         |
| [CreateRelationshipPropertyExistenceConstraint](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-create-relationship-property-existence-constraint) | 특정 타입의 모든 관계에 존재 제약조건을 생성합니다.          | Y     | Y         |
| [CreateUniqueConstraint](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-create-unique-constraint) | 특정 라벨을 가진 모든 노드에서 고유한 제약 조건을 생성합니다. | Y     | Y         |
| [Delete](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-delete) | 관계나 노드를 제거합니다.                                    |       | Y         |
| [DetachDelete](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-detach-delete) | 자체 노드와 관계를 제거합니다.                               |       | Y         |
| [DirectedRelationshipByIdSeek](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-directed-relationship-by-id-seek) | 관계 저장소에서 한 개 이상의 관계를 읽습니다.                | Y     |           |
| [Distinct](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-distinct) | 들어오는 행 스트림에서 중복 행을 제거합니다.                 |       |           |
| [DropIndex](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-drop-index) | 특정 라벨을 가진 모든 노드에서 인덱스 특성을 제거합니다.     | Y     | Y         |
| [DropNodeKeyConstraint](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-drop-node-key-constraint) | 특정 라벨을 가진 모든 노드의 특성 집합에서 노드 키를 제거합니다. | Y     | Y         |
| [DropNodePropertyExistenceConstraint](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-drop-node-property-existence-constraint) | 특정 라벨을 가진 모든 노드의 특성 집합에서 존재 제약을 제거합니다. | Y     | Y         |
| [DropRelationshipPropertyExistenceConstraint](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-drop-relationship-property-existence-constraint) | 특정 유형의 모든 관계 속성에서 존재 제한 조건을 제거합니다.  | Y     | Y         |
| [DropResult](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-drop-result) | 빈 결과를 출력할 때, 0개의 행을 생성합니다.                  |       |           |
| [DropUniqueConstraint](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-drop-unique-constraint) | 특정 유형의 모든 관계 속성에서 고유 조건을 제거합니다.       | Y     | Y         |
| [Eager](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-eager) | 격리를 위해, 작업에 영향을주는 일은 전체 데이터 세트가 완전히 실행 된 후 계속됩니다. |       |           |
| [EagerAggregation](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-eager-aggregation) | 그룹 표현 식을 평가합니다.                                   |       |           |
| [EmptyResult](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-empty-result) | 들어오는 모든 데이터를 로딩/ 처분합니다.                     |       |           |
| [EmptyRow](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-empty-row) | 열이 없는 단일 행을 반환합니다.                              | Y     |           |
| [Expand(All)](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-expand-all) | 지정된 노드에서 수신/발신 관계를 트래버스(traverse) 합니다.  |       |           |
| [Expand(Into)](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-expand-into) | 두 노드 사이의 모든 관계를 찾습니다.                         |       |           |
| [Filter](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-filter) | 조건 참인 행을 통과하는 하위 연산자 각 행을 필터링합니다.    |       |           |
| [Foreach](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-foreach) | 중첩 루프를 실행합니다. 연산자 왼쪽 행에서 생성하고 오른 쪽 연산자 행을 제거합니다. |       |           |
| [LetAntiSemiApply](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-let-anti-semi-apply) | 중첩 루프를 실행합니다. 여러 패턴 술어가 있는 곳에서 패턴 술어 부재를 테스트합니다. |       |           |
| [LetSelectOrSemiApply](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-let-select-or-semi-apply) | 중첩 루프를 실행합니다. 다른 술어와 결합한 패턴 술어 존재를 확인합니다. |       |           |
| [LetSelectOrAntiSemiApply](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-let-select-or-anti-semi-apply) | 중첩 루프를 실행합니다. 다른 술어와 결합한 패턴 술어 존재를 확인합니다. |       |           |
| [LetSemiApply](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-let-semi-apply) | 중첩 루프를 실행합니다. 쿼리에서 다양한 패턴 술어를 포함하는 패턴 조재를 확인합니다. |       |           |
| [Limit](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-limit) | 들어오는 입력 값에서 첫 `n`행을 반환합니다.                  |       |           |
| [LoadCSV](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-load-csv) | CSV 소스를 쿼리로 로드 합니다.                               | Y     |           |
| [LockNodes](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-lock-nodes) | 릴레이션을 생성할 때 시작/끝 노드를 잠가둡니다.              |       |           |
| [MergeCreateNode](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-merge-create-node) | 노드 찾기를 실패할 때 노드를 생성합니다.                     | Y     | Y         |
| [MergeCreateRelationship](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-merge-create-relationship) | 릴레이션 찾기를 실패할 때 릴레이션을 생성합니다.             |       | Y         |
| [NodeByIdSeek](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-node-by-id-seek) | 노드 저장소에서 id로 한개 이상의 노드를 읽습니다.            | Y     |           |
| [NodeByLabelScan](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-node-by-label-scan) | 노드 라벨 인덱스에서 모든 노드를 특정 라벨로 가져옵니다.     | Y     |           |
| [NodeCountFromCountStore](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-node-count-from-count-store) | 노드 카운트 관련 질문에 답하기 위해 카운트 저장소를 사용합니다. | Y     |           |
| [NodeHashJoin](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-node-hash-join) | 노드 ids의 해시 결합을 실행합니다.                           |       |           |
| [NodeIndexContainsScan](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-node-index-contains-scan) | 인덱스에 저장된 모든 값을 검사하여 특정 문자열을 포함하는 항목을 검색합니다. | Y     |           |
| [NodeIndexEndsWithScan](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-node-index-ends-with-scan) | 인덱스에 저장된 모든 값을 검사하여, 특정 문자열로 끝나는 값을 검색합니다. | Y     |           |
| [NodeIndexScan](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-node-index-scan) | 인덱스에 저장된 모든 값을 검사하여, 특정 값을 가진 모든 노드에 특정 라벨을 포함해서 반환합니다. | Y     |           |
| [NodeIndexSeek](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-node-index-seek) | 색인 검색을 이용해서 노드를 찾습니다.                        | Y     |           |
| [NodeIndexSeekByRange](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-node-index-seek-by-range) | 속성 값이 주어진 접두사 문자열과 일치할 때, 인덱스를 사용하여 노드를 찾습니다. | Y     |           |
| [NodeLeftOuterHashJoin](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-node-left-right-outer-hash-join) | 왼쪽 외부 해시 결합을 실행합니다.                            |       |           |
| [NodeRightOuterHashJoin](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-node-left-right-outer-hash-join) | 오른쪽 외부 해시 결합을 실행합니다.                          |       |           |
| [NodeUniqueIndexSeek](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-node-unique-index-seek) | 유일한 인덱스 내에 인덱스 검색을 이용해서 노드를 찾습니다.   | Y     |           |
| [NodeUniqueIndexSeekByRange](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-node-unique-index-seek-by-range) | 속성 값이 있는 접두사 문자열과 동일한 고유 색인에서 색인 탐색을 사용하는 노드를 찾습니다. | Y     |           |
| [Optional](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-optional) | 소스에서 데이터를 반환하지 않는 경우 모든 열을 ```null```로 설정하여 단일 행을 나타냅니다. |       |           |
| [OptionalExpand(All)](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-optional-expand-all) | 단일 행을 관계와  술부가 채워지지 않았을 때, 끝 노드를 ```null```로 설정하여 주어진 노드에서 관계를 트래버스합니다. |       |           |
| [OptionalExpand(Into)](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-optional-expand-into) | 두 노드 사이의 모든 관계를 탐색하여 관계가있는 단일 행을 생성하고 일치하는 관계가 없으면 끝 노드를 ```null```로 설정합니다 (시작 노드는 가장 작은 단위 노드입니다). |       |           |
| [ProcedureCall](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-procedure-call) | 프로시져를 호출합니다.                                       |       |           |
| [ProduceResults](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-produce-results) | 사용자가 결과를 사용할 수 있도록 준비합니다.                 |       |           |
| [ProjectEndpoints](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-project-endpoints) | 관계의 시작 및 끝 노드를 투영합니다.                         |       |           |
| [Projection](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-projection) | 결과 행을 생성하면서 그것의 표현 집합을 평가합니다.          | Y     |           |
| [RelationshipCountFromCountStore](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-relationship-count-from-count-store) | 카운트 저장소를 사용해서 관계 수 질문에 답합니다.            | Y     |           |
| [RemoveLabels](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-remove-labels) | 노드에서 레이블을 삭제합니다.                                |       | Y         |
| [RollUpApply](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-roll-up-apply) | 중첩 루프를 실행합니다. 패턴 표현이나 패턴 이해를 실행합니다. |       |           |
| [SelectOrAntiSemiApply](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-select-or-anti-semi-apply) | 중첩 루프를 실행합니다. 표현식 술어가 ```false```로 평가되면 패턴 술어가 없음을 테스트합니다. |       |           |
| [SelectOrSemiApply](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-select-or-semi-apply) | 중첩 루프를 실행합니다. 표현식 술어가 ```false```로 평가되면 패턴 술어가 없음을 테스트합니다. |       |           |
| [SemiApply](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-semi-apply) | 중첩 루프를 실행합니다.  패턴 술어의 존재 여부를 테스트합니다. |       |           |
| [SetLabels](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-set-labels) | 노드의 라벨을 설정합니다.                                    |       | Y         |
| [SetNodePropertyFromMap](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-set-node-property-from-map) | 맵의 노드에서 속성을 설정합니다.                             |       | Y         |
| [SetProperty](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-set-property) | 노드나 관계에서 특성을 설정합니다.                           |       | Y         |
| [SetRelationshipPropertyFromMap](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-set-relationship-property-from-map) | 맵의 노드에서 속성을 설정합니다.                             |       | Y         |
| [Skip](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-skip) | 들어오는 행의 `n`행을 스킵합니다.                            |       |           |
| [Sort](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-sort) | 주어진 키로 행을 정렬합니다.                                 |       |           |
| [Top](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-top) | 주어진 키로 정렬된 첫 `n`행을 반환합니다.                    |       |           |
| [TriadicSelection](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-triadic-selection) | 일반적인 '현재 친구가 아닌 친구 찾기'와 같은 삼자 쿼리를 해결합니다. |       |           |
| [UndirectedRelationshipByIdSeek](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-undirected-relationship-by-id-seek) | 관계 저장소 아이디에서 한 개 이상의 관계를 읽어옵니다.       | Y     |           |
| [Union](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-union) | 결과의 오른쪽 연산자를 왼쪽 연산자와 결합시킵니다.           |       |           |
| [Unwind](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-unwind) | 리스트 아이템 별로 한 가지 행을 반환합니다.                  |       |           |
| [ValueHashJoin](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-value-hash-join) | 임의 값에서 해시 결합을 실행합니다.                          |       |           |
| [VarLengthExpand(All)](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-varlength-expand-all) | 주어진 노드에서 변수-길이 관계를 트래버스합니다.             |       |           |
| [VarLengthExpand(Into)](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-varlength-expand-into) | 두 가지 노드 사이에서 모든 변수-길이 관계를 찾습니다.        |       |           |
| [VarLengthExpand(Pruning)](https://neo4j.com/docs/developer-manual/3.4/cypher/execution-plans/operators/#query-plan-varlength-expand-pruning) | 주어진 노드에서 변수-길이 관계를 트래버스해서 끝 노드의 고유한 값만 반환합니다. |       |           |
