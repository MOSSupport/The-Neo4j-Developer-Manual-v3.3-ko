
## 4.3. 세션 및 트랜잭션

```
이 섹션에서는 작업 단위를 작성하고 다음 작업에 논리적인 컨텍스트를 제공하는 방법에 대해서 다룹니다. 
```


### 4.3.1. 세션

세션은 트랜젝션의 연속적인 컨테이너 입니다. 세션은 필요한 만큼 풀에서 커넥션을 얻기 떄문에 가볍고 일회성으로 처리해야 합니다. 스레드 안전성이 중요한 언어에서 세션은 스레드로부터 위험한 것으로 간주하면 안됩니다. 

이것을 지원하는 언어에서 세션은 주로 컨텍스트 블록에서 범위가 정해집니다. 이것은 세션이 바르게 닫히고 모든 기본 연결이 해제되거나 유출되지 않도록합니다. 

**예시 4.18. 세션**

+ C#

```
public void AddPerson(string name)
{
    using (var session = Driver.Session())
    {
        session.Run("CREATE (a:Person {name: $name})", new {name});
    }
}
```

+ Java

```
public void addPerson(String name)
{
    try ( Session session = driver.session() )
    {
        session.run("CREATE (a:Person {name: $name})", parameters( "name", name ) );
    }
}
```

+ JavaScript

```
const session = driver.session();

session.run('CREATE (a:Person {name: $name})', {'name': personName}).then(() => {
  session.close(() => {
    console.log('Person created, session closed');
  });
});
```

+ Python

```
def add_person(self, name):
    with self._driver.session() as session:
        session.run("CREATE (a:Person {name: $name})", name=name)
```

### 4.3.2. 트랜잭션

트랜잭션은 한 개 이상의 Cypher 실행문으로 구성된 작업의 단위 원소 입니다. 트랜잭션은 세션에서 실행됩니다. 

Cypher 문을 실행하려면 두 가지 정보가 필요합니다: 견본 템플릿과 키가 있는 매개변수 집합. 템플릿은 런타임에 변수 값으로 대체된 플레이스 홀더를 포함하는 문자열 입니다. 변수화되지 않은 Cypher을 실행할 수 있지만 Cypher문 내에 변수를 사용하는것이 프로그래밍 연습에 더 도움이 됩니다. 이것은 Cypher 엔진에서 명령문을 캐싱할 수 있으므로 성능 향상에 도움됩니다. 매개 변수 값은 [섹션 3.2.1, “값과 유형”](/cypher/syntax.md) Neo4j 세 가지 유형을 제공하는 Neo4j 드라이버 API에 부착되어야 합니다.

Neo4j 드라이버 API는 아래 세 가지 유형을 제공합니다. 

+ 자동-커밋 트랜잭션
+ 트랜잭션 함수
+ 명시적 트랜잭션

이 중에서 트랜잭션 기능만 자동으로 실패를 표시합니다. 

#### 4.3.2.1. 자동-커밋 트랜잭션

자동-커밋 트랜잭션은 간단하지만 트랜잭션에 제한된 유형입니다. 이런 트랜잭션은 오직 한 개의 Cypher문으로 구성되며 실패했을 때 자동으로 재생할 수 없으며 캐쥬얼 체인에 참여할 수 없습니다. 

자동-커밋 트랜잭션은 ```session.run``` 메소드로 사용할 수 있습니다:

**예시 4.19. 자동-커밋 트랜잭션**

+ C#

```
public void AddPerson(string name)
{
    using (var session = Driver.Session())
    {
        session.Run("CREATE (a:Person {name: $name})", new {name});
    }
}
```

+ Java
 
```
public void addPerson( String name )
{
    try ( Session session = driver.session() )
    {
        session.run( "CREATE (a:Person {name: $name})", parameters( "name", name ) );
    }
}
```

+ JavaScript

```
function addPerson(name) {
  const session = driver.session();
  return session.run('CREATE (a:Person {name: $name})', {name: name}).then(result => {
    session.close();
    return result;
  });
}
```

+ Python

```
def add_person(self, name):
    with self._driver.session() as session:
        session.run("CREATE (a:Person {name: $name})", name=name)
```


