## 4.1. 시작하기 

```
이 섹션에서는 공식 Neo4j 드라이버 개요 및 Neo4j 데이터 베이스를 예시 "Hello World" 와 연결하는 방법에 대해 다룹니다. 
```

### 4.1.1. 공식 드라이버 

공식 및 커뮤니티 데이터베이스 드라이버는 Neo4j에 대한 어플리케이션 액세스를 제공합니다. 
공식 드라이버는 다음에서 사용됩니다:

+ C# — 모든 .NET 언어와 작동
+ Java — 모든 JVM 언어와 작동 
+ JavaScript
+ Python

드라이버 API는 도구적으로 제한되지 않습니다. 이를 통해 기본 데이터 베이스 토플로지( 단일 인스턴스, 인과 클러스터 등)는 어플리케이션 코드를 수정하지 않고도 변경할 수 있습니다. 일반적인 케이스에서는 토플로지에 변화가 있을 때만 연결 URI을 수정해야 합니다. 

공식 드라이버는 HTTP 통신을 지원하지 않습니다. HTTP 드라이버가 필요하다면 선택할 수 있는 커뮤니티 드라이버가 많이 있습니다. 드라이버 API는 도구적으로 불가지론적인 것으로 의도되었습니다. 이를 통해 기본 데이터 베이스 토플로지( 단일 인스턴스, 인과 클러스터 등)는 어플리케이션 코드를 수정하지 않고도 변경할 수 있습니다. 일반적인 경우 토플로지에 변화가 있을 때만 연결 URI를 수정해야 합니다. 

공식 드라이버는 HTTP 통신을 지원하지 않습니다. HTTP 드라이버가 필요할 때 선택할 수 있는 커뮤니티 드라이버가 많이 있습니다. 

### 4.1.2. 드라이버 버전 및 설치 

1.x 서비스 드라이버는 Neo4j 3.x를 위해 생성되었습니다. 주요 드라이버 버전 숫자는 볼트 프로토콜 버전과 연관이 있고, 부 버전 번호는 드라이버 기능 세트를 나타내고 패치 번호는 일반적인 드라이버 패치 레벨을 정의합니다. 드라이버의 부 버전이 모든 언어에서 동시에 출시되는 동안 패치 레벨은 변할 수도 있습니다. 

주요 시리즈에서 최근 출시된 드라이버를 사용할 것을 권장합니다. 이를 통해 클라이언트 어플리케이션에서 모든 서버 기능을 사용할 수 있습니다. 드라이버를 설치하거나 이용 가능한 드라이버 버전을 알아낼 때 관련 언어 배포 시스템을 사용하면 됩니다. 주요 시리즈에서는 최근 출시된 드라이버 사용을 권장합니다. 이를 통해 클라이언트 어플리케이션에서 모든 서버 기능을 사용할 수 있습니다. 드라이버를 설치하거나 이용 가능한 드라이버 버전을 알아낼 때 관련 언어 배포 시스템을 사용하면 됩니다. 

**예시 4.1. 드라이버 얻기**

+ C#

