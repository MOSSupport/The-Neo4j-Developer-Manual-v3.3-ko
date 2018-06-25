## 4.4. Cypher 값을 사용한 작업

```
이 섹션에서는 Cypher에서 사용하는 유형 및 값과 모국어 유형별로 매핑되는 방법에 대해서 알아봅니다. 
```


### 4.4.1. Cypher 유형 시스템 

드라이버는 어플리케이션 언어 유형과 Cypher 유형 시스템 간에 번환합니다. 변수 및 진행 결과를 전달하려면 Cypher가 유형별로 작동하는 방식의 기본 사항을 알고 Cypher 유형이 드라이버에 매핑되는 방식을 이해하는 것이 중요합니다. 

아래 테이블은 이용 가능한 데이터 유형을 나타냅니다. 모든 변수가 쓰이지 않더라도 결과에서 잠재적으로 모두 확인할 수 있습니다. 

| Cypher 유형      | 매개 변수 | 결과 |
| ---------------- | --------- | ---- |
| ```null```*          | ✔         | ✔    |
| ```List```           | ✔         | ✔    |
| ```Map```            | ✔         | ✔    |
| ```Boolean```        | ✔         | ✔    |
| ```Integer```        | ✔         | ✔    |
| ```Float```          | ✔         | ✔    |
| ```String```         | ✔         | ✔    |
| ```ByteArray```      | ✔         | ✔    |
| ```Date```           | ✔         | ✔    |
| ```Time```           | ✔         | ✔    |
| ```LocalTime```      | ✔         | ✔    |
| ```DateTime```       | ✔         | ✔    |
| ```LocalDateTime```  | ✔         | ✔    |
| ```Duration```       | ✔         | ✔    |
| ```Point```          | ✔         | ✔    |
| ```Node```**         |           | ✔    |
| ```Relationship```** |           | ✔    |
| ```Path```**         |           | ✔    |


null 표시는 유형이 아니지만 값의 부재를 나타내는 위치 지정자입니다. 

노드, 관계 및 경로는 원래 그래프 속성의 스냅샷으로 결과에 전송됩니다. 원본 IDs 속성이 이 스냅샷에 추가되어 있지만 클라이언트 복사본과는 별도로 삭제되거나 다른 방식으로 변경될 수 있는 기본 서버 속성에는 영구 링크가 유지되지 않습니다. 그래프 구조는 어플리케이션 내용에 따라 변수가 참조되거나 값으로 보내는 여부에 따라 다르기 때문에 매개 변수로 사용되지 않을 것 입니다. 그리고 Cypher은 이 값을 나타내는 메커니즘을 제공하지 않습니다. 동일한 기능은 ID또는 참조 또는 값 별 전달 속성의 값 별 추출된 앱을 전달해서 이용할수 있습니다. 

Neo4j 드라이버는 아래 테이블에 묘사된 것 같이 유형을 모국어 유형을 Cypher에 맵핑합니다. 고객 유형(언어나 표준 라이브러리에서 이용할 수 없는)은 **bold**로 표시됩니다.

**예시 4.24. Neo4j 유형을 모국어로 맵핑**

+ C#

| Neo4j 유형          | .NET 유형                         |
| ------------------- | --------------------------------- |
| ```null```          | ```null```                        |
| ```List```          | ```IList<object>```               |
| ```Map```           | ```IDictionary<string, object>``` |
| ```Boolean```       | ```bool```                        |
| ```Integer```       | ```long```                        |
| ```Float```         | ```double```                      |
| ```String```        | ```string```                      |
| ```ByteArray```     | ```byte[]```                      |
| ```Date```          | ```LocalDate```                   |
| `Time`              | ```OffsetTime```                  |
| ```LocalTime```     | ```LocalTime```                   |
| ```DateTime```      | ```ZonedDateTime```               |
| ```LocalDateTime``` | ```LocalDateTime```               |
| ```Duration```      | ```Duration```                    |
| ```Point```         | ```Point```                       |
| ```Node```          | ```INode```                       |
| ```Relationship```  | ```IRelationship```               |
| ```Path```          | ```IPath```                       |


