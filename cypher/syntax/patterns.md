### 3.2.9. 패턴 


- 도입 
- 노드 패턴
- 관련 노드 패턴
- 라벨 패턴
- 특성 지정
- 관계 패턴
- 변수-길이 패턴 매칭
- 경로 변수 할당

#### 3.2.9.1. 도입   

패턴 및 패턴-매칭은 Cypher의 핵심이며, Cypher를 효율적으로 사용하려면 패턴을 잘 이해해야 합니다.

패턴을 사용해서 찾으려는 데이터 모양을 설명하면, Cypher가 데이터를 가져오는 방법을 알아낼 것 입니다. 

The pattern describes the data using a form that is very similar to how one typically draws the shape of property graph data on a whiteboard: usually as circles (representing nodes) and arrows between them to represent relationships.







Patterns appear in multiple places in Cypher: in `MATCH`, `CREATE` and `MERGE` clauses, and in pattern expressions. Each of these is described in more detail in:

**섹션 3.3**
- 매치
- 선택적 사항
- 생성
- 병합 
- ```WHERE```에서 경로 패턴 사용

#### 3.2.9.2. 노드 패턴  

The very simplest 'shape' that can be described in a pattern is a node. A node is described using a pair of parentheses, and is typically given a name. For example:

```
(a)
```

This simple pattern describes a single node, and names that node using the variable `a`.

#### 3.2.9.3. 관련 노드 패턴
 

A more powerful construct is a pattern that describes multiple nodes and relationships between them. Cypher patterns describe relationships by employing an arrow between two nodes. For example:

```
(a)-->(b)
```

This pattern describes a very simple data shape: two nodes, and a single relationship from one to the other. In this example, the two nodes are both named as `a` and `b` respectively, and the relationship is 'directed': it goes from `a` to `b`.

This manner of describing nodes and relationships can be extended to cover an arbitrary number of nodes and the relationships between them, for example:

```
(a)-->(b)<--(c)
```

Such a series of connected nodes and relationships is called a "path".

Note that the naming of the nodes in these patterns is only necessary should one need to refer to the same node again, either later in the pattern or elsewhere in the Cypher query. If this is not necessary, then the name may be omitted, as follows:

```
(a)-->()<--(c)
```

#### 3.2.9.4. 라벨 패턴
 
In addition to simply describing the shape of a node in the pattern, one can also describe attributes. The most simple attribute that can be described in the pattern is a label that the node must have. For example:

```
(a:User)-->(b)
```

One can also describe a node that has multiple labels:

```
(a:User:Admin)-->(b)
```

#### 3.2.9.5. 특성 지정

노드 및 관계는 그래프에서 기초 구조 입니다. Neo4j는 더 풍부한 모델을 허용하도록 이 두 가지 속성을 사용합니다. 

속성은 map-구성을 이용해서 패턴으로 나타날 수 있습니다: 괄호는 여러 개의 키-표현 쌍을 쉼표로 구분합니다. 즉, 두 개의 속성을 가진 노드는 아래와 같이 쓰입니다. 

```
(a {name: 'Andres', sport: 'Brazilian Ju-Jitsu'})
```

이에 대한 기대와의 관계는 아래에 의해서 주어집니다. 

```
(a)-[{blocked: false}]->(b)
```

패턴에 속성이 있을 경우 데이터 모양에 제약을 추가합니다. ```CREATE``` 절의 경우, 속성은 새롭게 생성된 노드 및 관계에서 설정됩니다. ```MERGE``` 절의 경우 모양에서 속성은 추가 제약으로 쓰이며, 지정된 속성은 그래프의 기존 데이터와 일치해야 합니다. 일치하는 데이터가 없을 경우, ```MERGE```는 ```CREATE``` 역할을 하고 속성은 새롭게 생성된 노드 및 관계에서 설정됩니다. 


```CREATE```에 제공된 패턴은 속성을 명시하기 위해 단일 매개 변수를 사용할 것 입니다: ```CREATE (node $paramName)```. Cypher은 쿼리를 컴파일할 때 속성 이름으로 매칭을 효율적으로 수행하므로, 다른 절에 사용된 패턴에서는 수행할 수 없습니다. 



#### 3.2.9.6. 관계 패턴
 
관계를 설명하는 가장 간단한 방법은 이전 예시와 같이 두 노드 사이에 화살표를 넣는 것 입니다. 이 기술을 사용하면 관계가 존재해야 함과 그것의 방향성을 설명할 수 있습니다. 관계의 방향성을 신경쓰지 않는다면, 화살표 위쪽은 아래와 같이 생략할 수 있습니다. 

