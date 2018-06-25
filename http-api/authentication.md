
## 5.2. 인증 및 권한 부여                  


```
이 섹션에서는 Neo4j HTTP API를 사용하여 인증 및 권한 부여하는 방법에 대해서 알아봅니다.
```

HTTP API는 인증 및 권한 부여를 지원하므로 유효한 사용자의 이름과 비밀번호를 사용하여 HTTP API에 대한 요청을 승인해야 합니다. 인증 및 권한은 기본적으로 활성화되어 있습니다. 인증 및 권환 부여 활성화 또는 비활성화 시키는 방법과 관련된 내용은 다음을 참고하십시오. [운영 메뉴얼 → 인증 및 권한 부여](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/enable/) 
 
Neo4j가 처음 설치되면 기본 사용자와 비밀번호를 각각  ```neo4j```로 인증할 수 있습니다. 리소스 접속이 허용되기 전에 기본 비밀번호를 변경해야 합니다. 이것은 Neo4j 브라우저나 HTTP를 직접 호출함으로서 변경가능합니다.  

### 5.2.1. 인증                     

#### 5.2.1.1. 인증 누락                       

인증 헤더가 주어지지 않았다면 서버는 에러 메시지를 띄울 것 입니다. 

*요청 예시*

+ **GET** localhost:7474/db/data/                     
+ **Accept:** application/json; charset=UTF-8                     

*응답 예시*

+ **401:** 승인되지 않음                   
+ **내용 유형:** application/json; charset=UTF-8                     
+ **권한 부여:** Basic realm="Neo4j"                     

```
{
  "errors" : [ {
    "code" : "Neo.ClientError.Security.Unauthorized",
    "message" : "No authentication header supplied."
  } ]
}
```

#### 5.2.1.2. 서버 접속 인증                        

HTTP Basic Auth를 이용해서 Neo4j에 사용자 이름과 비밀번호를 보내 인증합니다. 요청할 때는 인증 헤더와 "payload"가 base64로 인코딩 되어있는 "username:password" 문자열 값인 <payload>을 포함해야 합니다. 

*요청 예시*

+ **GET** localhost:7474/user/neo4j                     
+ **Accept:** application/json; charset=UTF-8                     
+ **권한 부여:** Basic bmVvNGo6c2VjcmV0                     

*응답 예시*

+ **200:** OK                     
+ **내용 유형:** application/json; charset=UTF-8                     

```
{
  "password_change_required" : false,
  "password_change" : "http://localhost:7474/user/neo4j/password",
  "username" : "neo4j"
}
```


#### 5.2.1.3. 잘못된 인증                       

잘못된 사용자 이름이나 비밀번호로 접속하면 서버는 에러 메시지를 띄웁니다. 

*요청 예시*

+ **POST** localhost:7474/db/data/                     
+ **Accept:** application/json; charset=UTF-8                     
+ **권한 부여:** Basic bmVvNGo6aW5jb3JyZWN0                     

*응답 예시*

+ **401:** 승인되지 않음                    
+ **내용 유형:** application/json; charset=UTF-8                     
+ **WWW-Authenticate:** Basic realm="Neo4j"                     

```
{
  "errors" : [ {
    "code" : "Neo.ClientError.Security.Unauthorized",
    "message" : "Invalid username or password."
  } ]
}
```

#### 5.2.1.4. 비밀번호 변경 요청       

Neo4j가 처음 접속된 것과 같이 사용자는 새로운 비밀번호를 선택해야합니다. 데이터 베이스는 새 비밀번호가 필요하다는 알림을 보내고 엑세스를 거부할 것 입니다. 

*요청 예시*

+ **GET** localhost:7474/db/data/                     
+ **Accept:** application/json; charset=UTF-8                     
+ **권한 부여:** Basic bmVvNGo6bmVvNGo=                     

*응답 예시*

+ **403:** 금지                     
+ **내용 유형:** application/json; charset=UTF-8                     

```
{
  "password_change" : "http://localhost:7474/user/neo4j/password",
  "errors" : [ {
    "code" : "Neo.ClientError.Security.Forbidden",
    "message" : "User is required to change their password."
  } ]
}
```

### 5.2.2. 사용자 상태와 비밀번호 변경 

#### 5.2.2.1. 사용자 상태

현재 비밀번호를 알다면 서버에 사용자 상태 확인을 요청할 수 있습니다. 

*요청 예시*

+ **GET** localhost:7474/user/neo4j                     
+ **Accept:** application/json; charset=UTF-8                     
+ **권한 부여:** Basic bmVvNGo6c2VjcmV0                     

*응답 예시*

+ **200:** OK                     
+ **내용 유형:** application/json; charset=UTF-8                     

```
{
  "password_change_required" : false,
  "password_change" : "http://localhost:7474/user/neo4j/password",
  "username" : "neo4j"
}
```

#### 5.2.2.2. 첫 접속시 사용자 상태

처음 접속할 때 기본 비밀번호를 사용하면 사용자 비밀번호를 변경해야 함을 사용자 상태에 알립니다. 

*요청 예시*

+ **GET** localhost:7474/user/neo4j                     
+ **Accept:** application/json; charset=UTF-8                     
+ **권한 부여:** Basic bmVvNGo6bmVvNGo=                     

*응답 예시*

+ **200:** OK                     
+ **내용 유형:** application/json; charset=UTF-8                     

```
{
  "password_change_required" : true,
  "password_change" : "http://localhost:7474/user/neo4j/password",
  "username" : "neo4j"
}
```

#### 5.2.2.3. 사용자 비밀번호 변경

현재 비밀번호를 알고 있으면 서버에 새로운 사용자 비밀번호를 변경하도록 요청할 수 있습니다. 현재 비밀번호와 다른 원하는 비밀번호로 설정할 수 있습니다. 

*요청 예시*

+ **POST** localhost:7474/user/neo4j/password                     
+ **Accept:** application/json; charset=UTF-8                     
+ **권한 부여:** Basic bmVvNGo6bmVvNGo=                     
+ **내용 유형:** application/json                     

```
{
  "password" : "secret"
}
```

*응답 예시*

+ **200:** OK                     

### 5.2.3. 인증 및 권한 부여가 거부되었을 때 액서스(access)     

인증 및 권한 부여가 거부되었을 떄, HTTP API 요청은 ```Authorization```헤더 없이 전송될 수 있습니다.          

### 5.2.4. 하나의 인스턴스에서 다른 인스턴스로 보안 설정 복사 

사용자 이름과 비밀번호 결합은 각 Neo4j 인스턴스에 국한됩니다. Neo4j 인스턴스를 미리 설정된 인증 및 권한으로 시작하고 싶을 때가 많습니다. 이것을 하는 방법은[작동 메뉴얼 → 사용자와 역할 전파](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/propagate-users-and-roles)을 참조하십시오.