타임존 명은 Windows 시스템보다는 IANA 시스템을 준수합니다. 인바운드 변환은 유니코드 CLDR에 정의된 Windows-Olson 매핑을 사용하여 수행됩니다. 

+ Java

 | Neo4j 유형          | Java 유형                 |
| ------------------- | ------------------------- |
| ```null```          | ```null```                |
| ```List```          | ```List<Object>```        |
| ```Map```           | ```Map<String, Object>``` |
| ```Boolean```       | ```boolean```             |
| ```Integer```       | ```long```                |
| ```Float```         | ```double```              |
| ```String```        | ```String```              |
| ```ByteArray```     | ```byte[]```              |
| ```Date```          | ```LocalDate```           |
| ```Time```          | ```OffsetTime```          |
| ```LocalTime```     | ```LocalTime```           |
| ```DateTime```      | ```ZonedDateTime```       |
| ```LocalDateTime``` | ```LocalDateTime```       |
| ```Duration```      | ```IsoDuration```         |
| ```Point```         | ```Point```               |
| ```Node```          | ```Node```                |
| ```Relationship```  | ```Relationship```        |
| ```Path```          | ```Path```                |

변수로 전해진 ```Duration```이나 ```Period```은 항상 ```IsoDuration```으로 변환될 것 입니다. 

+ JavaScript

| Neo4j 유형          | JavaScript 유형     |
| ------------------- | ------------------- |
| ```null```          | ```null```          |
| ```List```          | ```Array```         |
| ```Map```           | ```Object```        |
| ```Boolean```       | ```Boolean```       |
| ```Integer```       | ```Integer*```      |
| ```Float```         | ```Number```        |
| ```String```        | ```String```        |
| ```ByteArray```     | ```Int8Array```     |
| ```Date```          | ```Date```          |
| ```Time```          | ```Time```          |
| ```LocalTime```     | ```LocalTime```     |
| ```DateTime```      | ```DateTime```      |
| ```LocalDateTime``` | ```LocalDateTime``` |
| ```Duration```      | ```Duration```      |
| ```Point```         | ```Point```         |
| ```Node```          | ```Node```          |
| ```Relationship```  | ```Relationship```  |
| ```Path```          | ```Path```          |


JavaScript는 원시 정수 유형이 없으므로 커스튬 정의 타입이 제공됩니다. 편의상 이 기능을 설정에서 비활성화여 기본 Number 유형 대신 사용할 수 있습니다. 이 경우 정밀성이 떨어질 수 있다는 점에 유의하십시오. 

+ Python

| Neo4j 유형          | Python 2 유형            | Python 3 유형            |
| ------------------- | ------------------------ | ------------------------ |
| ```null```          | ```None```               | ```None```               |
| ```List```          | ```list```               | ```list```               |
| ```Map```           | ```dict```               | ```dict```               |
| ```Boolean```       | ```bool```               | ```bool```               |
| ```Integer```       | ```int / long```*        | ```int```                |
| ```Float```         | ```float```              | ```float```              |
| ```String```        | ```unicode```**          | ```str```                |
| ```ByteArray```     | ```bytearray```          | ```bytearray```          |
| ```Date```          | ```neotime.Date```       | ```neotime.Date```       |
| ```Time```          | ```neotime.Time†```      | ```neotime.Time†```      |
| ```LocalTime```     | ```neotime.Time††```     | ```neotime.Time††```     |
| ```DateTime```      | ```neotime.DateTime†```  | ```neotime.DateTime†```  |
| ```LocalDateTime``` | ```neotime.DateTime††``` | ```neotime.DateTime††``` |
| ```Duration```      | ```neotime.Duration```   | ```neotime.Duration```   |
| ```Point```         | ```Point```              | ```Point```              |
| ```Node```          | ```Node```               | ```Node```               |
| ```Relationship```  | ```Relationship```       | ```Relationship```       |
| ```Path```          | ```Path```               | ```Path```               |

Cypher 가 64-bit 부호 정수를 사용하지만 int는 Python 2의 sys.maxint까지만 정수를 저장할 수 있습니다. 이 값보다 긴 경우, long이 사용됩니다. 
Phtyon 2에서, 변수로 전해진 ```str```은 UTF-8을 통해서 항상 ```unicode```로 변환될 것 입니다.
변수로 전해진 ```timedelta```는 항상 ```neotime.Duration```으로 전달될 것 입니다. 