```
(a)--(b)
```

노드와 함께, 관계에도 이름을 부여할 수 있습니다. 이 경우에 대괄호 쌍은 화살표를 분해하고 변수는 그 사이에 위치합니다. 예: 

```
(a)-[r]->(b)
```

노드의 라벨과 마찬가지로, 관계는 유형을 가질 수 있습니다. 

관계를 특정 유형으로 설명할 때, 다음과 같이 명시할 수 있습니다:

```
(a)-[r:REL_TYPE]->(b)
```

라벨과 다르게 관게는 한가지 유형만 가질 수 있습니다. 

 But if we’d like to describe some data such that the relationship could have any one of a set of types, then they can all be listed in the pattern, separating them with the pipe symbol `|` like this:

 

```
(a)-[r:TYPE1|TYPE2]->(b)
```

Note that this form of pattern can only be used to describe existing data (ie. when using a pattern with `MATCH` or as an expression). It will not work with `CREATE` or `MERGE`, since it’s not possible to create a relationship with multiple types.

As with nodes, the name of the relationship can always be omitted, as exemplified by:

```
(a)-[:REL_TYPE]->(b)
```

#### 3.2.9.7. 변수-길이 패턴 매칭

|      | Variable length pattern matching in versions 2.1.x and earlier does not enforce relationship uniqueness for patterns described within a single `MATCH` clause. This means that a query such as the following: `MATCH (a)-[r]->(b), p = (a)-[*]->(c) RETURN *, relationships(p) AS rs` may include `r` as part of the `rs` set. This behavior has changed in versions 2.2.0 and later, in such a way that `r` will be excluded from the result set, as this better adheres to the rules of relationship uniqueness as documented here [Section 3.1.4, “Uniqueness”](https://neo4j.com/docs/developer-manual/3.4/cypher/introduction/uniqueness/). If you have a query pattern that needs to retrace relationships rather than ignoring them as the relationship uniqueness rules normally dictate, you can accomplish this using multiple match clauses, as follows: `MATCH (a)-[r]->(b) MATCH p = (a)-[*]->(c) RETURN *, relationships(p)`. This will work in all versions of Neo4j that support the `MATCH` clause, namely 2.0.0 and later. |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

Rather than describing a long path using a sequence of many node and relationship descriptions in a pattern, many relationships (and the intermediate nodes) can be described by specifying a length in the relationship description of a pattern. For example:

```
(a)-[*2]->(b)
```

This describes a graph of three nodes and two relationship, all in one path (a path of length 2). This is equivalent to:

```
(a)-->()-->(b)
```

A range of lengths can also be specified: such relationship patterns are called 'variable length relationships'. For example:

```
(a)-[*3..5]->(b)
```

This is a minimum length of 3, and a maximum of 5. It describes a graph of either 4 nodes and 3 relationships, 5 nodes and 4 relationships or 6 nodes and 5 relationships, all connected together in a single path.

Either bound can be omitted. For example, to describe paths of length 3 or more, use:

```
(a)-[*3..]->(b)
```

To describe paths of length 5 or less, use:

```
(a)-[*..5]->(b)
```

Both bounds can be omitted, allowing paths of any length to be described:

```
(a)-[*]->(b)
```

As a simple example, let’s take the graph and query below:

Figure 3.3. Graph

![alt](https://neo4j.com/docs/developer-manual/3.4/images/Patterns-1.svg)

Query. 

```
MATCH (me)-[:KNOWS*1..2]-(remote_friend)
WHERE me.name = 'Filipa'
RETURN remote_friend.name
```

| remote_friend.name |
| ------------------ |
| 2 rows             |
| `"Dilshad"`        |
| `"Anders"`         |

This query finds data in the graph which a shape that fits the pattern: specifically a node (with the name property **'Filipa'**) and then the `KNOWS` related nodes, one or two hops away. This is a typical example of finding first and second degree friends.

Note that variable length relationships cannot be used with `CREATE` and `MERGE`.

#### 3.2.9.8. 경로 변수 할당


As described above, a series of connected nodes and relationships is called a "path". Cypher allows paths to be named using an identifer, as exemplified by:

```
p = (a)-[*3..5]->(b)
```

You can do this in `MATCH`, `CREATE` and `MERGE`, but not when using patterns as expressions.