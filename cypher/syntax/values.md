### 3.2.1. 값 및 종류

Cypher는 여러 가지 데이터 유형에 1급 지원을 제공합니다.

이는 여러 범주로 나뉘고, 아래 하위 섹션에서 자세히 설명합니다.

- 속성 유형
- 구조 유형
- 합성 유형

#### 3.2.1.1. 속성 유형

- ✓ 사이퍼 쿼리에서 반환될 수 있습니다. 
- ✓ [변수](./parameters.md)로 사용할 수 있습니다.
- ✓ 속성으로 저장할 수 있씁니다. 
- ✓ [사이퍼 리터럴](./expressions.md)로 구성될 수 있습니다. 

속성 유형 구성:

- *Number*, 추상 유형은 다음 하위 유형을 가지고 있습니다:
  + 숫자
  + 실수
  + 문자열
  + 논리값 

형용사 `numeric`가 문맥에서 Cypher 함수나 표현을 나타낼 때 사용되는 경우 모든 유형의 Number (정수 또는 실수)가 적용됨을 나타냅니다. 

  



Homogeneous lists of simple types can also be stored as properties, although lists in general ([합성 유형](./values.md)) cannot be stored.                                                              

같은 종류의 단일 유형은 속성으로 저장될 수도 있습니다 


 Cypher also provides pass-through support for byte arrays, which can be stored as property values. Byte arrays are *not* considered a first class data type by Cypher, so do not have a literal representation.  



#### 3.2.1.2. Structural types

- ✓ Can be returned from Cypher queries
- ❏ Cannot be used as [parameters](https://neo4j.com/docs/developer-manual/3.3/cypher/syntax/parameters/)
- ❏ Cannot be stored as properties
- ❏ Cannot be constructed with [Cypher literals](https://neo4j.com/docs/developer-manual/3.3/cypher/syntax/expressions/)

Structural types comprise:

- Nodes, comprising:
  - Id
  - Label(s)
  - Map (of properties)
- Relationships, comprising:
  - Id
  - Type
  - Map (of properties)
  - Id of the start and end nodes
- Paths
  - An alternating sequence of nodes and relationships

|      | Nodes, relationships, and paths are returned as a result of pattern matching. |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

|      | Labels are not values but are a form of pattern syntax. |
| ---- | ------------------------------------------------------- |
|      |                                                         |

#### 3.2.1.3. Composite types

- ✓ Can be returned from Cypher queries
- ✓ Can be used as [parameters](https://neo4j.com/docs/developer-manual/3.3/cypher/syntax/parameters/)
- ❏ Cannot be stored as properties
- ✓ Can be constructed with [Cypher literals](https://neo4j.com/docs/developer-manual/3.3/cypher/syntax/expressions/)

Composite types comprise:

- **Lists** are heterogeneous, ordered collections of values, each of which has any property, structural or composite type.
- **Maps** are heterogeneous, unordered collections of (키, 값) pairs, where:
  - the key is a String
  - the value has any property, structural or composite type

Composite values can also contain `null`. 


Special care must be taken when using `null` (see [섹션 3.2.12, “Working with `null`”](./working-with-null.md)).