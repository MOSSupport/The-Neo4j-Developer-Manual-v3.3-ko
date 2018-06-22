## 4.2. 클라이언트 어플리케이션

```
이 섹션에서는 어플리케이션에서 데이터베이스에 연결하는 방법에 대해 다룹니다. 
```

### 4.2.1. 드라이버 객체

Neo4j 클라이언트 어플리케이션은 데이터 베이스 접근 엑서스를 제공하기 위해 드라이버 객체 인스턴스를 필요로합니다. 객체 인스턴스는 Neo4j와 상호작용하는 어플리케이션의 모든 부분에 사용할 수 있어야 합니다. [스레드 안전성](https://neo4j.com/docs/developer-manual/3.4/terminology)이 중요한 언어에서 드라이버는 스레드(thread)로부터 안전하다고할 수 있습니다.

**라이프 사이클 노트**

일반적으로 어플리케이션은 시작할 때 드라이버 인스턴스를 추가하고 종료할 때 삭제합니다. 드라이버 인스턴스를 제거하면 드라이버에서 열린 모든 연결이 즉시 종료됩니다. 연결 풀이있는 드라이버는 모든 풀이 종료됩니다. 

드라이버 인스턴스를 생성하려면 연결 URI와 인증 정보를 제공해야 합니다. 필요하다면 추가 설정 정보를 제공할 수 있습니다. 이 모든 세부사항은 드라이버 생애동안 변경할 수 없습니다. 그러므로, (다양한 데이터 베이스와 작업하는 것과 같이) 다양한 설정이 필요할 경우 다양한 드라이버 객체를 사용해야 됩니다. 

드라이버 생성 및 파기의 예는 다음과 같습니다. 

**예시 4.6. 드라이버 수명**

+ C#

```
public class DriverLifecycleExample : IDisposable
{
    public IDriver Driver { get; }

    public DriverLifecycleExample(string uri, string user, string password)
    {
        Driver = GraphDatabase.Driver(uri, AuthTokens.Basic(user, password));
    }

    public void Dispose()
    {
        Driver?.Dispose();
    }
}
```

+ Java

```
public class DriverLifecycleExample implements AutoCloseable
{
    private final Driver driver;

    public DriverLifecycleExample( String uri, String user, String password )
    {
        driver = GraphDatabase.driver( uri, AuthTokens.basic( user, password ) );
    }

    @Override
    public void close() throws Exception
    {
        driver.close();
    }
}
```

+ JavaScript

```
const driver = neo4j.driver(uri, neo4j.auth.basic(user, password));

driver.onCompleted = () => {
  console.log('Driver created');
};

driver.onError = error => {
  console.log(error);
};

const session = driver.session();
session.run('CREATE (i:Item)').then(() => {
  session.close();

  // ... on application exit:
  driver.close();
});
```

+ Python

```
class DriverLifecycleExample:
    def __init__(self, uri, user, password):
        self._driver = GraphDatabase.driver(uri, auth=(user, password))

    def close(self):
        self._driver.close()
```

### 4.2.2. URIs 연결 

URI 연결은 그래프 데이터 베이스 및 연결 방법을 식별합니다. 공식 Neo4j 드라이버는 현재, 다음 URI 스키마 및 드라이버 객체 유형을 지원합니다:

**테이블 4.2. 사용가능한 URI 스키마**

| URI 스키마  | 드라이버 객체 유형 |
| ----------- | ------------------ |
| 볼트        | 다이렉트 드라이버  |
| 볼트+라우팅 | 라우팅 드라이버    |

#### 4.2.2.1. 다이렉트 드라이버 (볼트)

다이렉트 드라이버는 ```bolt``` URI에서 생성됩니다. 예시: ```bolt://localhost:7687```. 이 종류의 드라이버는 단일 데이터베이스 서버 연결을 유지할 때 사용되며 일반적으로 단일 Neo4j 인스턴스를 사용하거나 특정 클러스터 멤버를 대상으로 작업할 때 사용됩니다. 라우팅 드라이버는 캐쥬얼 클러스터와 작업하는 것이 더 바람직합니다. 

#### 4.2.2.2. 라우팅 드라이버 (볼트+라우팅)

라우팅 드라이버는 ```bolt+routing``` URI를 통해 만들어 집니다. 예시: ```bolt+routing
://graph.example.com:7687```. URI 주소는 핵심 서버의 주소여야 됩니다. 이런 종류의 드라이버는 볼트 라우팅 프로토콜을 사용하고 클러스터와 함께 작동해서 사용 가능한 클러스터 멤버로 트랜잭션을 라우팅합니다. 

#### 4.2.2.3. 라우팅 컨텍스트를 사용한 드라이버 라우팅 

라우팅 컨텍스트가 있는 라우팅 드라이버는 1.3 이상 버전과 Neo4j 캐쥬얼 클러스터 3.2 이상 버전을 드라이버를 사용할 때 함께 쓸 수 있습니다. 이런 설치에서 라우팅 드라이버는 ```bolt+routing``` URI 쿼리 부분 문맥을 통해서 더 바람직한 라우팅을 포함할 수 있습니다. 

표준 Neo4j 환경설정에서 라우팅 컨텍스트는 *server policies*를 사용하여 서버 쪽에 정의됩니다. 그러므로 드라이버는 라우팅 컨텍스트를 서버 정책에 맞춰 클러스터에 전달합니다. 그 후, 서버 정책에 따라 클러스터에서 정제 된 라우팅 정보를 가져옵니다. 

라우팅 드라이버의 URI는 라우팅 컨텍스트가 있는 핵심 서버의 주소여야 합니다. 

**예시 4.7. 라우팅 컨텍스트로 라우팅 드라이버 설정**

이 예는 Neo4j가 [Neo4j 작동 메뉴얼 → 다중 데이터 센터 시스템을 위한 로드 밸런싱](https://neo4j.com/docs/operations-manual/current/clustering/multi-data-center/load-balancing/)에 설명된 서버 정책에 따라 설정되었다고 가정합니다. 특히, ```europe```라고 불리는 서버 정책을 정의했습니다. 또한, 드라이버를 지시하는 서버 ```neo01.graph.example.com```가 있습니다.
 
이 URI는 서버 정책 ```europe```을 사용합니다:
```bolt+routing://neo01.graph.example.com?policy=europe```

**라우팅 컨텍스트로 라우팅 드라이버를 사용하도록 서버 쪽 설정**

라우팅 컨텍스트의 라우팅 드라이버를 사용하는 전제조건은 Neo4j 데이터베이스가 인과관계 클러스터에서 [Multi-data 멀티 데이터 센터 라이센싱 옵션](https://neo4j.com/docs/operations-manual/3.4/clustering/multi-data-center/)가 활성화된 상태로 쓰이는 것 입니다. 또한, 라우팅 컨테스트는 클러스터에서 라우팅 클러스터로 정의되어야 합니다. 

### 4.2.3. 인증

인증 관련 세부 사항은 데이터베이스 접속에 필요한 사용자 이름, 비밀번호 또는 다른 신원 정보를 인증 토큰으로 제공합니다. Neo4j는 다양한 인증 기준을 지원하지만 보통 기본 인증을 사용합니다. 
 
#### 4.2.3.1. 기초 증명 

기초 증명 스키마는 서버 내에 저장된 암호 파일을 기반으로하고 에플리케이션에 사용자 이름과 암호를 제공해야합니다. 

**예시 4.8. 기초 증명**

+ C#

```
public IDriver CreateDriverWithBasicAuth(string uri, string user, string password)
{
    return GraphDatabase.Driver(uri, AuthTokens.Basic(user, password));
}
```

+ Java

```
public BasicAuthExample( String uri, String user, String password )
{
    driver = GraphDatabase.driver( uri, AuthTokens.basic( user, password ) );
}
```

+ JavaScript

```
const driver = neo4j.driver(uri, neo4j.auth.basic(user, password));
```

+ Python

```
def __init__(self, uri, user, password):
    self._driver = GraphDatabase.driver(uri, auth=(user, password))
```

기본 인증 스키마는 LDAP서버에서도 인증할 수 있습니다. 

#### 4.2.3.2. Kerberos 인증 

Kerberos 인증 스키마는 인증 토큰 Kerberos을 base64로 인코딩된 서버 인증 티켓과 만드는 간단한 방법을 제공합니다. Kerberos 인증 토큰을 생성하는 가장 좋은 방법은 아래와 같습니다. 

**예시 4.9. Kerberos 인증**

+ C#

```
public IDriver CreateDriverWithKerberosAuth(string uri, string ticket)
{
    return GraphDatabase.Driver(uri, AuthTokens.Kerberos(ticket),
        new Config { EncryptionLevel = EncryptionLevel.None });
}
```

+ Java

```
public KerberosAuthExample( String uri, String ticket )
{
    driver = GraphDatabase.driver( uri, AuthTokens.kerberos( ticket ) );
}
```

+ JavaScript

```
const driver = neo4j.driver(uri, neo4j.auth.kerberos(ticket));
```

+ Python

```
def __init__(self, uri, ticket):
    self._driver = GraphDatabase.driver(uri, auth=kerberos_auth(ticket))
```

서버에 [Kerberos 애드온](https://neo4j.com/docs/add-on/kerberos/1.0) 이 설치되어 있으면 Kerberos 인증 토큰은 서버에서만 적용할 수 있습니다. 

#### 4.2.3.3. 고객 인증 

사용자 지정 보안 공급자가 구축된 고급 배포의 경우 사용자 인증 도우미를 사용할 수 있습니다. 

**예시 4.10.고객 인증**
+ C#

```
public IDriver CreateDriverWithCustomizedAuth(string uri,
    string principal, string credentials, string realm, string scheme, Dictionary<string, object> parameters)
{
    return GraphDatabase.Driver(uri, AuthTokens.Custom(principal, credentials, realm, scheme, parameters),
        new Config {EncryptionLevel = EncryptionLevel.None});
}
```

+ Java

```
public CustomAuthExample( String uri, String principal, String credentials, String realm, String scheme,
        Map<String,Object> parameters )
{
    driver = GraphDatabase.driver( uri, AuthTokens.custom( principal, credentials, realm, scheme, parameters ) );
}
```

+ JavaScript

```
const driver = neo4j.driver(uri, neo4j.auth.custom(principal, credentials, realm, scheme, parameters));
```

+ Python

```
def __init__(self, uri, principal, credentials, realm, scheme, **parameters):
    self._driver = GraphDatabase.driver(uri, auth=custom_auth(principal, credentials, realm, scheme, **parameters))
```

### 4.2.4. 환경 설정 

#### 4.2.4.1. 암호화 

TLS 암호화는 기본적으로 모든 연결에서 사용할 수 있습니다. 이것은 다음 설정에서 비활성화될 수도 있습니다. 

**예시 4.11. 암호화 되지 않은 것**
+ C#

```
public IDriver CreateDriverWithCustomizedSecurityStrategy(string uri, string user, string password)
{
    return GraphDatabase.Driver(uri, AuthTokens.Basic(user, password),
        new Config {EncryptionLevel = EncryptionLevel.None});
}
```

+ Java

```
public ConfigUnencryptedExample( String uri, String user, String password )
{
    driver = GraphDatabase.driver( uri, AuthTokens.basic( user, password ),
            Config.build().withoutEncryption().toConfig() );
}
```

+ JavaScript

```
const driver = neo4j.driver(uri, neo4j.auth.basic(user, password),
  {
    encrypted: 'ENCRYPTION_OFF'
  }
);
```

+ Python

```
def __init__(self, uri, user, password):
    self._driver = GraphDatabase.driver(uri, auth=(user, password), encrypted=False)
```

서버는 모든 연결에서 암호화할 수 있도록 수정할 수 있습니다. 자세한 내용은 [운영 메뉴얼 → Neo4j 설정](https://neo4j.com/docs/operations-manual/3.4/configuration/connectors/)을 참조하면 됩니다. 

서버에서 허용하지 않는 암호화 설정으로 서버에 연결할 때 *서비스 이용 불가능* 상태가 됩니다. 

#### 4.2.4.2. 신뢰 

TLS 핸드 셰이크 동안 서버는 클라이언트 어플리케이션에 인증서를 제공합니다. 어플리케이션은 아래 신뢰 계획에 기반하여 이 인증서를 수용하거나 거부할 수 있습니다.

**테이블4.3. 신뢰 계획**

| 신뢰 계획                                 | 설명                                                     |
| ----------------------------------------- | -------------------------------------------------------- |
| ```TRUST_ALL_CERTIFICATES``` (기본 값)    | 서버에서 제공된 모든 인증서 수락합니다.                  |
| ```TRUST_CUSTOM_CA_SIGNED_CERTIFICATES``` | 사용자 지정 CA에 대해 확인할 수있는 인증서를 수락합니다. |
| ```TRUST_SYSTEM_CA_SIGNED_CERTIFICATES``` | 시스템 저장소에 대해 확인할 수있는 인증서를 수락합니다.  |

**예시 4.12. 신뢰**
+ C#

```
public IDriver CreateDriverWithCustomizedTrustStrategy(string uri, string user, string password)
{
    return GraphDatabase.Driver(uri, AuthTokens.Basic(user, password),
        new Config {TrustStrategy = TrustStrategy.TrustAllCertificates});
}
```

+ Java

```
public ConfigTrustExample( String uri, String user, String password )
{
    driver = GraphDatabase.driver( uri, AuthTokens.basic( user, password ),
            Config.build().withTrustStrategy( Config.TrustStrategy.trustAllCertificates() ).toConfig() );
}
```

+ JavaScript

```
const driver = neo4j.driver(uri, neo4j.auth.basic(user, password),
  {
    encrypted: 'ENCRYPTION_ON',
    trust: 'TRUST_ALL_CERTIFICATES'
  }
);
```

+ Python

```
def __init__(self, uri, user, password):
    self._driver = GraphDatabase.driver(uri, auth=(user, password), trust=TRUST_ALL_CERTIFICATES)

```


#### 4.2.4.3. 연결 풀 관리 

드라이버는 연결 풀을 유지합니다. 폴링 된 연결은 모든 쿼리의 새 연결에서 추가된 오버헤드를 피하기 위해 세션 및 트랜잭션에서 재사용 됩니다. 연결 풀은 항상 비어있습니다. 새로운 연결은 세션 및 트랜잭션의 요구에 의해 생성됩니다. 세션 및 트랜잭션의 실행을 마쳤을 때 연결은 재사용하기 위해서  풀로 반환됩니다. 
 
에플리케이션 사용자는 연결 풀 설정을 조정하여 클라이언트 행동 성능 요구 사항 및 데이터베이스 소스 사용 한계를 기반으로 다양한 케이스에 맞게 드라이버를 구성할 수 있습니다. 

드라이버 구성으로 사용할 수 있는 커넥션 풀 설정과 관련된 내용은 다음과 같습니다. 

```MaxConnectionLifetime```

이 임계 값보다 오래된 폴링 연결은 닫히고 풀에서 제거됩니다. 이런 제거는 커넥션을 획득하는 중에 발생하여 새 세션이 이전 연결에서 백업되지 않도록 합니다. 이 옵션을 낮은 값으로 설정하면 연결이 많이 끊겨서 성능이 저하될 수 있습니다. 주변 시스템 인프라 (운영 시스템, 라우터, 로드 발렌서, 프록시 및 방화벽과 같은)에 노출되는 최대 수명보다 값을 작게 설정하는 것이 좋습니다. 음수 값을 사용하면 수명이 확인되지 않습니다. 기본 값: 1시간
 
```MaxConnectionPoolSize```

이 설정은 커넥션 풀에서 다룰 수 있는 호스트별 최대 커넥션 개수를 정의합니다. 다른 말로, 다이렉트 드라이버에서 단일 데이터베이스의 최대 연결 수를 설정합니다. 라우팅 드라이버에서는 각 클러스터 멤버 별로 최대 연결 수를 설정합니다. 풀 크기가 가득찼을 때 세션 또는 트랜잭션이 연결을 요청한 경우, 풀에서 무료 연결을 사용할 수 있거나  새 연결을 확보하라는 요청이 시간 초과될 때까지 대기해야 합니다. 
타임아웃을 요청하는 연결은 ```ConnectionAcquisitionTimeout```에서 설정됩니다. 기본 값: 드라이버 별로 다르지만, 100 정도의 숫자입니다.  

```ConnectionAcquisitionTimeout```

이 설정은 세션이나 트랜잭션이 예외 처리를 하기 전에 풀에 대기중인 무료 연결 소비 시간을 제한합니다. 
이 경우 예외 처리는 ```ClientException```입니다. 타임 아웃은 연결 풀이 최대 용량일 때만 적용됩니다. 기본 값: 1m.

**예시 4.13. 연결 풀 관리**

+ C#

```
public IDriver CreateDriverWithCustomizedConnectionPool(string uri, string user, string password)
{
    return GraphDatabase.Driver(uri, AuthTokens.Basic(user, password),
        new Config
        {
            MaxConnectionLifetime = TimeSpan.FromMinutes(30),
            MaxConnectionPoolSize = 50,
            ConnectionAcquisitionTimeout = TimeSpan.FromMinutes(2)
        });
}
```

+ Java

```
public ConfigConnectionPoolExample( String uri, String user, String password )
{
    driver = GraphDatabase.driver( uri, AuthTokens.basic( user, password ), Config.build()
            .withMaxConnectionLifetime( 30, TimeUnit.MINUTES )
            .withMaxConnectionPoolSize( 50 )
            .withConnectionAcquisitionTimeout( 2, TimeUnit.MINUTES )
            .toConfig() );
}
```

+ JavaScript

```
const driver = neo4j.driver(uri, neo4j.auth.basic(user, password),
  {
    maxConnectionLifetime: 30*60*60,
    maxConnectionPoolSize: 50,
    connectionAcquisitionTimeout: 2*60
  }
);
```

+ Python

```
def __init__(self, uri, user, password):
    self._driver = GraphDatabase.driver(uri, auth=(user, password),
                                        max_connection_lifetime=30 * 60, max_connection_pool_size=50,
                                        connection_acquisition_timeout=2 * 60)
```

#### 4.2.4.4. 접속 시간 초과 

연결할 때 설정할 수 있는 최대 시간을 설정하려면 드라이버 설정에 기간 값을 적용하면 됩니다.  

**예시 4.14. 접속 시간 초과**

+ C#

```
public IDriver CreateDriverWithCustomizedConnectionTimeout(string uri, string user, string password)
{
    return GraphDatabase.Driver(uri, AuthTokens.Basic(user, password),
        new Config {ConnectionTimeout = TimeSpan.FromSeconds(15)});
}
```

+ Java

```
public ConfigConnectionTimeoutExample( String uri, String user, String password )
{
    driver = GraphDatabase.driver( uri, AuthTokens.basic( user, password ),
            Config.build().withConnectionTimeout( 15, SECONDS ).toConfig() );
}
```

+ JavaScript

이는 JavaScript 드라이버에서 이용할 수 없습니다. 

+ Python

```
def __init__(self, uri, user, password):
    self._driver = GraphDatabase.driver(uri, auth=(user, password), connection_timeout=15)
```

#### 4.2.4.5. 부하 분산 전략 

라우팅 드라이버는 여러 클러스터 멤버간 고르게 쿼리를 라우팅하는 부하 분산을 포함합니다. 내장된 부한 분산은 두 가지 전략을 제공합니다: ```least-connected```, ```round-robin```. 일반적으로 ```least-connected``` 전략은 클러스터 멤버에게 쿼리를 배포할 때 쿼리 실행 시간과 서버 로딩을 고려하므로 성능이 향상됩니다. 기본 값 : ```least-connected```

**예시 4.15. 부하 분산 전략**

+ C#
 
```
public IDriver CreateDriverWithCustomizedLoadBalancingStrategy(string uri, string user, string password)
{
    return GraphDatabase.Driver(uri, AuthTokens.Basic(user, password),
        new Config
        {
            LoadBalancingStrategy = LoadBalancingStrategy.LeastConnected
        });
}
```

+ Java

```
public ConfigLoadBalancingStrategyExample( String uri, String user, String password )
{
    driver = GraphDatabase.driver( uri, AuthTokens.basic( user, password ), Config.build()
            .withLoadBalancingStrategy( Config.LoadBalancingStrategy.LEAST_CONNECTED )
            .toConfig() );
}
```

+ JavaScript

```
const driver = neo4j.driver(uri, neo4j.auth.basic(user, password),
  {
    loadBalancingStrategy: "least_connected"
  }
);
```

+ Python

```
def __init__(self, uri, user, password):
    self._driver = GraphDatabase.driver(uri, auth=(user, password), load_balancing_strategy=LOAD_BALANCING_STRATEGY_LEAST_CONNECTED)
```

#### 4.2.4.6. 최대 재시도 시간 

재시도를 설정하려면 트랜잭션 기능을 재시도 시도할 최대 시간을 설정해야 합니다. 
 
**예시 4.16. 최대 재시도 시간**

+ C#

```
public IDriver CreateDriverWithCustomizedMaxRetryTime(string uri, string user, string password)
{
    return GraphDatabase.Driver(uri, AuthTokens.Basic(user, password),
        new Config {MaxTransactionRetryTime = TimeSpan.FromSeconds(15)});
}
```

+ Java

```
public ConfigMaxRetryTimeExample( String uri, String user, String password )
{
    driver = GraphDatabase.driver( uri, AuthTokens.basic( user, password ),
            Config.build().withMaxTransactionRetryTime( 15, SECONDS ).toConfig() );
}
```

+ JavaScript

```
const maxRetryTimeMs = 15 * 1000; // 15 seconds
const driver = neo4j.driver(uri, neo4j.auth.basic(user, password),
  {
    maxTransactionRetryTime: maxRetryTimeMs
  }
);
```

+ Python

```
def __init__(self, uri, user, password):
    self._driver = GraphDatabase.driver(uri, auth=(user, password), max_retry_time=15)
```

여기 설정된 시간은 작업 단위의 운영 시간을 고려하지 않으며, 단지 재시도가 더이상 시도되지 않는 것에 불과합니다. 

### 4.2.5. 이용할 수 없는 서비스 

드라이버를 재시도한 후에도 드라이버가 서버와 통신을 설정할 수 없을 경우 서비스 이용 불가능 상태가 표시됩니다. 이 조건에서는 주로 근본적인 네트워크나 데이터베이스 문제가 나타납니다. 응용 프로그램은 이런 문제를 해결하도록 설계해야 합니다.
  
**예시 4.17. 이용할 수 없는 서비스**

+ C#

```
public bool AddItem()
{
    try
    {
        using (var session = Driver.Session())
        {
            return session.WriteTransaction(
                tx =>
                {
                    tx.Run("CREATE (a:Item)");
                    return true;
                }
            );
        }
    }
    catch (ServiceUnavailableException)
    {
        return false;
    }
}
```

+ Java

```
public boolean addItem()
{
    try ( Session session = driver.session() )
    {
        return session.writeTransaction( new TransactionWork<Boolean>()
        {
            @Override
            public Boolean execute( Transaction tx )
            {
                tx.run( "CREATE (a:Item)" );
                return true;
            }
        } );
    }
    catch ( ServiceUnavailableException ex )
    {
        return false;
    }
}
```

+ JavaScript

```
const driver = neo4j.driver(uri, neo4j.auth.basic(user, password), {maxTransactionRetryTime: 3000});
const session = driver.session();

const writeTxPromise = session.writeTransaction(tx => tx.run('CREATE (a:Item)'));

writeTxPromise.catch(error => {
  if (error.code === neo4j.error.SERVICE_UNAVAILABLE) {
    console.log('Unable to create node: ' + error.code);
  }
});
```

+ Python

```
def add_item(self):
    try:
        with self._driver.session() as session:
            session.write_transaction(lambda tx: tx.run("CREATE (a:Item)"))
        return True
    except ServiceUnavailable:
        return False
```