자동-커밋 트랜잭션은 네트워크로 전송되고 즉시 승인됩니다. 이것은 다수의 트랜잭션이 네트워크 패킷을 공유하지 못하는 것을 의미하며, 다른 트랜잭션 유형보다 네트워크 효율성이 낮습니다.

자동-커밋 트랜잭션은 Cypher을 배우거나 일회성 스크립트를 작성하는 것과 같이 단순한 케이스에 사용 됩니다. 자동 커밋 트랜잭션은 프로덕션 환경에서 성능이나 탄력성을 다룰 떄는 사용하지 않는 것이 좋습니다.

그러나, 자동-커밋 트랜잭션은 ```USING PERIODIC COMMIT``` Cypher 명령어를 실행하는 유일한 방법입니다. 

#### 4.3.2.2. 트랜잭션 함수

트랜잭션 함수는 트랜잭션 작업 단위를 포함할 때 사용하는 것을 권장합니다. 이 형식은 최소 상용구 코드를 필요로하며 데이터베이스 쿼리와 어플리케이션 논리 분리를 허용합니다. 

**예시 4.20. 트랜잭션 기능**

+ C#

```
public void AddPerson(string name)
{
    using (var session = Driver.Session())
    {
        session.WriteTransaction(tx => tx.Run("CREATE (a:Person {name: $name})", new {name}));
    }
}
```

+ Java

```
public void addPerson( final String name )
{
    try ( Session session = driver.session() )
    {
        session.writeTransaction( new TransactionWork<Integer>()
        {
            @Override
            public Integer execute( Transaction tx )
            {
                return createPersonNode( tx, name );
            }
        } );
    }
}

private static int createPersonNode( Transaction tx, String name )
{
    tx.run( "CREATE (a:Person {name: $name})", parameters( "name", name ) );
    return 1;
}
```

+ JavaScript

```
const session = driver.session();
const writeTxPromise = session.writeTransaction(tx => tx.run('CREATE (a:Person {name: $name})', {'name': personName}));

writeTxPromise.then(result => {
  session.close();

  if (result) {
    console.log('Person created');
  }
});
```

+ Python

```
def add_person(self, name):
    with self._driver.session() as session:
        session.write_transaction(self.create_person_node, name)

@staticmethod
def create_person_node(tx, name):
    tx.run("CREATE (a:Person {name: $name})", name=name)
```

또한 트랜잭션 함수는 자동 재시도 기계 장치를 활용해서 커넥션 및 일시적인 문제를 해결할 수 있습니다. 이 재시도 기능은 드라이버 구성에 있습니다. 

트랜잭션 함수의 쿼리 결과는 그 기능에서 사용해야 합니다. 트랜잭션 함수 값은 원시 결과가 아닌 파생된 값 입니다.
 
#### 4.3.2.3. 명시적 트랜잭션

명시적 트랜잭션은 트랜잭션 기능 형식으로 명확한 ```BEGIN```, ```COMMIT``` 및 ```ROLLBACK``` 조작 관련 액세스를 제공합니다. 이 형식은 소수 케이스에서 유용하지만 트랜잭션 함수를 사용할 것을 권장합니다. 

#### 4.3.2.4. Cypher 에러