† 여기서 tzinfo는 None이 아닙니다.
†† 여기서 tzinfo는 None 입니다.
 
### 4.4.2. 명령어 결과

명령어 결과는 레코드 스트림으로 구성됩니다. 일반적으로 결과는 수신 어플리케이션에서 처리되지만 결과의 모든 또는 일부분은 추후에 사용할 수 있도록 유지할 수 있습니다. 

4.2. 결과 스트림

[드라이버 결과 스트림]( ---이미지 넣기!!!

#### 4.4.2.1. 레코드

레코드는 결과 일부분의 불변한 뷰를 제공합니다. 이것은 키와 값의 정렬된 맵입니다. 기록 내 값이 키순으로 정렬되어 있으므로 해당 값은 위치(0부터 시작하는 정수) 또는 키(문자열)로 접속할 수 있습니다. 

#### 4.4.2.2. 버퍼

4.3. 결과 버퍼

![드라이버 결과 버퍼](https://neo4j.com/docs/developer-manual/3.4/images/driver-result-buffer.svg)

대부분 드라이버는 결과 버퍼를 포함합니다. 결과에 대한 단계 포인트를 제공하고 결과 처리를 *페치* (네트워크에서 버퍼로 이동) 및 *소비* (버퍼에서 응용 프로그램으로 이동)로 나눕니다. 

만약 결과가 생산된 순서대로 사용되면 레코드는 단지 버퍼로 통과합니다. 순서가 바뀌어 사영된다면 버퍼는 어플리케이션에서 사용될 때 까지 레코드를 유지하는데 사용됩니다. 많은 결과를 얻으려면 대용량의 메모리와 성능에 영향을 줄 수 있습니다. 이런 이유로, 가능한 곳 어디든 결과를 사용하도록 권장합니다. 


#### 4.4.2.3. 스트림 사용

쿼리 결과는 종종 스트림으로 사용될 수 있습니다. 드라이버는 결과 스트림을 반복하기 위해 관용적 언어를 제공합니다. 


**예시 4.25. 스트림 사용**

+ C#

```
public List<string> GetPeople()
{
    using (var session = Driver.Session())
    {
        return session.ReadTransaction(tx =>
        {
            var result = tx.Run("MATCH (a:Person) RETURN a.name ORDER BY a.name");
            return result.Select(record => record[0].As<string>()).ToList();
        });
    }
}
```

+ Java

```
public List<String> getPeople()
{
    try ( Session session = driver.session() )
    {
        return session.readTransaction( new TransactionWork<List<String>>()
        {
            @Override
            public List<String> execute( Transaction tx )
            {
                return matchPersonNodes( tx );
            }
        } );
    }
}

private static List<String> matchPersonNodes( Transaction tx )
{
    List<String> names = new ArrayList<>();
    StatementResult result = tx.run( "MATCH (a:Person) RETURN a.name ORDER BY a.name" );
    while ( result.hasNext() )
    {
        names.add( result.next().get( 0 ).asString() );
    }
    return names;
}
```

+ JavaScript

```
const session = driver.session();
const result = session.run('MATCH (a:Person) RETURN a.name ORDER BY a.name');
const collectedNames = [];

result.subscribe({
  onNext: record => {
    const name = record.get(0);
    collectedNames.push(name);
  },
  onCompleted: () => {
    session.close();

    console.log('Names: ' + collectedNames.join(', '));
  },
  onError: error => {
    console.log(error);
  }
});
```

+ Python

```
def get_people(self):
    with self._driver.session() as session:
        return session.read_transaction(self.match_person_nodes)

@staticmethod
def match_person_nodes(tx):
    result = tx.run("MATCH (a:Person) RETURN a.name ORDER BY a.name")
    return [record["a.name"] for record in result]
```


#### 4.4.2.4. 결과 유지

결과 레코드 스트림은 세션에서 다른 명령문이 실행되거나 현재 트랜잭션이 닫힐 때까지 이용 가능합니다. 이 범위를 넘어서 결과를 유지하려면, 결과는 명시적으로 유지되어야 합니다. 결과를 유지하려면 각  드라이버는 결과 스트림을 수집하고 해당 언어의 표준 데이터 구조로 번환하는 메소드를 제공합니다.세션은 다음 워크로드를 자유롭게 사용할 수 있는 동안 저장된 결과를 진행할 수 있습니다. 저장된 결과는 새로운 명령문을 실행할 때 직접 사용될 수도 있습니다. 

예시 4.26. 추가 처리를 위한 결과 유지 
 
+ C#

```
public int AddEmployees(string companyName)
{
    using (var session = Driver.Session())
    {
        var persons = session.ReadTransaction(tx => tx.Run("MATCH (a:Person) RETURN a.name AS name").ToList());
        return persons.Sum(person => session.WriteTransaction(tx =>
        {
            tx.Run("MATCH (emp:Person {name: $person_name}) " +
                "MERGE (com:Company {name: $company_name}) " +
                "MERGE (emp)-[:WORKS_FOR]->(com)",
                new {person_name = person["name"].As<string>(), company_name = companyName});
            return 1;
        }));
    }
}
```

+ Java

```
public int addEmployees( final String companyName )
{
    try ( Session session = driver.session() )
    {
        int employees = 0;
        List<Record> persons = session.readTransaction( new TransactionWork<List<Record>>()
        {
            @Override
            public List<Record> execute( Transaction tx )
            {
                return matchPersonNodes( tx );
            }
        } );
        for ( final Record person : persons )
        {
            employees += session.writeTransaction( new TransactionWork<Integer>()
            {
                @Override
                public Integer execute( Transaction tx )
                {
                    tx.run( "MATCH (emp:Person {name: $person_name}) " +
                            "MERGE (com:Company {name: $company_name}) " +
                            "MERGE (emp)-[:WORKS_FOR]->(com)",
                            parameters( "person_name", person.get( "name" ).asString(), "company_name",
                                    companyName ) );
                    return 1;
                }
            } );
        }
        return employees;
    }
}

private static List<Record> matchPersonNodes( Transaction tx )
{
    return tx.run( "MATCH (a:Person) RETURN a.name AS name" ).list();
}
```

+ JavaScript

```
const session = driver.session();

const readTxPromise = session.readTransaction(tx => tx.run('MATCH (a:Person) RETURN a.name AS name'));

const addEmployeesPromise = readTxPromise.then(result => {
  const nameRecords = result.records;

  let writeTxsPromise = Promise.resolve();
  for (let i = 0; i < nameRecords.length; i++) {
    const name = nameRecords[i].get('name');

    writeTxsPromise = writeTxsPromise.then(() =>
      session.writeTransaction(tx =>
        tx.run(
          'MATCH (emp:Person {name: $person_name}) ' +
          'MERGE (com:Company {name: $company_name}) ' +
          'MERGE (emp)-[:WORKS_FOR]->(com)',
          {'person_name': name, 'company_name': companyName})));
  }

  return writeTxsPromise.then(() => nameRecords.length);
});

addEmployeesPromise.then(employeesCreated => {
  session.close();
  console.log('Created ' + employeesCreated + ' employees');
```

+ Python

```
def add_employees(self, company_name):
    with self._driver.session() as session:
        employees = 0
        persons = session.read_transaction(self.match_person_nodes)

        for person in persons:
            employees += session.write_transaction(self.add_employee_to_company, person, company_name)

        return employees

@staticmethod
def add_employee_to_company(tx, person, company_name):
    tx.run("MATCH (emp:Person {name: $person_name}) "
           "MERGE (com:Company {name: $company_name}) "
           "MERGE (emp)-[:WORKS_FOR]->(com)",
           person_name=person["name"], company_name=company_name)
    return 1

@staticmethod
def match_person_nodes(tx):
    return list(tx.run("MATCH (a:Person) RETURN a.name AS name"))
```

### 4.4.3. 명령문 결과 요약

쿼리 통계, 타이밍 및 서버 정보와 같은 추가 정보는 쿼리 결과 요약에서 얻을 수 있습니다. 만약 이 상세 정보가 전체 결과가 사용되기 전에 억세스 된다면, 나머지 결과는 버퍼링 됩니다. 


[특정 언어 드라이버  API 문서](./get-started.md)를 참조하십시오.