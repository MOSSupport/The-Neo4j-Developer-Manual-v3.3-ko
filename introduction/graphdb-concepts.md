# 1.2. 그래프 데이터베이스 개념

```
이 챕터에서는 그래프 데이터 모델 관련 내용을 다룹니다.
```

### 1.2.1. Neo4j 그래프 데이터베이스

그래프 데이터베이스는 가장 일반적인 데이터 그래프 구조로 데이터를 저장하며, 모든 종류의 데이터를 쉽게 액세스할 수 있는 방식으로 표현할 수 있습니다. Neo4j그래프는 특성 그래프모델(Property Graph Model)을 기반으로 합니다.

그래프 데이터베이스 용어에 대해서는 부록 B, [Terminology](https://neo4j.com/docs/developer-manual/current/terminology/)를 참고하세요.

단계별로 접근하는 예제그래프:

![이미지제목](https://mossupport.github.io/developer-manual/introduction/img/graphdb-simple-labels.svg)

#### 1.2.1.1. 노드

```
Neo4j의 노드는 [속성(properties)](https://mossupport.github.io/developer-manual/introduction/graphdb-concepts.html#1213-%EC%86%8D%EC%84%B1) 및 [레이블(labels)](https://mossupport.github.io/developer-manual/introduction/graphdb-concepts.html#1214-%EB%A0%88%EC%9D%B4%EB%B8%94)이 있는 [속성 그래프(property graph model)](https://github.com/opencypher/openCypher/blob/master/docs/property-graph-model.adoc#pgm-definitions-node)에 설명된 노드입니다.
```

노드는 엔터티를 나타낼 때 주로 사용되지만, 도메인에 따라 다른 용도로 사용될 수도 있습니다.

가장 단순한 그래프는 단일 노드입니다. 단일 속성 ```title```을 가지고 하나의 노드로 구성된 아래 그래프를 참고하세요.

![이미지제목](https://mossupport.github.io/developer-manual/introduction/img/graphdb-simple-node.svg)

앞 예제의 노드에 두 개의 노드와 하나의 속성을 추가하면, 아래와 같습니다.

![이미지제목](https://mossupport.github.io/developer-manual/introduction/img/graphdb-simple-nodes.svg)

#### 1.2.1.2. 관계

```
Neo4j의 관계는 관계 유형으로 [특성 그래프 모델(property graph model)](https://github.com/opencypher/openCypher/blob/master/docs/property-graph-model.adoc#pgm-definitions-relationship)에 설명 된 특성(properties)입니다.
```

그래프 데이터베이스에서 노드간 관계는 연관데이터를 찾을 수 있는 핵심 기능입니다. 관계는 두 개의 노드를 연결하며 유효한 소스 및 대상 노드를 포함합니다.

관계는 노드를 임의 구조로 구성되어 그래프를 목록, 트리,지도 또는 복합 엔티티와 유사하게 만들 수 있습니다. 이 중 하나는 더 복잡하고 많이 연결된 구조로 결합할 수 있습니다.

예제 그래프는 관계를 추가하면 더 이해하기 쉽습니다.

![이미지제목](https://mossupport.github.io/developer-manual/introduction/img/graphdb-simple-rels.svg)

이 예에서는 관계 유형으로 ```ACTED_IN```와 ```DIRECTED```을 사용합니다. ```ACTED_IN``` 관계의 ```roles``` 등록 정보에는 하나의 항목이 있는 배열이 있습니다.

아래는 ```Tom Hanks``` 노드를 소스 노드로, ```Forrest Gump```를 대상 노드로 사용하는 ```ACTED_IN``` 관계입니다. 

![이미지제목](https://mossupport.github.io/developer-manual/introduction/img/graphdb-nodes-and-rel.svg)

```Tom Hanks``` 노드는 나가는 관계를 가지는 반면, ```Forrest Gump``` 노드는 들어오는 관계가 있음을 알 수 있습니다.

관계는 어느 방향으로든 동등하게 통과합니다. 따라서, 운행 또는 성능과 관련하여 반대 방향으로 중복 관계를 추가할 필요가 없습니다.

관계에는 항상 방향이 있지만 응용 프로그램에서 필요없는 방향은 무시할 수 있습니다.

![이미지제목](https://mossupport.github.io/developer-manual/introduction/img/graphdb-nodes-and-rel-self.svg)

위의 예는 ```Tom Hanks```가 자신을 알고 있음을 의미합니다.


노드 관계를 따라 찾을 수 있는 것을 예제 그래프로 살펴봅니다. 



![이미지제목](https://mossupport.github.io/developer-manual/introduction/img/graphdb-simple-rels.svg)

**표 1.1. 관계의 방향과 유형의 사용**

| 우리가 알고 싶은 것    | 어디에서 시작하는가 | 관계 유형 | 방향     |
| ---------------------- | ------------------- | --------- | -------- |
| get actors in movie    | ```:Movie``` node         | ```:ACTED_IN``` | 들어오는 |
| get movies with actor  | ```:Person``` node        | ```:ACTED_IN``` | 나가는   |
| get directors of movie | ```:Movie``` node         | ```:DIRECTED``` | 들어오는 |
| get movies directed by | ```:Person``` node        | ```:DIRECTED``` | 나가는   |

#### 1.2.1.3. 속성

```
Neo4j의 속성은 [속성 그래프 모델(property graph model)](https://github.com/opencypher/openCypher/blob/master/docs/property-graph-model.adoc#pgm-definitions-property)에 설명 된 속성입니다. 노드와 관계 모두 속성을 가질 수 있습니다.
```

속성은 이름 (또는 키)이 문자열로 지정된 값입니다. 지원되는 속성값들은 다음과 같습니다.

+ 숫자를 가지는 추상형
	+ 정수
	+ 실수
+ 문자열
+ 논리형
+ 공간형
	+ 포인트
+ 시간형
	+ 날짜
	+ 시간
	+ 현지 시각
	+ 날짜 시간
	+ 현지날짜 시간
	+ 지속 시간


```null```은 유효한 속성값이 아닙니다. 속성 키 부재 여부를 데이터베이스에 저장하는 대신 ```null```로 모델링할 수 있습니다.

#### 1.2.1.4. 레이블

```
Neo4j의 레이블은 [속성 그래프 모델(property graph model)](https://github.com/opencypher/openCypher/blob/master/docs/property-graph-model.adoc#pgm-definitions-label)에 정의된 레이블입니다. 레이블을 역할에 지정하거나 유형을 노드에 지정합니다.
```

레이블은 노드를 집합으로 묶을 때 사용하는 그래프 구문입니다. 동일하게 레이블된 노드는 같은 집합에 속합니다. 많은 데이터베이스 쿼리는 작성하기 쉽고 효율적인 쿼리를 만들어서 집합으로 작업할 수 있습니다. 노드는 그래프 레이블에 추가 옵션을 포함하지 않는 레이블 숫자를 나타냅니다. 

레이블은 제약 조건을 정의하고 속성에 색인을 추가할 때 사용합니다.([1.2.1.7 스키마](https://mossupport.github.io/developer-manual/introduction/graphdb-concepts.html#1217-%EC%8A%A4%ED%82%A4%EB%A7%88) 참조)

예를 들어, 사용자를 나타내는 모든 노드는 레이블 ```:User```로 레이블할 수 있습니다. Neo4j에 주어진 이름의 모든 사용자를 찾는 것과 같이 사용자 노드에서만 작업을 수행하도록 요청할 수 있습니다.

그러나 더 많은 것에서도 레이블을 사용할 수 있습니다. 예를 들어, 런타임 중 레이블을 추가하거나 제거할 수 있으므로 임시 노드 상태를 표시할 수 있습니다. A : 일시 중지 된 은행 계좌는 정지된 은행 계좌를 나타낼 때 사용할 수 있습니다. a : 현재 시즌을 나타내는 야채 레이블 등이 있습니다.

이 예에서 그래프에 ```:Person```과 ```:Movie``` 레이블을 추가합니다.

![이미지제목](https://mossupport.github.io/developer-manual/introduction/img/graphdb-simple-labels.svg)

```:Actor``` 레이블을 ```Tom Hanks``` 노드에 추가해서 노드가 다중 레이블을 가지는 것을 확인 합니다. 

![이미지제목](https://mossupport.github.io/developer-manual/introduction/img/graphdb-simple-labels-multi.svg)


#### 레이블 명

유니 코드 문자열을 레이블 이름으로 사용할 수 있습니다. Cypher에서 식별자 규칙 충돌을 피하거나 레이블에 영숫자가 아닌 문자를 허용하기 위해서 backtick (') 구문을 사용합니다. 관습에 따라 레이블은 CamelCase 표기법으로 작성되며 첫 번째 문자는 대문자입니다. 예: ```User``` , ```CarOwner```. Cypher 쿼리의 스타일 지정 관련 내용은 [Cypher 스타일 가이드](https://github.com/opencypher/openCypher/blob/master/docs/style-guide.adoc)를 참조하십시오.

레이블에는 int의 id 공간이 있습니다. 데이터베이스에 포함 할 수있는 레이블의 최대 수는 약 20 억 개입니다.

#### 1.2.1.5. 순회

```
순회(Traversal)는 경로를 찾기 위한 그래프를 탐색합니다.
```

순회(traversal)는 그래프를 조회하고 시작 노드에서 관련 노드로 이동하여, "나에게 없는 음악 중에서 친구가 좋아하는 음악은?" 또는 "이 전원 공급이 중단되면 영향 받는 웹 서비스는?" 물음에 대한 답을 찾습니다.

그래프 순회는 몇 가지 규칙에 의한 관계를 따라 노드를 방문하는 것을 의미합니다. 일반적으로 그래프에서 흥미로운 노드와 관계가 있는 부분을 이미 알고 있으므로 하위 그래프만 방문합니다.

Cypher는 순회(Traversal) 및 기타 기법을 사용하여 그래프를 쿼리하는 선언 방법을 제공합니다. 자세한 내용은 [3. Cypher](https://mossupport.github.io/developer-manual/cypher/cypher.html)를 참조하십시오.

우리가 작은 예제 데이터베이스에 따라 ```Tom Hanks```가 연기 한 영화를 찾고 싶다면 Tom Hanks 노드에서 시작하여 노드에 연결된 ```ACTED_IN``` 관계를 따르고 결과로 ```Forrest Gump```로 마무리 합니다. 점선을 참고하세요 :

![이미지제목](https://mossupport.github.io/developer-manual/introduction/img/graphdb-traversal.svg)

#### 1.2.1.6. 경로

```
Neo4j의 경로는 속성 그래프 모델 경로는 Cypher 쿼리 또는 순회에서 검색됩니다.
```

이전 예제에서 순회(Traversal) 결과는 경로로 반환되기도 합니다.

![이미지제목](https://mossupport.github.io/developer-manual/introduction/img/graphdb-path.svg)

위 경로는 길이가 1입니다.

가능한 최단 경로는 길이가 0입니다. 즉, 단일 노드 만 있고 관계가 없는 경로이며 다음과 같이 표시 될 수 있습니다.

![이미지제목](https://mossupport.github.io/developer-manual/introduction/img/graphdb-path-zero.svg)

이 경로의 길이는 1입니다.


![이미지제목](https://mossupport.github.io/developer-manual/introduction/img/graphdb-path-example-loop.svg)

#### 1.2.1.7. 스키마

```
Neo4j는 선택적 스키마 그래프 데이터베이스입니다.
```

스키마없이도 Neo4j를 사용할 수 있습니다. 성능 및 모델링 이점을 얻기 위해 스키마를 도입 여부를 선택 할 수 있습니다. 스키마에서 얻을 수 있는 혜택을 누릴 때 사용할 수 있습니다.

스키마 명령어는 Neo4j 클러스터 마스터 시스템에만 적용됩니다. 슬레이브에 적용하면 ```Neo.ClientError.Transaction.InvalidType``` 오류 코드가 수신됩니다 


#### 인덱스

```
데이터베이스에서는 노드를 찾는 속도를 향상시키는 인덱스를 생성함으로써, 성능을 향상시킬 수 있습니다.
```

Neo4j는 색인을 생성 할 속성을 지정하면 그래프가 발전하면서 색인을 최신 상태로 유지합니다. 새로 인덱싱된 속성으로 노드를 검색하는 작업으로 성능이 크게 향상됩니다.

결과적으로 Neo4j 인덱스는 사용이 가능합니다. 즉, 처음 인덱스를 만들면 즉시 연산을 반환합니다. 인덱스는 백그라운드에서 채워지므로 쿼리에서 즉시 사용할 수는 없습니다. 인덱스가 백그라운드에 완전히 채워지면 결국 인덱스가 생성됩니다. 이는 이제 쿼리에서 사용할 준비가 되었음을 의미합니다.

인덱스에 문제가 발생하면 **실패** 상태가 될 수 있습니다. 실패하게 되면 쿼리 속도를 높이는 데 사용되지 않습니다. 다시 작성하려면 인덱스를 삭제하고 재생성할 수 있습니다. 해결하기 위해서 로그 내 실패 원인을 찾으시기 바랍니다.

Cypher의 인덱스 작업에 대해서는 [3.5.1 "인덱스"](https://mossupport.github.io/developer-manual/cypher/schema/index.html)를 참조하십시오.

#### 제약 조건

Neo4j는 데이터를 깨끗하게 유지하는 데 도움이됩니다. 이것은 제약 조건을 사용하여 작업 합니다. 제약 조건을 사용하면 데이터의 모양에 대한 규칙을 지정할 수 있습니다. 이 규칙을 위반하는 변경 사항은 거부됩니다.

Cypher의 제약 조건 작업 관련 내용은 [3.5.2 "제약 조건"](https://mossupport.github.io/developer-manual/cypher/schema/constraints.html)을 참조하십시오.