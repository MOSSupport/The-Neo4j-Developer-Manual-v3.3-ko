## 5.1. HTTP API

```
이 챕터에서는 Neo4j 쿼리를 조작하는 HTTP API에 대해서 알아봅니다. 
```

### 5.1. 트랜잭션 Cypher HTTP 앤드포인트 
 
Neo4j 트랜잭션 HTTP 앤드포인트는 트랜잭션 범위 내 Cypher문을 실행할 수 있도록 합니다. 클라이언트가 커밋하거나 롤백을 클릭하기 전까지 많은 HTTP 요청에서 트랜잭션을 계속 열어둘 수 있습니다. 각 HTTP 요청에는 명령문 목록이 포함될 수 있으며 트랜잭션을 시작이나 트랜잭션 커밋을 요청할 때 편리하게 사용하도록 명령문을 포함할 수 있습니다. 
 
서버는 타임아웃을 사용해서 고아 트랜잭션을 보호합니다. 타임아웃 기간 내에 주어진 트랜잭션에 요청 사항이 없다면 서버는 다시 롤백할 것 입니다. 서버 설정 내 타임아웃은 ```Operations Manual → dbms.rest.transaction.idle_timeout```을 타임아웃 전에 몇 초를 설정함으로써 구성할 수 있습니다. 기본 타임 아웃은 60초 입니다. 

+ 문자 줄 바꿈은 사이퍼 명령어 내에서 사용할 수 없습니다. 

+ 오픈 트랜잭션은 HA 클러스터에서 멤버들과 공유되지 않습니다. 
  따라서 앤드포인트를 HA 클러스터에서 사용한다면 주어진 트랜잭션에서 모든 요청이 동일한 Neo4j 인스턴스로 보내지도록 해야합니다. 

+ ```USING PERIODIC COMMIT``` ( [섹션 3.5.4.5](/cypher/query-tuning.md) ```PERIODIC COMMIT```참조 )을 사용하는 사이퍼 쿼리는 새 트랜잭션을 생성하고 단일 HTTP 요청(섹션 5.1.2, "트랜잭션을 한 개 요청에서 시작하고 커밋"참조)으로 바로 커밋할 때만 실행될 수 있습니다. 

+ 요청이 실패하면 트랙잭션은 롤백됩니다. 트랜잭션 키 존재 유무 결과를 확인하면 트랜잭션이 오픈되어 있는지 알 수 있습니다.  


#### 5.1.1. 스트리밍

HTTP API의 응답은 JSON 스트림으로 전송 될 수 있으므로 서버 측에서 성능이 향상되고 메모리 오버 헤드가 낮아집니다. 스트리밍을 사용하려면 ```X-Stream: true```헤더를 각 요청에 제공하면 됩니다. 

반복되는 시나리오에서 쿼리 속도를 높히기 위해서는 리터럴을 사용하지 말고 가능한 변수로 대체하는 것이 좋습니다. 이렇게하면 서버가 쿼리 계획을 캐시 할 수 있습니다. 자세한 내용은 [섹션 3.2.6,"매개 변수"](/cypher/syntax.md)을 참조하십시오. 


#### 5.1.2. 하나의 요청에서 트랜잭션 시작과 커밋 

여러 HTTP 요청에서 트랜잭션을 오픈할 필요가 없다면 트랜잭션을 시작하고 명령어 실행을 단일 HTTP 요청으로 커밋할 수 있습니다. 

*요청 예시*

+ **POST** localhost:7474/db/data/transaction/commit
+ **Accept:** application/json; charset=UTF-8
+ **내용 유형:** application/json

```
{
  "statements" : [ {
    "statement" : "CREATE (n) RETURN id(n)"
  } ]
}
```

*응답 예시*

+ **200:** OK
+ **내용 유형:** application/json

```
{
  "results" : [ {
    "columns" : [ "id(n)" ],
    "data" : [ {
      "row" : [ 18 ],
      "meta" : [ null ]
    } ]
  } ],
  "errors" : [ ]
}
```

### 5.1.3. 다양한 명령어 실행  

동일한 요청에서 여러 개의 사이퍼 문을 보낼 수 있습니다. 이 응답은 각 명령문 결과를 포함됩니다. 

*요청 예시*

+ **POST** localhost:7474/db/data/transaction/commit
+ **Accept** application/json; charset=UTF-8
+ **내용 유형:** application/json

