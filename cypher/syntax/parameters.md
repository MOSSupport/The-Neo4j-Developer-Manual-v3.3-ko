### 3.2.6. 매개 변수 
   
- 도입 
- 문자열 리터럴
- 일반 표현
- 대-소문자 구분 문자열 패턴 일치 
- 속성으로 노드 생성
- 속성으로 다양한 노드 생성
- 노드의 모든 속성 설정
- `SKIP` 및 `LIMIT`
- 노드 id
- 다양한 노드 ids
- 프로시저 호출
- 인덱스 값(명시 인덱스)
- 인덱스 쿼리(명시 인덱스)
  
#### 3.2.6.1. 도입

Cypher는 매개 변수로 쿼리하는 것을 지원합니다. 이는 개발자가 쿼리를 작성할 때 문자열 작성에 의존하지 않아도 된다는 것을 의미합니다. 또한 매개 변수는 Cypher가 실행 계획을 훨씬 쉽게 캐싱해서 쿼리 실행 시간을 단축합니다.  
 

매개 변수는 다음에서 사용합니다:

- 리터럴 및 표현 
- 노드 및 관계 ids
- 명시적 인덱스 전용 : 인덱스 값 및 쿼리 

매개 변수는 쿼리 계획으로 컴파일되는 쿼리 구조의 일부인 아래 구문에서 사용할 수 없습니다. 

- 속성 키; ```MATCH (n) WHERE n.$param = 'something'```는 사용할 수 없습니다. 
- 관계 유형
- 레이블

매개변수는 문자와 숫자 및 이들 조합으로 구성되지만, 숫자나 통화 기호로 시작할 순 없습니다. 

아래에서는 매개 변수 사용에 관련 포괄적인 리스트를 제공합니다. 이 예에서, 매개변수는 JSON으로 제공되며; 제출 방법은 드라이버에 따라 다릅니다.

이전 구문 {param}은 더 이상 사용되지 않으며 이후 릴리스에서 모두 제거되므로 새로운 매개변수 구문 ```$param``` 사용을 권장합니다. 


#### 3.2.6.2. 문자열 리터럴 

**변수** 

```
{
  "name" : "Johan"
}
```

**쿼리** 

```
MATCH (n:Person)
WHERE n.name = $name
RETURN n
```

이 구문에서도 매개변수를 사용할 수 있습니다. 

**변수**

```
{
  "name" : "Johan"
}
```

**쿼리** 

```
MATCH (n:Person { name: $name })
RETURN n
```

#### 3.2.6.3. 정규식 

**변수** 

```
{
  "regex" : ".*h.*"
}
```

**쿼리** 

```
MATCH (n:Person)
WHERE n.name =~ $regex
RETURN n.name
```

#### 3.2.6.4. 일치하는 대-소문자 문자열 패턴 

**변수** 

```
{
  "name" : "Michael"
}
```

**쿼리** 

```
MATCH (n:Person)
WHERE n.name STARTS WITH $name
RETURN n.name
```

#### 3.2.6.5. 속성으로 노드 생성 

**변수** 

```
{
  "props" : {
    "name" : "Andres",
    "position" : "Developer"
  }
}
```

**쿼리** 

```
CREATE ($props)
```

#### 3.2.6.6. 속성으로 다양한 노드 생성
 
**변수** 

```
{
  "props" : [ {
    "awesome" : true,
    "name" : "Andres",
    "position" : "Developer"
  }, {
    "children" : 3,
    "name" : "Michael",
    "position" : "Developer"
  } ]
}
```

**쿼리** 

```
UNWIND $props AS properties
CREATE (n:Person)
SET n = properties
RETURN n
```

#### 3.2.6.7. 노드의 모든 속성 설정
 
이것이 모든 현재 속성을 대체함을 유의하십시오.

**변수** 

```
{
  "props" : {
    "name" : "Andres",
    "position" : "Developer"
  }
}
```

**쿼리** 

```
MATCH (n:Person)
WHERE n.name='Michaela'
SET n = $props
```

#### 3.2.6.8. `SKIP` 및 `LIMIT`
 
**변수**

```
{
  "s" : 1,
  "l" : 1
}
```

**쿼리** 

```
MATCH (n:Person)
RETURN n.name
SKIP $s
LIMIT $l
```

#### 3.2.6.9. 노드 id

**변수** 

```
{
  "id" : 0
}
```

**쿼리** 

```
MATCH (n)
WHERE id(n)= $id
RETURN n.name
```

#### 3.2.6.10. 다양한 노드 ids

**변수**

```
{
  "ids" : [ 0, 1, 2 ]
}
```

**쿼리** 

```
MATCH (n)
WHERE id(n) IN $ids
RETURN n.name
```

#### 3.2.6.11. 프로시저 호출

**변수** 

```
{
  "indexname" : ":Person(name)"
}
```

**쿼리** 

```
CALL db.resampleIndex($indexname)
```

#### 3.2.6.12. 인덱스 값(명시 인덱스)
 
**변수**

```
{
  "value" : "Michaela"
}
```

**쿼리** 

```
START n=node:people(name = $value)
RETURN n
```

#### 3.2.6.13. 인덱스 쿼리(명시 인덱스)

**변수**

```
{
  "query" : "name:Andreas"
}
```

**쿼리** 

```
START n=node:people($query)
RETURN n
```