Cypher을 실행할 때 Cypher 엔진을 사용해서 예외 처리를할 수 있습니다. 각 예외처리는 에러 메시지 관련 정보 제공 [상태 코드](https://neo4j.com/docs/developer-manual/3.4/reference/status-codes/)와 상세 내용 메시지로 구성됩니다.  

에러는 아래 테이블과 같이 분류되어 있습니다. 

| 분류              | 요약                                                         |
| ----------------- | ------------------------------------------------------------ |
| 클라이언트 에러   | 클라이언트 어플리케이션에서 에러가 발생했습니다. 어플리케이션은 작업을 수정하고 재시도해야 합니다. |
| 데이터베이스 에러 | 서버에서 에러가 발생했습니다. 일반적으로 어플리케이션을 재시도할 수 없습니다. |
| 일시적인 에러     | 일시적인 에러가 발생했습니다. 어플리케이션은 작업을 재시도해야 합니다. |


### 4.3.3. 인과관계 연결

인과관계 클러스터로 작업할 때 트랜잭션은 인과관계 지속성을 위해 연결됩니다. 이는 두 가지 트랜잭션 모두 첫 번째 트랜잭션이 커밋된 후에 두 번째 트랜잭션을 사용할 수 있음을 의미합니다. 트랜잭션이 다른 물리적인 클러스터 멤버에서 실행될 때도 마찬가지 입니다. 

프로토콜 제한으로 자동-커밋 트랜잭션은 현재 인과 관계 체인에 참여할 수 없습니다. 이것은 이후 프로토콜에서 변경되지만, 인과 관계 일관성이 중요한 곳에서는 ```session.run``` 호출을 자제해야 합니다. 


인과 관계는 트랜잭션 사이에 [북마크](https://neo4j.com/docs/developer-manual/3.4/terminology)를 전달하여 연결합니다. 각 북마크는 트랜잭션 히스토리에 지점을 기록하고 클러스터 맴버에게 특정 시퀀스 작업 수행을 알릴 때 사용됩니다. 내부적으로 북마크는 COMMIT이 성공하면 서버에서 클라이언트로 전송되며 BEGIN에서는 클라이언트에서 서버로 전달됩니다. 한 개 이상의 북마크를 수신하면 트랜잭션 서버는 최근 트랜잭션을 잡을때까지 차단합니다.  

세션 내에 북마크 전파는 자동적으로 수행되며 명시적 신호나 어플리케이션 설정을 요구하지 않습니다. 관련 없는 작업에서 이 메커니즘을 사용하지 않으면 어플리케이션은 여러 세션을 사용할 수 있습니다. 이것은 클러스터 체인에서 오버헤드를 피합니다. 세션 간 전파는 마지막 북마크를 한 개 이상 세션에서 추출하여 다른 세션으로 구성하여 전달할 수 있습니다. 이것은 일반적으로 어플리케이션이 북마크와 직접 작업하는 유일한 경우입니다. 

**특징 4.1. 북마크 전달**

![driver passing bookmarks](https://neo4j.com/docs/developer-manual/3.4/images/driver-passing-bookmarks.svg) 

**예시 4.21. 세션 사이 북마크 넣기**

이 예는 세션 사이에서 북마크를 전달하는 것을 나타냅니다. 

세 가지 다른 세션을 사용합니다.: a,b, c. 이 세션 내에서 두 가지 별개의 트랜잭션을 운영합니다. 첫 번째로 사람 Alice를 생성하고, 두 번째로 그녀가 Wayne에서 일하는 것을 기록합니다. 두 가지 트랜잭션에서 전송된 북마크는 세션에서 처리됩니다. 마지막 트랜잭션의 북마크는 추후에 사용하도록 배열로 저장합니다. 

세션 b에서 두 가지 별개 트랜잭션도 운영합니다. 첫 번째에서 사람 Bob을 생성하고, 두 번째로 그가 LexCorp 기업에서 일하는 것을 기록합니다. 두 가지 트랜잭션 사이로 전송되는 북마크는 세션에서 처리됩니다. 마지막 트랜잭션 북마크는 나중에 사용하도록 배열로 저장합니다. 

마지막 세션 c에서는 Alice와 Bob사이에 우정을 맺어주려 합니다. 이것은 Alice와 Bob이 처음 생성했을 때만 가능합니다. 확실하게 하기위해서 세션 a와 세션 b에서 각각 마지막 트랜잭션 북마크를 전달합니다. 

+ C#

```
// Create a company node
private void AddCompany(ITransaction tx, string name)
{
    tx.Run("CREATE (a:Company {name: $name})", new {name});
}

// Create a person node
private void AddPerson(ITransaction tx, string name)
{
    tx.Run("CREATE (a:Person {name: $name})", new {name});
}

// Create an employment relationship to a pre-existing company node.
// This relies on the person first having been created.
private void Employ(ITransaction tx, string personName, string companyName)
{
    tx.Run(@"MATCH (person:Person {name: $personName})
             MATCH (company:Company {name: $companyName})
             CREATE (person)-[:WORKS_FOR]->(company)", new {personName, companyName});
}

// Create a friendship between two people.
private void MakeFriends(ITransaction tx, string name1, string name2)
{
    tx.Run(@"MATCH (a:Person {name: $name1})
             MATCH (b:Person {name: $name2})
             MERGE (a)-[:KNOWS]->(b)", new {name1, name2});
}

// Match and display all friendships.
private void PrintFriendships(ITransaction tx)
{
    var result = tx.Run("MATCH (a)-[:KNOWS]->(b) RETURN a.name, b.name");

    foreach (var record in result)
    {
        Console.WriteLine($"{record["a.name"]} knows {record["b.name"]}");
    }
}

public void AddEmployAndMakeFriends()
{
    // To collect the session bookmarks
    var savedBookmarks = new List<string>();

    // Create the first person and employment relationship.
    using (var session1 = Driver.Session(AccessMode.Write))
    {
        session1.WriteTransaction(tx => AddCompany(tx, "Wayne Enterprises"));
        session1.WriteTransaction(tx => AddPerson(tx, "Alice"));
        session1.WriteTransaction(tx => Employ(tx, "Alice", "Wayne Enterprises"));

        savedBookmarks.Add(session1.LastBookmark);
    }

    // Create the second person and employment relationship.
    using (var session2 = Driver.Session(AccessMode.Write))
    {
        session2.WriteTransaction(tx => AddCompany(tx, "LexCorp"));
        session2.WriteTransaction(tx => AddPerson(tx, "Bob"));
        session2.WriteTransaction(tx => Employ(tx, "Bob", "LexCorp"));

        savedBookmarks.Add(session2.LastBookmark);
    }

    // Create a friendship between the two people created above.
    using (var session3 = Driver.Session(AccessMode.Write, savedBookmarks))
    {
        session3.WriteTransaction(tx => MakeFriends(tx, "Alice", "Bob"));

        session3.ReadTransaction(PrintFriendships);
    }
}
```

+ Java

```
// Create a company node
private StatementResult addCompany( final Transaction tx, final String name )
{
    return tx.run( "CREATE (:Company {name: $name})", parameters( "name", name ) );
}

// Create a person node
private StatementResult addPerson( final Transaction tx, final String name )
{
    return tx.run( "CREATE (:Person {name: $name})", parameters( "name", name ) );
}

// Create an employment relationship to a pre-existing company node.
// This relies on the person first having been created.
private StatementResult employ( final Transaction tx, final String person, final String company )
{
    return tx.run( "MATCH (person:Person {name: $person_name}) " +
                    "MATCH (company:Company {name: $company_name}) " +
                    "CREATE (person)-[:WORKS_FOR]->(company)",
            parameters( "person_name", person, "company_name", company ) );
}

// Create a friendship between two people.
private StatementResult makeFriends( final Transaction tx, final String person1, final String person2 )
{
    return tx.run( "MATCH (a:Person {name: $person_1}) " +
                    "MATCH (b:Person {name: $person_2}) " +
                    "MERGE (a)-[:KNOWS]->(b)",
            parameters( "person_1", person1, "person_2", person2 ) );
}

// Match and display all friendships.
private StatementResult printFriends( final Transaction tx )
{
    StatementResult result = tx.run( "MATCH (a)-[:KNOWS]->(b) RETURN a.name, b.name" );
    while ( result.hasNext() )
    {
        Record record = result.next();
        System.out.println( String.format( "%s knows %s", record.get( "a.name" ).asString(), record.get( "b.name" ).toString() ) );
    }
    return result;
}

public void addEmployAndMakeFriends()
{
    // To collect the session bookmarks
    List<String> savedBookmarks = new ArrayList<>();

    // Create the first person and employment relationship.
    try ( Session session1 = driver.session( AccessMode.WRITE ) )
    {
        session1.writeTransaction( tx -> addCompany( tx, "Wayne Enterprises" ) );
        session1.writeTransaction( tx -> addPerson( tx, "Alice" ) );
        session1.writeTransaction( tx -> employ( tx, "Alice", "Wayne Enterprises" ) );

        savedBookmarks.add( session1.lastBookmark() );
    }

    // Create the second person and employment relationship.
    try ( Session session2 = driver.session( AccessMode.WRITE ) )
    {
        session2.writeTransaction( tx -> addCompany( tx, "LexCorp" ) );
        session2.writeTransaction( tx -> addPerson( tx, "Bob" ) );
        session2.writeTransaction( tx -> employ( tx, "Bob", "LexCorp" ) );

        savedBookmarks.add( session2.lastBookmark() );
    }

    // Create a friendship between the two people created above.
    try ( Session session3 = driver.session( AccessMode.WRITE, savedBookmarks ) )
    {
        session3.writeTransaction( tx -> makeFriends( tx, "Alice", "Bob" ) );

        session3.readTransaction( this::printFriends );
    }
}
```

+ JavaScript

```
// Create a company node
function addCompany(tx, name) {
  return tx.run('CREATE (a:Company {name: $name})', {'name': name});
}

// Create a person node
function addPerson(tx, name) {
  return tx.run('CREATE (a:Person {name: $name})', {'name': name});
}

// Create an employment relationship to a pre-existing company node.
// This relies on the person first having been created.
function addEmployee(tx, personName, companyName) {
  return tx.run('MATCH (person:Person {name: $personName}) ' +
    'MATCH (company:Company {name: $companyName}) ' +
    'CREATE (person)-[:WORKS_FOR]->(company)', {'personName': personName, 'companyName': companyName});
}

// Create a friendship between two people.
function makeFriends(tx, name1, name2) {
  return tx.run('MATCH (a:Person {name: $name1}) ' +
    'MATCH (b:Person {name: $name2}) ' +
    'MERGE (a)-[:KNOWS]->(b)', {'name1': name1, 'name2': name2});
}

// To collect friend relationships
const friends = [];

// Match and display all friendships.
function findFriendships(tx) {
  const result = tx.run('MATCH (a)-[:KNOWS]->(b) RETURN a.name, b.name');

  result.subscribe({
    onNext: record => {
      const name1 = record.get(0);
      const name2 = record.get(1);

      friends.push({'name1': name1, 'name2': name2});
    }
  });
}

// To collect the session bookmarks
const savedBookmarks = [];

// Create the first person and employment relationship.
const session1 = driver.session(neo4j.WRITE);
const first = session1.writeTransaction(tx => addCompany(tx, 'Wayne Enterprises')).then(
  () => session1.writeTransaction(tx => addPerson(tx, 'Alice'))).then(
  () => session1.writeTransaction(tx => addEmployee(tx, 'Alice', 'Wayne Enterprises'))).then(
  () => {
    savedBookmarks.push(session1.lastBookmark());

    return session1.close();
  });

// Create the second person and employment relationship.
const session2 = driver.session(neo4j.WRITE);
const second = session2.writeTransaction(tx => addCompany(tx, 'LexCorp')).then(
  () => session2.writeTransaction(tx => addPerson(tx, 'Bob'))).then(
  () => session2.writeTransaction(tx => addEmployee(tx, 'Bob', 'LexCorp'))).then(
  () => {
    savedBookmarks.push(session2.lastBookmark());

    return session2.close();
  });

// Create a friendship between the two people created above.
const last = Promise.all([first, second]).then(ignore => {
  const session3 = driver.session(neo4j.WRITE, savedBookmarks);

  return session3.writeTransaction(tx => makeFriends(tx, 'Alice', 'Bob')).then(
    () => session3.readTransaction(findFriendships).then(
      () => session3.close()
    )
  );
});
```

+ Python

```
class BookmarksExample(object):

    def __init__(self, uri, user, password):
        self._driver = GraphDatabase.driver(uri, auth=(user, password))

    def close(self):
        self._driver.close()

    # Create a person node.
    @classmethod
    def create_person(cls, tx, name):
        tx.run("CREATE (:Person {name: $name})", name=name)

    # Create an employment relationship to a pre-existing company node.
    # This relies on the person first having been created.
    @classmethod
    def employ(cls, tx, person_name, company_name):
        tx.run("MATCH (person:Person {name: $person_name}) "
               "MATCH (company:Company {name: $company_name}) "
               "CREATE (person)-[:WORKS_FOR]->(company)",
               person_name=person_name, company_name=company_name)

    # Create a friendship between two people.
    @classmethod
    def create_friendship(cls, tx, name_a, name_b):
        tx.run("MATCH (a:Person {name: $name_a}) "
               "MATCH (b:Person {name: $name_b}) "
               "MERGE (a)-[:KNOWS]->(b)",
               name_a=name_a, name_b=name_b)

    # Match and display all friendships.
    @classmethod
    def print_friendships(cls, tx):
        result = tx.run("MATCH (a)-[:KNOWS]->(b) RETURN a.name, b.name")
        for record in result:
            print("{} knows {}".format(record["a.name"] ,record["b.name"]))

    def main(self):
        saved_bookmarks = []  # To collect the session bookmarks

        # Create the first person and employment relationship.
        with self._driver.session() as session_a:
            session_a.write_transaction(self.create_person, "Alice")
            session_a.write_transaction(self.employ, "Alice", "Wayne Enterprises")
            saved_bookmarks.append(session_a.last_bookmark())

        # Create the second person and employment relationship.
        with self._driver.session() as session_b:
            session_b.write_transaction(self.create_person, "Bob")
            session_b.write_transaction(self.employ, "Bob", "LexCorp")
            saved_bookmarks.append(session_b.last_bookmark())

        # Create a friendship between the two people created above.
        with self._driver.session(bookmarks=saved_bookmarks) as session_c:
            session_c.write_transaction(self.create_friendship, "Alice", "Bob")
            session_c.read_transaction(self.print_friendships)
```
 
인과관계 클러스터 모드에서 작동하지 않는 데이터베이스의 북마크를 실행할 때 ```null``` 값을 반환됩니다. 

### 4.3.4. 접속 모드 

트랜잭션은 ```read```나 ```write```모드에서 실행할 수 있습니다. 인과 관계 클러스터에서 각 트랜잭션은 모드에 기반하여 적절한 서버에서 라우팅 됩니다. 단일 인스턴스를 사용할 때 모든 트랜잭션은 하나의 서버로 전송됩니다. 읽기 및 쓰기를 식별하여 Cypher을 라우팅하면 사용 가능한 클러스터 리소스 이용율을 개선시킬 수 있습니다: 일반적으로 쓰기 서버보다 읽기 서버가 많기 때문에 읽기 트랜잭션을 읽기 서버에 가능한 많이 다이렉트(direct)하는 것이 좋습니다. 이렇게하면 쓰기 트랙잭션이 쓰기 서버를 사용을 유지하는데 도움됩니다. 

접속 모드는 두 가지 모드를 제공합니다: 트랜잭션 및 세션 별. 세션 생성시 지정된 접속모드는 트랜잭션 모드로 대체할 수 있습니다. 일반적으로 접근 모드는 항상 트랜잭션 함수를 사용해서 트랜잭션 레벨로 명시해야합니다. 세션-레벨 설정은 명시적 및 자동-커밋 트랜잭션에만 필요합니다.  

드라이버는 Cypher를 파싱하지 않고 트랜잭션 읽기 및 쓰기 작업 수행 여부를 결정할 수 없습니다. 이 결과로 ```read```태그가 붙은 ```write``` 트랜잭션은 읽기 서버로 보내지지만 실행은 실패합니다. 

**예시 4.22. 읽기-쓰기 트랜잭션**

+ C#

```
public long AddPerson(string name)
{
    using (var session = Driver.Session())
    {
        session.WriteTransaction(tx => CreatePersonNode(tx, name));
        return session.ReadTransaction(tx => MatchPersonNode(tx, name));
    }
}

private static void CreatePersonNode(ITransaction tx, string name)
{
    tx.Run("CREATE (a:Person {name: $name})", new {name});
}

private static long MatchPersonNode(ITransaction tx, string name)
{
    var result = tx.Run("MATCH (a:Person {name: $name}) RETURN id(a)", new {name});
    return result.Single()[0].As<long>();
}
```

+ Java

```
public long addPerson( final String name )
{
    try ( Session session = driver.session() )
    {
        session.writeTransaction( new TransactionWork<Void>()
        {
            @Override
            public Void execute( Transaction tx )
            {
                return createPersonNode( tx, name );
            }
        } );
        return session.readTransaction( new TransactionWork<Long>()
        {
            @Override
            public Long execute( Transaction tx )
            {
                return matchPersonNode( tx, name );
            }
        } );
    }
}

private static Void createPersonNode( Transaction tx, String name )
{
    tx.run( "CREATE (a:Person {name: $name})", parameters( "name", name ) );
    return null;
}

private static long matchPersonNode( Transaction tx, String name )
{
    StatementResult result = tx.run( "MATCH (a:Person {name: $name}) RETURN id(a)", parameters( "name", name ) );
    return result.single().get( 0 ).asLong();
```

+ JavaScript

```
const session = driver.session();

const writeTxPromise = session.writeTransaction(tx => tx.run('CREATE (a:Person {name: $name})', {name: personName}));

writeTxPromise.then(() => {
  const readTxPromise = session.readTransaction(tx => tx.run('MATCH (a:Person {name: $name}) RETURN id(a)', {name: personName}));

  readTxPromise.then(result => {
    session.close();

    const singleRecord = result.records[0];
    const createdNodeId = singleRecord.get(0);

    console.log('Matched created node with id: ' + createdNodeId);
  });
});
```

+ Python

```
def add_person(self, name):
    with self._driver.session() as session:
        session.write_transaction(self.create_person_node, name)
        return session.read_transaction(self.match_person_node, name)

@staticmethod
def create_person_node(tx, name):
    tx.run("CREATE (a:Person {name: $name})", name=name)
    return None

@staticmethod
def match_person_node(tx, name):
    result = tx.run("MATCH (a:Person {name: $name}) RETURN count(a)", name=name)
    return result.single()[0]
```

### 4.3.5. 비동기 프로그래밍 

Java, .NET 및 JavaScript은 모두 비동기 프로그래밍을 지원합니다. 이 예는 Java 및 .NET이 블록 API와 함께 프로그래밍 모델을 제공하는 방법을 보여줍니다. 이전 보다 좋은 비동기 통합 어플리케이션을 허용하는 비동기 메소드도 있습니다. 비동기 메소드는 동기식에 대응하지만 접두사 *async* 가 추가됩니다. 
 
**예시 4.23. 비동기 프로그래밍 예시**
 
Java, .NET 및 JavaScript은 모두 비동기 프로그래밍을 지원합니다. 이 예는 Java 및 .NET이 블록 API와 함께 어떻게 프로그래밍 모델을 제공하는지에 대해 보여줍니다.  

이전 섹션에 언급한 방법외에 비동기 스타일로 적힌 더 나은 통합 어플리케이션을 허용하는 몇가지 비동기 메소드도 있습니다. 비동기 메소드는 동기식에 대응하여 이름이 지어지지만 접두사 *async* 가 추가됩니다. 
 
**예시 4.23. 비동기 프로그래밍 예씨**

+ C#

```
자동-커밋 트랜잭션.

var records = new List<string>();
var session = Driver.Session();

try
{
    // Send cypher statement to the database.
    // The existing IStatementResult interface implements IEnumerable
    // and does not play well with asynchronous use cases. The replacement
    // IStatementResultCursor interface is returned from the RunAsync
    // family of methods instead and provides async capable methods.
    var reader = await session.RunAsync(
        "MATCH (p:Product) WHERE p.id = $id RETURN p.title", // Cypher statement
        new { id = 0 } // Parameters in the statement, if any
    );

    // Loop through the records asynchronously
    while (await reader.FetchAsync())
    {
        // Each current read in buffer can be reached via Current
        records.Add(reader.Current[0].ToString());
    }
}
finally
{
    // asynchronously close session
    await session.CloseAsync();
}
```

**트랜잭션 함수**

```
var session = Driver.Session();

try
{
    // Wrap whole operation into an implicit transaction and
    // get the results back.
    result = await session.ReadTransactionAsync(async tx =>
    {
        var records = new List<string>();

        // Send cypher statement to the database
        var reader = await tx.RunAsync(
            "MATCH (p:Product) WHERE p.id = $id RETURN p.title", // Cypher statement
            new { id = 0 } // Parameters in the statement, if any
        );

        // Loop through the records asynchronously
        while (await reader.FetchAsync())
        {
            // Each current read in buffer can be reached via Current
            records.Add(reader.Current[0].ToString());
        }

        return records;
    });
}
finally
{
    // asynchronously close session
    await session.CloseAsync();
}
```

**명백한 트랜잭션**

```
var records = new List<string>();
var session = Driver.Session();

try
{
    // Start an explicit transaction
    var tx = await session.BeginTransactionAsync();

    // Send cypher statement to the database through the explicit
    // transaction acquired
    var reader = await tx.RunAsync(
        "MATCH (p:Product) WHERE p.id = $id RETURN p.title", // Cypher statement
        new { id = 0 } // Parameters in the statement, if any
    );

    // Loop through the records asynchronously
    while (await reader.FetchAsync())
    {
        // Each current read in buffer can be reached via Current
        records.Add(reader.Current[0].ToString());
    }

    // Commit the transaction
    await tx.CommitAsync();
}
finally
{
    // asynchronously close session
    await session.CloseAsync();
}
```

+ Java 

**자동-커밋 트랜잭션**

```
String query = "MATCH (p:Product) WHERE p.id = $id RETURN p.title";
Map<String,Object> parameters = Collections.singletonMap( "id", 0 );

Session session = driver.session();

return session.runAsync( query, parameters )
        .thenCompose( cursor -> cursor.listAsync( record -> record.get( 0 ).asString() ) )
        .exceptionally( error ->
        {
            // query execution failed, print error and fallback to empty list of titles
            error.printStackTrace();
            return Collections.emptyList();
        } )
        .thenCompose( titles -> session.closeAsync().thenApply( ignore -> titles ) );
```

**트랜잭션 함수**

```
String query = "MATCH (p:Product) WHERE p.id = $id RETURN p.title";
Map<String,Object> parameters = Collections.singletonMap( "id", 0 );

Session session = driver.session();

return session.readTransactionAsync( tx ->
        tx.runAsync( query, parameters )
                .thenCompose( cursor -> cursor.forEachAsync( record ->
                        // asynchronously print every record
                        System.out.println( record.get( 0 ).asString() ) ) )
);
```

**명백한 트랜잭션**

```
String query = "MATCH (p:Product) WHERE p.id = $id RETURN p.title";
Map<String,Object> parameters = Collections.singletonMap( "id", 0 );

Session session = driver.session();

Function<Transaction,CompletionStage<Void>> printSingleTitle = tx ->
        tx.runAsync( query, parameters )
                .thenCompose( StatementResultCursor::singleAsync )
                .thenApply( record -> record.get( 0 ).asString() )
                .thenApply( title ->
                {
                    // single title fetched successfully
                    System.out.println( title );
                    return true; // signal to commit the transaction
                } )
                .exceptionally( error ->
                {
                    // query execution failed
                    error.printStackTrace();
                    return false; // signal to rollback the transaction
                } )
                .thenCompose( commit -> commit ? tx.commitAsync() : tx.rollbackAsync() );

return session.beginTransactionAsync()
        .thenCompose( printSingleTitle )
        .exceptionally( error ->
        {
            // either commit or rollback failed
            error.printStackTrace();
            return null;
        } )
        .thenCompose( ignore -> session.closeAsync() );
```

세션을 닫아서 얻어진 모든 리소스(네트워크 연결과 같은)가 정리되었는지 확인해야 합니다. 그러므로 항상 ```CompletionStage``` 세션 끝에서 ```Session#closeAsync()```을 하는 것을 권장합니다. 세션은 정상/비정상적으로 완료된 것과는 관계없이 종료해야 합니다. 또한, 세션 종료는 세션 끝에서 커밋되지 않거나 실패한 트랜잭션을 롤백합니다. 따라서 세션이 풀 체인 끝에 종료되어 있는 한 ```Transaction#rollbackAsync()```을 사용해서 트랜잭션 롤백을 하는 것은 선택사항 입니다. 