```
{
  "statements" : [ {
    "statement" : "CREATE (n) RETURN id(n)"
  }, {
    "statement" : "CREATE (n {props}) RETURN n",
    "parameters" : {
      "props" : {
        "name" : "My Node"
      }
    }
  } ]
}

```

*응답 예시*

+ **200:** OK
+ **내용 유형:** application/json

```
{
  "results" : [ {
    "columns" : [ "id(n)" ],
    "data" : [ {
      "row" : [ 14 ],
      "meta" : [ null ]
    } ]
  }, {
    "columns" : [ "n" ],
    "data" : [ {
      "row" : [ {
        "name" : "My Node"
      } ],
      "meta" : [ {
        "id" : 15,
        "type" : "node",
        "deleted" : false
      } ]
    } ]
  } ],
  "errors" : [ ]
}
```


### 5.1.4. 트랜잭션 시작 

0개 이상의 사이퍼 문을 트랜잭션 앤드포인트에 추가하여 새 트랜잭션을 시작할 수 있습니다. 서버는 명령어 결과와 오픈 트랜잭션 위치로 응답합니다. 

*요청 예시*

+ **POST** localhost:7474/db/data/transaction
+ **Accept:** application/json; charset=UTF-8
+ **내용 유형:** application/json

```
{
  "statements" : [ {
    "statement" : "CREATE (n {props}) RETURN n",
    "parameters" : {
      "props" : {
        "name" : "My Node"
      }
    }
  } ]
}
```

*응답 예시*

+ **201:** Created
+ **내용 유형:**: application/json
+ **위치:** localhost:7474/db/data/transaction/10

```
{
  "commit" : "http://localhost:7474/db/data/transaction/10/commit",
  "results" : [ {
    "columns" : [ "n" ],
    "data" : [ {
      "row" : [ {
        "name" : "My Node"
      } ],
      "meta" : [ {
        "id" : 22,
        "type" : "node",
        "deleted" : false
      } ]
    } ]
  } ],
  "transaction" : {
    "expires" : "Tue, 15 May 2018 11:01:09 +0000"
  },
  "errors" : [ ]
}
```


### 5.1.5. 오픈 트랜잭션에서 명령문 실행

오픈 트랜잭션이 있을 때 추가 명령문을 실행하는 여러 요청을 수행하고 트랜잭션 타임아웃을 재설정해서 트랜잭션을 계속 열어둘 수 있습니다. 
 
*요청 예시*

+ **POST** localhost:7474/db/data/transaction/12
+ **Accept:** application/json; charset=UTF-8
+ **내용 유형:** application/json

```
{
  "statements" : [ {
    "statement" : "CREATE (n) RETURN n"
  } ]
}
```

*응답 예시*

+ **200:** OK
+ **내용 유형:** application/json

```
{
  "commit" : "http://localhost:7474/db/data/transaction/12/commit",
  "results" : [ {
    "columns" : [ "n" ],
    "data" : [ {
      "row" : [ { } ],
      "meta" : [ {
        "id" : 23,
        "type" : "node",
        "deleted" : false
      } ]
    } ]
  } ],
  "transaction" : {
    "expires" : "Tue, 15 May 2018 11:01:09 +0000"
  },
  "errors" : [ ]
}
```

### 5.1.6. 오픈 트랜잭션 타임아웃 재설정

모든 고아 트랜잭션은 일정 기간 사용하지 않으면 자동으로 만료됩니다. 이 현상은 트랜잭션 타임 아웃을 재설정해서 막을 수 있습니다. 

타임아웃은 서버의 빈 목록을 실행하는 연결 유지 요청을 보내서 재설정할 수 있습니다. 이 요청은 트랜잭션 타임 아웃을 재설정하고 트랜잭션이 만료되는 새로운 시간을 응답의 트랜잭션 섹션에서 RFC1123 형식의 타임 스탬프 값으로 리턴합니다. 
 
*요청 예시*

+ **POST** localhost:7474/db/data/transaction/2
+ **Accept:** application/json; charset=UTF-8
+ **내용 유형:** application/json

```
{
  "statements" : [ ]
}
```

*응답 예시*

+ **200:** OK
+ **내용 유형** application/json

```
{
  "commit" : "http://localhost:7474/db/data/transaction/2/commit",
  "results" : [ ],
  "transaction" : {
    "expires" : "Tue, 15 May 2018 11:01:07 +0000"
  },
  "errors" : [ ]
}
```