최신 드라이버 버전은 [https://www.nuget.org/packages/Neo4j.Driver](https://www.nuget.org/packages/Neo4j.Driver)에서 확인할 수 있습니다. 

Visual Studio에 NuGet을 사용해서 최신 드라이버를 설치하려면:

```
PM> Install-Package Neo4j.Driver
```

드라이버의 특정 버전을 설치하기 위해서 선택할 수도 있습니다. 

아래는 드라이버의 특정 버전을 설치하기 위한 구문입니다.  

```
PM> Install-Package Neo4j.Driver -Version $DOTNET_DRIVER_VERSION
```

아래는 드라이버 1.6.0 버전을 설치하는 예입니다. 

```
PM> Install-Package Neo4j.Driver -Version 1.6.0
```

이 드라이버와 관련된 릴리즈 노트를 검토할 수 있습니다.[참조](https://github.com/neo4j/neo4j-dotnet-driver/releases).

+ Java

최신 드라이버 버전은 [maven 저장소](https://search.maven.org/#search%7Cgav%7C1%7Cg%3A%22org.neo4j.driver%22%20AND%20a%3A%22neo4j-java-driver%22)에서 확인할 수 있습니다. 

**예시 4.2. Maven을 이용해서 Java 드라이버 설치**

Maven을 사용할 때 *pom.xml* 파일에 다음 블록을 추가합니다. 드라이버 버전의 플레이스 홀더(placeholder)에 유의해야 합니다. 설치하려는 패치 버전을 정확히 알아야 합니다. 

```
<dependencies>
    <dependency>
        <groupId>org.neo4j.driver</groupId>
        <artifactId>neo4j-java-driver</artifactId>
        <version>$JAVA_DRIVER_VERSION</version>
    </dependency>
</dependencies>
```

아래는 드라이버 버전 1.6.1을 추가하는 예 입니다.  

```
<dependencies>
    <dependency>
        <groupId>org.neo4j.driver</groupId>
        <artifactId>neo4j-java-driver</artifactId>
        <version>1.6.1</version>
    </dependency>
</dependencies>
```

**예시 4.3. Gradle 또는 Grails용 Java 드라이버에 의존성 추가**

아래는 Gradle 또는 Grails에 의존성을 추가하는 방법입니다. 드라이버 버전의 플레이스 홀더(placeholder)에 유의해야 합니다. 설치하는 것의 패치 버전을 정확히 확인해야 합니다. 

```
compile 'org.neo4j.driver:neo4j-java-driver:$JAVA_DRIVER_VERSION'
```

아래는 드라이버 버전 1.6.1.을 추가하는 예입니다. 

```
compile 'org.neo4j.driver:neo4j-java-driver:1.6.1'
```

이 드라이버와 관련된 릴리즈 노트를 확인할 수 있습니다. [참조](https://github.com/neo4j/neo4j-java-driver/releases).

+ JavaScript

드라이버 최신 버전을 찾을 때 ```npm```을 사용합니다:
```
npm show neo4j-driver@* version
```

드라이버의 최신 버전을 설치할 떄:
```
npm install neo4j-driver
```

드라이버의 특정 설치 버전을 선택할 수도 있습니다. 

아래는 특정 드라이버 버전을 설치할 때 사용하는 구문입니다. 
```
npm install neo4j-driver@$JAVASCRIPT-DRIVER-VERSION
```

아래는 드라이버 1.6.1을 설치하는 예 입니다. 
```
npm install neo4j-driver@1.6.1
```

이 드라이버의 릴리즈 노트는 다음에서 확인할 수 있습니다. [참조](https://github.com/neo4j/neo4j-javascript-driver/releases).

+ Python

드라이버의 최신 버전은 [https://pypi.python.org/pypi/neo4j-driver](https://pypi.python.org/pypi/neo4j-driver)에서 확인할 수 있습니다. 

드라이버의 최신 버전을 설치하려면:
```
pip install neo4j-driver
```

드라이버의 특정 설치 버전을 선택할 수도 있습니다. 

아래는 특정 드라이버 버전을 설치할 때 사용하는 구문입니다. 
```
pip install neo4j-driver==$PYTHON_DRIVER_VERSION
```

아래는 드라이버 1.6.0을 설치하는 예 입니다. 
```
pip install neo4j-driver==1.6.0rc1
```

이 드라이버의 릴리즈 노트는 다음에서 확인할 수 있습니다. [참조](https://github.com/neo4j/neo4j-python-driver/releases).

### 4.1.3. 지원 언어 및 프레임워크 버전

각 언어/프레임워크에는 Neo4j가 지원하는 공식 버전이 많습니다. 

**테이블 4.1. 1.x 드라이버 시리즈에서 지원하는 언어 및 프레임 워크**

| 언어/프레임워크 | 지원되는 버전                                                |
| --------------- | ------------------------------------------------------------ |
| Java            | Oracle JDK 7/8 및 오픈 JDK 7/8 (최근 출시된 패치)            |
| Python          | CPython 2.7, 3.4, 3.5 및 3.6 (l최근 출시된 패치)             |
| JavaScript      | 드라이버는 Node.JS의 모든 LTS 버전특히 4.x와 6.x시리즈 런타임에서  작업하도록 제작되었습니다. (참조 [https://github.com/nodejs/LTS](https://github.com/nodejs/LTS)). |
| .NET            | 드라이버는 .NET 표준 1.3을 목표로합니다. (참조 [https://github.com/dotnet/standard/blob/master/docs/versions.md](https://github.com/dotnet/standard/blob/master/docs/versions.md)). |


### 4.1.4. "Hello World" 예시

아래 예는 드라이버로 Neo4j와 상호작용하는데 필요한 최소 설정을 나타냅니다. 

**예시 4.4. Hello World**

+ C#
```
public class HelloWorldExample : IDisposable
{
    private readonly IDriver _driver;

    public HelloWorldExample(string uri, string user, string password)
    {
        _driver = GraphDatabase.Driver(uri, AuthTokens.Basic(user, password));
    }

    public void PrintGreeting(string message)
    {
        using (var session = _driver.Session())
        {
            var greeting = session.WriteTransaction(tx =>
            {
                var result = tx.Run("CREATE (a:Greeting) " +
                                    "SET a.message = $message " +
                                    "RETURN a.message + ', from node ' + id(a)",
                    new {message});
                return result.Single()[0].As<string>();
            });
            Console.WriteLine(greeting);
        }
    }

    public void Dispose()
    {
        _driver?.Dispose();
    }

    public static void Main()
    {
        using (var greeter = new HelloWorldExample("bolt://localhost:7687", "neo4j", "password"))
        {
            greeter.PrintGreeting("hello, world");
        }
    }
}
```

+ Java

```
public class HelloWorldExample implements AutoCloseable
{
    private final Driver driver;

    public HelloWorldExample( String uri, String user, String password )
    {
        driver = GraphDatabase.driver( uri, AuthTokens.basic( user, password ) );
    }

    @Override
    public void close() throws Exception
    {
        driver.close();
    }

    public void printGreeting( final String message )
    {
        try ( Session session = driver.session() )
        {
            String greeting = session.writeTransaction( new TransactionWork<String>()
            {
                @Override
                public String execute( Transaction tx )
                {
                    StatementResult result = tx.run( "CREATE (a:Greeting) " +
                                                     "SET a.message = $message " +
                                                     "RETURN a.message + ', from node ' + id(a)",
                            parameters( "message", message ) );
                    return result.single().get( 0 ).asString();
                }
            } );
            System.out.println( greeting );
        }
    }

    public static void main( String... args ) throws Exception
    {
        try ( HelloWorldExample greeter = new HelloWorldExample( "bolt://localhost:7687", "neo4j", "password" ) )
        {
            greeter.printGreeting( "hello, world" );
        }
    }
}
```

+ JavaScript
```
const driver = neo4j.driver(uri, neo4j.auth.basic(user, password));
const session = driver.session();

const resultPromise = session.writeTransaction(tx => tx.run(
  'CREATE (a:Greeting) SET a.message = $message RETURN a.message + ", from node " + id(a)',
  {message: 'hello, world'}));

resultPromise.then(result => {
  session.close();

  const singleRecord = result.records[0];
  const greeting = singleRecord.get(0);

  console.log(greeting);

  // on application exit:
  driver.close();
});
```

+ Python
```
class HelloWorldExample(object):

    def __init__(self, uri, user, password):
        self._driver = GraphDatabase.driver(uri, auth=(user, password))

    def close(self):
        self._driver.close()

    def print_greeting(self, message):
        with self._driver.session() as session:
            greeting = session.write_transaction(self._create_and_return_greeting, message)
            print(greeting)

    @staticmethod
    def _create_and_return_greeting(tx, message):
        result = tx.run("CREATE (a:Greeting) "
                        "SET a.message = $message "
                        "RETURN a.message + ', from node ' + id(a)", message=message)
```

### 4.1.5. 드라이버 API 문서

모든 드라이버 기능의 전체 리스트는 특정 언어 드라이버의 API문서를 참조하면 됩니다. 

**예시 4.5. API 문서**

+ C#
    [https://neo4j.com/docs/api/dotnet-driver/1.6/](https://neo4j.com/docs/api/dotnet-driver/1.6/)
+ Java
    [https://neo4j.com/docs/api/java-driver/1.5/](https://neo4j.com/docs/api/java-driver/1.5/)
+ JavaScript
    [https://neo4j.com/docs/api/javascript-driver/1.6/](https://neo4j.com/docs/api/javascript-driver/1.6/)    
+ Python
    [https://neo4j.com/docs/api/python-driver/1.6/](https://neo4j.com/docs/api/python-driver/1.6/)
