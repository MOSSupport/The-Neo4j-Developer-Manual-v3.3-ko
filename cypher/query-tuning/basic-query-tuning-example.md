
### 3.6.3. 기본 쿼리 튜닝 예시

프로파일링 쿼리 응답을 기본 예시에서 확인합니다. 아래 예에서 영화 데이터 설정을 사용합니다. 
 
데이터 임포팅부터 시작합니다:

```
LOAD CSV WITH HEADERS FROM 'https://neo4j.com/docs/developer-manual/3.4/csv/query-tuning/movies.csv' AS line
MERGE (m:Movie { title: line.title })
ON CREATE SET m.released = toInteger(line.released), m.tagline = line.tagline
```

```
LOAD CSV WITH HEADERS FROM 'https://neo4j.com/docs/developer-manual/3.4/csv/query-tuning/actors.csv' AS line
MATCH (m:Movie { title: line.title })
MERGE (p:Person { name: line.name })
ON CREATE SET p.born = toInteger(line.born)
MERGE (p)-[:ACTED_IN { roles:split(line.roles, ';')}]->(m)
```

```
LOAD CSV WITH HEADERS FROM 'https://neo4j.com/docs/developer-manual/3.4/csv/query-tuning/directors.csv' AS line
MATCH (m:Movie { title: line.title })
MERGE (p:Person { name: line.name })
ON CREATE SET p.born = toInteger(line.born)
MERGE (p)-[:DIRECTED]->(m)
```

먼저, 'Tom Hanks'을 찾도록 쿼리를 작성합니다. 이것을 하는 간단한 방법은 다음과 같습니다:

```
MATCH (p { name: 'Tom Hanks' })
RETURN p
```

이 쿼리는 `Tom Hanks` 노드를 찾지만 데이터베이스의 노드가 증가하면서 점점 느려질 것 입니다. 이 현상이 일어나는 원인은 쿼리를 프로파일링하여 알 수 있습니다. 
 
쿼리를 프로파일링하는 이 옵션과 관련된 내용은 [섹션 3.6.2, “프로파일링 쿼리”](./how-do-i-profile-a-query.md)에서 확인할 수 있습니다. 이 경우, 쿼리리에 ```PROFILE``` 접두사를 추가합니다.

```
PROFILE
MATCH (p { name: 'Tom Hanks' })
RETURN p
```

```
Compiler CYPHER 3.4

Planner COST

Runtime INTERPRETED

Runtime version 3.4

+-----------------+----------------+------+---------+-----------------+-------------------+----------------------+-----------+---------------------------+
| Operator        | Estimated Rows | Rows | DB Hits | Page Cache Hits | Page Cache Misses | Page Cache Hit Ratio | Variables | Other                     |
+-----------------+----------------+------+---------+-----------------+-------------------+----------------------+-----------+---------------------------+
| +ProduceResults |             16 |    1 |       0 |               0 |                 0 |               0.0000 | p         |                           |
| |               +----------------+------+---------+-----------------+-------------------+----------------------+-----------+---------------------------+
| +Filter         |             16 |    1 |     163 |               0 |                 0 |               0.0000 | p         | p.name = $`  AUTOSTRING0` |
| |               +----------------+------+---------+-----------------+-------------------+----------------------+-----------+---------------------------+
| +AllNodesScan   |            163 |  163 |     164 |               0 |                 0 |               0.0000 | p         |                           |
+-----------------+----------------+------+---------+-----------------+-------------------+----------------------+-----------+---------------------------+

Total database accesses: 327
```

실행 플랜을 읽을 때, 아래 부터 읽는 것을 기억해야 합니다. 

마지막 행에서 데이터베이스에 'Tom Hanks' 속성을 포함하는 노드가 하나 뿐이므로 행 열의 값이 높은 것을 확인할 수 있습니다. 연산자 행에서 AllNodesScan이 사용된 것을 확인할 수 있습니다. 이는 쿼리 플래너가 데이터베이스의 모든 노드에서 스캔된 것을 의미합니다. 