### 5.1.7. 오픈 트랜잭션 커밋

오픈 트랜잭션이 있을 경우 커밋을 요청할 수 있습니다. 선택적으로 추가 명령문을 트랜잭션 커밋 전 실행될 요청과 함께 제출합니다.  

*요청 예시*

+ **POST** localhost:7474/db/data/transaction/6/commit
+ **Accept:** application/json; charset=UTF-8
+ **내용 유형:** application/json

```
{
  "statements" : [ {
    "statement" : "CREATE (n) RETURN id(n)"
  } ]
}
```

*응답 예시*

+ **200:** OK
+ **내용 유형:** application/json

```
{
  "results" : [ {
    "columns" : [ "id(n)" ],
    "data" : [ {
      "row" : [ 17 ],
      "meta" : [ null ]
    } ]
  } ],
  "errors" : [ ]
}
```

### 5.1.8. 오픈 트랜잭션 롤백 

오픈 트랜잭션이 있을 경우 롤백 요청을 보낼 수 있습니다. 서버가 트랜잭션을 롤백할 것 입니다. 이 트랜잭션 내에서 실행하는 명령문은 바로 실패할 것 입니다. 

*요청 예시*

+ **DELETE** localhost:7474/db/data/transaction/3
+ **Accept:** application/json; charset=UTF-8

*응답 예시*

+ **200:** OK
+ **내용 유형:** application/json; charset=UTF-8

```
{
  "results" : [ ],
  "errors" : [ ]
}
```


### 5.1.9. 쿼리 통계 자료 포함 

명령문에서 ```includeStats```을 ```true```로 설정하면 쿼리 통계 자료가 리턴됩니다. 

*요청 예시*

+ **POST** localhost:7474/db/data/transaction/commit
+ **Accept:** application/json; charset=UTF-8
+ **내용 형식:** application/json

```
{
  "statements" : [ {
    "statement" : "CREATE (n) RETURN id(n)",
    "includeStats" : true
  } ]
}
```

*응답 예시*

+ **200:** OK
+ **내용 형식:** application/json

```
{
  "results" : [ {
    "columns" : [ "id(n)" ],
    "data" : [ {
      "row" : [ 16 ],
      "meta" : [ null ]
    } ],
    "stats" : {
      "contains_updates" : true,
      "nodes_created" : 1,
      "nodes_deleted" : 0,
      "properties_set" : 0,
      "relationships_created" : 0,
      "relationship_deleted" : 0,
      "labels_added" : 0,
      "labels_removed" : 0,
      "indexes_added" : 0,
      "indexes_removed" : 0,
      "constraints_added" : 0,
      "constraints_removed" : 0
    }
  } ],
  "errors" : [ ]
}
```


### 5.1.10. 그래프 형식으로 결과 리턴 

쿼리에서 리턴된 그래프 구조의 노드와 관계를 알고싶다면 "그래프" 결과를 데이터 형식으로 구체화하면 됩니다. 예를들어서 이것은 그래프 구조를 시각화할 때 유용합니다. 형식은 결과의 모든 열에있는 노드와 관계를 조합하고 경로를 포함하여 노드와 관계의 모음을 플러시합니다. 

*요청 예시*

+ **POST** localhost:7474/db/data/transaction/commit
+ **Accept:** application/json; charset=UTF-8
+ **내용 유형:** Content-Type: application/json

```
{
  "statements" : [ {
    "statement" : "CREATE ( bike:Bike { weight: 10 } ) CREATE ( frontWheel:Wheel { spokes: 3 } ) CREATE ( backWheel:Wheel { spokes: 32 } ) CREATE p1 = (bike)-[:HAS { position: 1 } ]->(frontWheel) CREATE p2 = (bike)-[:HAS { position: 2 } ]->(backWheel) RETURN bike, p1, p2",
    "resultDataContents" : [ "row", "graph" ]
  } ]
}
```

*응답 예시*

+ **200:** OK
+ **내용 유형:** application/json

