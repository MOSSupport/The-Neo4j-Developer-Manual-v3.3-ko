
# 3. Cypher

```
이 장에는 Cypher 쿼리 관련 완전하고 권위있는 문서가 포함되어 있습니다.
```

## 3.1. 도입

간단한 요약은 [3.1.1. "Cypher는 무엇입니까?"](https://mossupport.github.io/developer-manual/cypher/cypher.html#311-cypher%EB%8A%94-%EB%AC%B4%EC%97%87%EC%9E%85%EB%8B%88%EA%B9%8C)에서 확인할 수 있습니다. 첫 시작을할 때 [2.2. "Cypher 시작하기"](https://mossupport.github.io/developer-manual/get-started/cypher.html)를 참조하십시오. 용어 사용은 [부록 B, 용어](https://neo4j.com/docs/developer-manual/3.4/terminology/)를 참조하면 됩니다.

- [Cypher는 무엇입니까?](https://mossupport.github.io/developer-manual/cypher/cypher.html#311-cypher%EB%8A%94-%EB%AC%B4%EC%97%87%EC%9E%85%EB%8B%88%EA%B9%8C)
- [그래프 쿼리 및 업데이트](https://mossupport.github.io/developer-manual/cypher/introduction/query-the-graph.html)
- [트랜잭션](https://mossupport.github.io/developer-manual/cypher/introduction/transactions.html)
- [고유성](https://mossupport.github.io/developer-manual/cypher/introduction/uniqueness.html)
 
### 3.1.1. Cypher란?

Cypher는 그래프 저장소 표현과 효율적인 쿼리 및 업데이트에서 쓰이는 서술문 그래프 쿼리 언어입니다. Cypher는 간단하지만 영향력있는 언어입니다. Cypher는 복잡한 데이터베이스 쿼리를 간단하게 표현할 수 있습니다. 이렇게 하면 데이터베이스 억세스 손실 없이 도메인에 집중할 수 있습니다.

Cypher는 개발자 및 (우리가 중요하게 생각하는) 운영 전문가에게 적합한 인도적 쿼리가 되도록 고안되었습니다. 우리의 목표는 간단한 것을 쉽고, 복잡한 일을 가능하게 만드는 것입니다. 쿼리 구조는 영어 산문형식과 도해법을 기반으로하여 더 이해하기 쉽게 합니다. 우리는 글쓰기가 아닌 읽기에서 언어를 최적화하려고 노력했습니다.

선언적 언어이기 때문에, Cypher는 그래프를 *검색 하는 방법*이 아닌 그래프에서 *검색 할 내용* 을 명확하게 표현하는데 집중합니다. 이것은 Java와 같은 명령형 언어 및 Gremlin, [JRuby Neo4j bindings](https://github.com/neo4jrb/neo4j/)와 같은 스크립팅 언어와 대조적입니다. 이 접근 방식은 쿼리 최적화를 사용자에게 부담시키지 않고 물리적 데이터베이스 구조가 변경 되었기 때문에 (새 인덱스 등) 모든 traversals를 업데이트 요구하지 않고 세부 구현 사항을 만듭니다.


Cypher는 다양한 접근 방식에서 영감을 얻었으며 쿼리 표현을 위해 기존 방식을 따릅니다. ```WHERE``` 및 ```ORDER BY``` 와 같은 키워드는 [SQL](http://en.wikipedia.org/wiki/SQL)에서 영감을 받았습니다. 패턴 매칭은 [SPARQL](http://en.wikipedia.org/wiki/SPARQL)에서 표현 방식을 차용했습니다. 콜렉션 의미 중 일부는 Haskell 및 Python과 같은 언어에서 차용했습니다. 

#### 구조

Cypher는 SQL의 구조를 차용하며, 다양한 절을 사용하여 쿼리를 작성합니다.

절은 함께 묶여 있고 서로 중간 결과 집합을 제공합니다. 예를 들어, 하나의 ```MATCH``` 절에서 일치하는 변수는 다음 절이있는 문맥이됩니다.

쿼리 언어는 고유한 구절로 구성됩니다. 자세한 내용은 매뉴얼 뒷부분에서 읽을 수 있습니다.

다음은 그래프를 읽을 때 사용하는 몇 가지 절입니다:

- ```MATCH```: 일치시킬 그래프 패턴. 그래프에서 데이터를 가져 오는 일반적인 방법입니다.
- ```WHERE```: 절의 조항이 아닌 ```MATCH```, ```OPTIONAL MATCH``` 및 ```WITH```의 일부입니다. 패턴에 제약 조건을 추가하거나 ```WITH``` 중간 결과를 필터링합니다.
- ```RETURN```: 값을 반환합니다.

```MATCH``` 및 ```RETURN``` 이 수행되는 것을 확인합니다.

그림 3.1. 예제 그래프


![img](https://mossupport.github.io/developer-manual/cypher/img/Example-Graph-cypher-intro.svg)

예시 : 'John'과 발견한 친구의 친구를 모두 반환하기 전에 'John'과 'john'의 친구(직접적인 관계가 아닌)를 찾는 쿼리가 있습니다.

```
MATCH (john {name: 'John'})-[:friend]->()-[:friend]->(fof)
RETURN john.name, fof.name
```

결과값:

```
+----------------------+
| john.name | fof.name |
+----------------------+
| "John"    | "Maria"  |
| "John"    | "Steve"  |
+----------------------+
2 rows
```

다음으로 행위에 더 많은 부분을 설정하도록 필터링을 추가합니다:

사용자 이름 목록을 가져와서 이 목록과 일치하는 노드를 찾아서 친구들과 일치 시키고 'S'로 시작하는 '이름' 속성을 가진 친구를 반환합니다.

```
MATCH (user)-[:friend]->(follower)
WHERE user.name IN ['Joe', 'John', 'Sara', 'Maria', 'Steve'] AND follower.name =~ 'S.*'
RETURN user.name, follower.name
```

결과 값:

```
+---------------------------+
| user.name | follower.name |
+---------------------------+
| "Joe"     | "Steve"       |
| "John"    | "Sara"        |
+---------------------------+
2 rows
```

다음은 그래프를 업데이트 할 때 사용하는 절 입니다 :

- ```CREATE``` (또는 ```DELETE```): 노드와 관계 생성(삭제).
- ```SET``` (또는 ```REMOVE```): ```Set```으로 노드 값 속성 및 레이블 지정 ```REMOVE```로 제거.
- ```MERGE```: 기존 노드와 일치시키거나 새로운 노드 및 패턴을 만듭니다. 이는 특히 고유성 제약 조건과 함께 유용합니다.