사람이 아닌 노드를 많이 찾지만, 원하는 속성이 없기에  `Tom Hanks`를 찾는 것이 비효율적으로 보일 수 있습니다.  

문제를 해결하기 위해서 노드를 확인할 때 쿼리 플래너가 검색 범위를 좁힐 수 있도록 라벨을 명시하면 됩니다. 이 쿼리에서 ```Person``` 라벨을 추가해야 합니다. 

```
MATCH (p:Person { name: 'Tom Hanks' })
RETURN p
```

이 쿼리는 처음 것보다 빠르지만 데이터베이스의 사람 수가 증가함에 따라 쿼리가 느려집니다. 
우리는 다시 쿼리 결과를 프로파일링하여 이유를 분석 할 수 있습니다.

```
PROFILE
MATCH (p:Person { name: 'Tom Hanks' })
RETURN p
```

```
Compiler CYPHER 3.4

Planner COST

Runtime INTERPRETED

Runtime version 3.4

+------------------+----------------+------+---------+-----------------+-------------------+----------------------+-----------+---------------------------+
| Operator         | Estimated Rows | Rows | DB Hits | Page Cache Hits | Page Cache Misses | Page Cache Hit Ratio | Variables | Other                     |
+------------------+----------------+------+---------+-----------------+-------------------+----------------------+-----------+---------------------------+
| +ProduceResults  |             13 |    1 |       0 |               0 |                 0 |               0.0000 | p         |                           |
| |                +----------------+------+---------+-----------------+-------------------+----------------------+-----------+---------------------------+
| +Filter          |             13 |    1 |     125 |               0 |                 0 |               0.0000 | p         | p.name = $`  AUTOSTRING0` |
| |                +----------------+------+---------+-----------------+-------------------+----------------------+-----------+---------------------------+
| +NodeByLabelScan |            125 |  125 |     126 |               0 |                 0 |               0.0000 | p         | :Person                   |
+------------------+----------------+------+---------+-----------------+-------------------+----------------------+-----------+---------------------------+

Total database accesses: 251
```

마지막 열 ```Rows``` 값이 줄어들었고, 이전에 보였던 일부 노드는 더 이상 확인 되지 않습니다. NodeByLabelScan 연산자는 데이터베이스의 모든 `Person` 노드 선형 스캔을 수행하여 이를 달성했음을 나타냅니다. 

이 작업을 마치면, ```Filter``` 연산자를 사용하고 각 이름 특성을 비교해서 모든 노드를 확인할 수 있습니다. 

몇가지 경우에 수용할 수 있지만, 자주 사용할 경우 ```Person``` 이름 속성에 라벨을 생성하면 더 나은 성능을 나타냅니다. 

```
CREATE INDEX ON :Person(name)
```

이제 쿼리를 다시 실행하면, 더 빨라집니다. 

```
MATCH (p:Person { name: 'Tom Hanks' })
RETURN p
```

그 이유를 확인하기 위해 쿼리를 프로파일링 합니다. 

```
PROFILE
MATCH (p:Person { name: 'Tom Hanks' })
RETURN p
```

```
Compiler CYPHER 3.4

Planner COST

Runtime INTERPRETED

Runtime version 3.4

+-----------------+----------------+------+---------+-----------------+-------------------+----------------------+-----------+---------------+
| Operator        | Estimated Rows | Rows | DB Hits | Page Cache Hits | Page Cache Misses | Page Cache Hit Ratio | Variables | Other         |
+-----------------+----------------+------+---------+-----------------+-------------------+----------------------+-----------+---------------+
| +ProduceResults |              1 |    1 |       0 |               0 |                 0 |               0.0000 | p         |               |
| |               +----------------+------+---------+-----------------+-------------------+----------------------+-----------+---------------+
| +NodeIndexSeek  |              1 |    1 |       2 |               0 |                 0 |               0.0000 | p         | :Person(name) |
+-----------------+----------------+------+---------+-----------------+-------------------+----------------------+-----------+---------------+

Total database accesses: 2
```

 