```
{
  "results" : [ {
    "columns" : [ "bike", "p1", "p2" ],
    "data" : [ {
      "row" : [ {
        "weight" : 10
      }, [ {
        "weight" : 10
      }, {
        "position" : 1
      }, {
        "spokes" : 3
      } ], [ {
        "weight" : 10
      }, {
        "position" : 2
      }, {
        "spokes" : 32
      } ] ],
      "meta" : [ {
        "id" : 19,
        "type" : "node",
        "deleted" : false
      }, [ {
        "id" : 19,
        "type" : "node",
        "deleted" : false
      }, {
        "id" : 9,
        "type" : "relationship",
        "deleted" : false
      }, {
        "id" : 20,
        "type" : "node",
        "deleted" : false
      } ], [ {
        "id" : 19,
        "type" : "node",
        "deleted" : false
      }, {
        "id" : 10,
        "type" : "relationship",
        "deleted" : false
      }, {
        "id" : 21,
        "type" : "node",
        "deleted" : false
      } ] ],
      "graph" : {
        "nodes" : [ {
          "id" : "19",
          "labels" : [ "Bike" ],
          "properties" : {
            "weight" : 10
          }
        }, {
          "id" : "20",
          "labels" : [ "Wheel" ],
          "properties" : {
            "spokes" : 3
          }
        }, {
          "id" : "21",
          "labels" : [ "Wheel" ],
          "properties" : {
            "spokes" : 32
          }
        } ],
        "relationships" : [ {
          "id" : "9",
          "type" : "HAS",
          "startNode" : "19",
          "endNode" : "20",
          "properties" : {
            "position" : 1
          }
        }, {
          "id" : "10",
          "type" : "HAS",
          "startNode" : "19",
          "endNode" : "21",
          "properties" : {
            "position" : 2
          }
        } ]
      }
    } ]
  } ],
  "errors" : [ ]
}
```

### 5.1.11. 취급상 오류 

트랜잭션 앤드포인트에 요청된 결과는 클라이언트로 다시 스트리밍 됩니다. 그러므로 서버는 HTTP 상태 코드를 전송할 때 요청 성공 여부를 알지 못합니다.

이런 이유로 트랜잭션 앤드포인트의 모든 요청은 명령문 실행 성공 여부와 관계 없이 200 또는 201 상태 코드를 리턴합니다. 응답 끝부분에서 서버는 명령문을 실행하는 동안 발생한 오류 리스트를 포함합니다. 리스트가 비어있다면, 요청은 성공적으로 완료될 것 입니다. 

명령문을 실행하는 동안 오류가 발생하면 서버는 트랜잭션을 롤백할 것 입니다. 

이 예에서는 취급상 오류를 확인하기 위해서 서버에 잘못된 명령문을 보냅니다. 

상태 코드 관련 더 많은 정보는 [섹션 A.1, "Neo4j 상태 코드"](https://neo4j.com/docs/developer-manual/3.4/reference/status-codes/)에서 확인할 수 있습니다. 

*요청 예시*

+ **POST** localhost:7474/db/data/transaction/11/commit
+ **Accept:** application/json; charset=UTF-8
+ **내용 유형:** application/json

```
{
  "statements" : [ {
    "statement" : "This is not a valid Cypher Statement."
  } ]
}
```

*응답 예시*

+ **200:** OK
+ **내용 유형:** application/json

```
{
  "results" : [ ],
  "errors" : [ {
    "code" : "Neo.ClientError.Statement.SyntaxError",
    "message" : "Invalid input 'T': expected <init> (line 1, column 1 (offset: 0))\n\"This is not a valid Cypher Statement.\"\n ^"
  } ]
}
```


### 5.1.12. 오픈 트랜잭션에서 에러 취급 

요청에 오류가 있을 때마다 서버는 트랜잭션을 롤백합니다. 트랜잭션 키의 존재 여부 응답을 검사하면 트랜잭션이 오픈되어 있는지 확인할 수 있습니다. 

*요청 예시*

+ **POST** localhost:7474/db/data/transaction/9
+ **Accept:** application/json; charset=UTF-8
+ **내용 유형:** application/json

```
{
  "statements" : [ {
    "statement" : "This is not a valid Cypher Statement."
  } ]
}
```

*응답 예시*

+ **200:** OK
+ **내용 유형:** application/json

```
{
  "commit" : "http://localhost:7474/db/data/transaction/9/commit",
  "results" : [ ],
  "errors" : [ {
    "code" : "Neo.ClientError.Statement.SyntaxError",
    "message" : "Invalid input 'T': expected <init> (line 1, column 1 (offset: 0))\n\"This is not a valid Cypher Statement.\"\n ^"
  } ]
}




