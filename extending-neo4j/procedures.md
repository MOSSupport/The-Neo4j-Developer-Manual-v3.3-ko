

## 6.1. 프로시저(Procedures)


```
사용자 정의 프로시저는 Java로 쓰이고 데이터베이스에 배포되며 Cypher에서 호출됩니다.
```

프로시저는 사이퍼(Cypher)에서 직접 호출할 수 있는 사용자 정의 코드를 작성하여 Neo4j를 확장할 수 있는 메커니즘입니다. 프로시저는 변수를 가질 수 있고 데이터베이스에서 수행되며 결과를 리턴합니다. 


프로시저는 Java로 작성되고 jar 파일로 압축됩니다. jar 파일을 각각의 독립되거나 클러스터 된 서버 *$NEO4J_HOME/plugins* 디렉토리에 넣어서 데이터베이스에 배포할 수 있습니다. 새로운 프로시저를 시작하기 위해서는 각 서버에서 데이터베이스를 재시작 해야합니다. 

이 프로시저는 Neo4j를 확장할 때 선호하는 방식입니다. 아래는 프로시저 사용 예시 입니다.:

1. 사이퍼에서 메뉴얼 인덱스, 스키마 내에서 같이 사용할 수 없는 기능에 대한 접근 허용을 제공합니다. 

2. 제 3자 시스템에 대한 접근을 허용합니다. 

3. 연결된 구성 요소를 세거나 밀집도가 높은 노드를 찾는 것과 같은 전역 그래프 작업을 수행합니다. 

4. 사이퍼로 표현하기 어려운 프로시저 작업을 표현합니다. 


### 6.1.1. 프로시저 호출

저장된 프로시저를 호출할 때 사이퍼 ```call``` 절을 사용합니다.  ```CALL org.neo4j.examples.findDenseNodes(1000)```을 사용해서 호출하려면 ```findDenseNodes```프로시저명이 패키지 ```org.neo4j.examples``` 내에서 정규화된 이름이여야 합니다. ```CALL```은 사이퍼 구에서 유일한 절이거나 다른 절과 결합될 수 있습니다. 인수는 쿼리 내에서 제공되거나 관련 매개 변수 집합에서 직접 가져올 수 있습니다. 자세한 내용은 사이퍼 문서 [CALL 절](././cypher/clauses.md)에서 확인할 수 있습니다. 

 
### 6.1.2. 내장 프로시저                   

Neo4j에는 여러가지 프로시저가 묶음으로 제공됩니다. 이것은 아래와 같이 쓰입니다:

+ 스키마 검사.
+ 메타 데이터 검사.
+ 프로시저와 구성요소 탐색.
+ 관리 데이터 모니터.
+ 사용자 비밀번호 설정.

하위 집합은 [운영 메뉴얼 -> 제공된 절차](././reference/procedures.md)에서 확인할 수 있습니다. ```CALL dbms.procedures()```을 실행하면 프로시저의 모든 리스트가 표시됩니다. 


### 6.1.3. 사용자 정의 프로시저

```
이 섹션에서는 Neo4j에서 프로시저를 작성, 테스트 및 배포하는 방법에 대해 다룹니다.                  
```

사용자 프로시저는 프로그래밍 언어 Java로 작성되어 있습니다. 프로시저는 (Neo4j를 제외하고)코드 자체와 모든 종속성을 포함하는 *jar*파일을 통해 배포됩니다. 다음 데이터베이스가 재시작한 후 파일을 이용할 수 있도록 독립형 데이터베이스나 클러스터 멤버의 *plungin*  디렉토리에 위치해야 합니다.   

아래는 새 절차를 생성하고 배치하는 단계를 보여주는 예입니다. 

 아래 예는 [GitHub 저장소](https://github.com/neo4j-examples/neo4j-procedure-template)로 사용할 수 있습니다. 신속하게 시작하려면 저장소를 포크(fork)하고 아래 가이드를 따라 코드로 작업하면 됩니다. 

 
#### 6.1.3.1. 새 프로젝트 설정                       

프로젝트는 프로시저를 컴파일하고 *jar* 파일을 생성하는 모든 방법으로 설정할 수 있습니다. 아래는 [Maven](https://maven.apache.org/) 빌드 시스템을 설정하는 예입니다. 가독성을 높이기 위해 여기서는 Maven *pom.xml* 파일 발췌한 부분만 나타냅니다. 전체 파일은 [Neo4j 프로시저 템플릿](https://github.com/neo4j-examples/neo4j-procedure-template) 저장소에서 사용할 수 있습니다. 

**Maven으로 프로젝트 설정하기**

```
<project xmlns="http://maven.apache.org/POM/4.0.0" xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
        xsi:schemaLocation="http://maven.apache.org/POM/4.0.0
                     http://maven.apache.org/xsd/maven-4.0.0.xsd">
 <modelVersion>4.0.0</modelVersion>

 <groupId>org.neo4j.example</groupId>
 <artifactId>procedure-template</artifactId>
 <version>1.0.0-SNAPSHOT</version>

 <packaging>jar</packaging>
 <name>Neo4j Procedure Template</name>
 <description>A template project for building a Neo4j Procedure</description>

 <properties>
   <neo4j.version>3.4.0</neo4j.version>
 </properties>
```

다음으로 빌드 종속성이 정의됩니다. 아래 두 섹션은 *pom.xml*의  ```<dependencies></dependencies>``` 테그 사이에 포함되어 있습니다. 
 
첫 번째 종속 섹션에는 런타임에 사용되는 API 프로시저가 포함됩니다. 프로시저가 Neo4j 인스턴스에 배포되면 Neo4j에서 종속성을 제공하므로 범위는 ```provided```가 되도록 설정됩니다. 프로젝트에 Neo4j가 아닌 종속성이 추가된다면 해당 범위는 ```compile``` 되어야 됩니다.     


```
<dependency>
  <groupId>org.neo4j</groupId>
  <artifactId>neo4j</artifactId>
  <version>${neo4j.version}</version>
  <scope>provided</scope>
</dependency>
```

다음으로 프로시저를 테스트하는데 필요한 종속성이 추가됩니다:

+ Neo4j Harness는 경량 Neo4j 인스턴스를 시작할 수 있는 유틸리티 입니다. 특정 절차를 구현하고 Neo4j를 시작하는 데 사용되므로 테스트가 크게 간소화 됩니다.
+ Neo4j Java 드라이버는 프로시저를 호출하는 사이퍼 문을 전송할 때 사용됩니다. 
+ JUnit은 일반적인 Java 테스트 프레임워크입니다. 


```
<dependency>
  <groupId>org.neo4j.test</groupId>
  <artifactId>neo4j-harness</artifactId>
  <version>${neo4j.version}</version>
  <scope>test</scope>
</dependency>

<dependency>
  <groupId>org.neo4j.driver</groupId>
  <artifactId>neo4j-java-driver</artifactId>
  <version>1.5.2</version>
  <scope>test</scope>
</dependency>

<dependency>
  <groupId>junit</groupId>
  <artifactId>junit</artifactId>
  <version>4.12</version>
  <scope>test</scope>
</dependency>
```

 
프로시저에서 사용하는 종속성을 선언하는 것 외에 Maven이 프로젝트를 빌드하려면 수행할 단계를 정의해야 합니다. 목표는 소스를 컴파일 하는 것이고 다음으로는 Neo4j 인스턴스에 배포할 수 있는 *jar* 파일로 패키지 하는 것 입니다. 


프로시저는 버전 1.8은 Maven 컴파일러 플러그인 구성에서 소스 및 대상 버전을 정의할 수 있는 Java버전 8 이상이 필요합니다. 


The [Maven Shade](https://maven.apache.org/plugins/maven-shade-plugin/) 플러그인은 컴파일 프로시저를 패키지할 때 사용됩니다. 또한 이것은 종속성 범위가 *test* 또는        *provide*로 되어있지 않은 경우 패키지 내 모든 종속성도 포함합니다. 

프로시저가 각 Neo4j 인스턴스 *plugins* 디렉토리에 배치되고 인스턴스가 재시작되면 프로시저를 사용할 수 있습니다. 

```
<build>
 <plugins>
   <plugin>
     <artifactId>maven-compiler-plugin</artifactId>
     <version>3.1</version>
     <configuration>
       <source>1.8</source>
       <target>1.8</target>
     </configuration>
   </plugin>
   <plugin>
     <artifactId>maven-shade-plugin</artifactId>
     <executions>
       <execution>
         <phase>package</phase>
         <goals>
           <goal>shade</goal>
         </goals>
       </execution>
     </executions>
   </plugin>
 </plugins>
</build>
```

#### 6.1.3.2. 통합 테스트 작성                        

테스트 종속성은 *Neo4j Harness*와  *JUnit*을 포함합니다. 이들은 프로시저에서 통합 테스트를 작성할 때 사용됩니다. 

먼저 프로시저 역할을 정해야 합니다. 그 후 작동 여부를 확인하기 위해서 테스트를 작성합니다. 마지막으로 테스트를 통과하는 프로시저를 작성합니다.

아래는 사이퍼에서 Neo4j의 전체 텍스트 인덱스에 액세스하는 프로시저를 테스트 하는 템플릿입니다. 


**프로시저를 위한 테스트 작성.**

```
package example;

import org.junit.Rule;
import org.junit.Test;
import org.neo4j.driver.v1.*;
import org.neo4j.graphdb.factory.GraphDatabaseSettings;
import org.neo4j.harness.junit.Neo4jRule;

import static org.hamcrest.core.IsEqual.equalTo;
import static org.junit.Assert.assertThat;
import static org.neo4j.driver.v1.Values.parameters;

public class ManualFullTextIndexTest
{
    // This rule starts a Neo4j instance
    @Rule
    public Neo4jRule neo4j = new Neo4jRule()

            // This is the Procedure we want to test
            .withProcedure( FullTextIndex.class );

    @Test
    public void shouldAllowIndexingAndFindingANode() throws Throwable
    {
        // In a try-block, to make sure we close the driver after the test
        try( Driver driver = GraphDatabase.driver( neo4j.boltURI() , Config.build().withoutEncryption().toConfig() ) )
        {

            // Given I've started Neo4j with the FullTextIndex procedure class
            //       which my 'neo4j' rule above does.
            Session session = driver.session();

            // And given I have a node in the database
            long nodeId = session.run( "CREATE (p:User {name:'Brookreson'}) RETURN id(p)" )
                    .single()
                    .get( 0 ).asLong();

            // When I use the index procedure to index a node
            session.run( "CALL example.index({id}, ['name'])", parameters( "id", nodeId ) );

            // Then I can search for that node with lucene query syntax
            StatementResult result = session.run( "CALL example.search('User', 'name:Brook*')" );
            assertThat( result.single().get( "nodeId" ).asLong(), equalTo( nodeId ) );
        }
    }
}
```

​                                
#### 6.1.3.3. 프로시저 작성                        


테스트가 완료되면 테스트 기대치를 충족하는 프로시저가 작성됩니다. 전체 예제는 [Neo4j 절차 템플릿](https://github.com/neo4j-examples/neo4j-procedure-template) 저장소에서 사용할 수 있습니다.    

주의 사항:

+ 모든 프로시저는 ```@Procedure``` 로 주석처리 되어 있습니다. 
+ 프로시저 주석은 두 가지 인수 ```name```과 ```mode```를 가질 수 있습니다. 

  + ```name```은 생성된 기본값 ```class.path.nameOfMethod```과 다른 프로시저 이름을 지정할 때 사용됩니다. 만약 ```mode```가 지정되었다면 ```name```도 지정해야 합니다. 
  + ```mode```는 프로시저가 수행하는 상호 작용 유형을 선언할 때 사용됩니다. 기본 ```mode```는 ```READ```입니다. 아래 모드를 사용할 수 있습니다:

    + ```READ``` 이 프로시저는 그래프에 대해 읽기 작업을 수행합니다. 
    + ```WRITE``` 이 프로시저는 그래프에 대해 읽기와 쓰기 작업을 수행합니다.
    + ```SCHEMA``` 이 프로시저는 스키마에 대해 작업을 수행합니다. 인덱스 및 제한 조건 작성 및 제거 작업입니다. 이 모드에서 절차는 그래프 데이터를 읽을 수 있지만 쓸 수는 없습니다. 
    + ```DBMS``` 이 절차에서는 사용자 관라와 쿼리 관리와 같은 시스템 작동 역할을 수행할 것 입니다. 이 모드 프로시저에서는 그레프 데이터를 읽거나 쓸 수 없습니다.

+ 프로시저 내용은 프로시저에서 사용할 리소스와 같은 ```@Context```로 주석 처리됩니다. 

+ 아래 유형에 대해 언급할 수 있습니다:

  + 프로시저에서 보낸 입출력은 [섹션 3.2.1, "값 및 유형"](././cypher/syntax/values.md)에서 지원하는 타입 중에서 하나여야 합니다. 
  + Java에서 Cypher 유형 및 해당 항목은 아래 표에서 확인할 수 있습니다. 
  + 복합 타입은 다음을 통해서 지원됩니다:

    +  ```List<T>```는 ```T```를 지원하는 종류 중에 하나입니다. 
    + ```Map<String, Object>```은 맵에서 지원하는 종류 중에서 하나여야 합니다. 
  + 유형을 미리알 수 없는 경우에 ```Object``` 사용이 지원됩니다. 그러나 실제 값은 앞서 언급한 타입 중에서 하나의 값을 가져야 됩니다. 


**테이블 6.1. 지원되는 유형**

| 사이퍼 종류           | Java 종류                                |
| --------------------- | ---------------------------------------- |
| ``` String```         | ``` String```                            |
| ``` Integer```        | ``` Long```                              |
| ``` Float```          | ```Double```                             |
| ``` Boolean```        | ``` Boolean```                           |
| ``` Point```          | ``` org.neo4j.graphdb.spatial.Point```   |
| ``` Date```           | ``` java.time.LocalDate```               |
| ``` Time```           | ``` java.time.OffsetTime```              |
| ``` LocalTime```      | ``` java.time.LocalTime```               |
| ````DateTime````      | ``` java.time.ZonedDateTime```           |
| ````LocalDateTime```` | ``` java.time.LocalDateTime```           |
| ````Duration````      | ``` java.time.temporal.TemporalAmount``` |
| ``` Node```           | ``` org.neo4j.graphdb.Node```            |
| ``` Relationship```   | ``` org.neo4j.graphdb.Relationship```    |
| ``` Path```           | ``` org.neo4j.graphdb.Path```            |


자세한 정보는 [API 문서 프로시저](https://neo4j.com/docs/java-reference/3.4/javadocs/index.html?org/neo4j/procedure/Procedure.html)에서 확인할 수 있습니다. 

프로시저 내에서 에러를 표시하는 올바른 방법은  ```RuntimeException```를 예외처리 하는 것 입니다.

```
package example;

import java.util.List;
import java.util.Map;
import java.util.Set;
import java.util.stream.Stream;

import org.neo4j.graphdb.GraphDatabaseService;
import org.neo4j.graphdb.Label;
import org.neo4j.graphdb.Node;
import org.neo4j.graphdb.index.Index;
import org.neo4j.graphdb.index.IndexManager;
import org.neo4j.logging.Log;
import org.neo4j.procedure.Context;
import org.neo4j.procedure.Name;
import org.neo4j.procedure.PerformsWrites;
import org.neo4j.procedure.Procedure;

import static org.neo4j.helpers.collection.MapUtil.stringMap;
import static org.neo4j.procedure.Procedure.Mode.SCHEMA;
import static org.neo4j.procedure.Procedure.Mode.WRITE;

/**
 * This is an example showing how you could expose Neo4j's full text indexes as
 * two procedures - one for updating indexes, and one for querying by label and
 * the lucene query language.
 */
public class FullTextIndex
{
    // Only static fields and @Context-annotated fields are allowed in
    // Procedure classes. This static field is the configuration we use
    // to create full-text indexes.
    private static final Map<String,String> FULL_TEXT =
            stringMap( IndexManager.PROVIDER, "lucene", "type", "fulltext" );

    // This field declares that we need a GraphDatabaseService
    // as context when any procedure in this class is invoked
    @Context
    public GraphDatabaseService db;

    // This gives us a log instance that outputs messages to the
    // standard log, `neo4j.log`
    @Context
    public Log log;

    /**
     * This declares the first of two procedures in this class - a
     * procedure that performs queries in a manual index.
     *
     * It returns a Stream of Records, where records are
     * specified per procedure. This particular procedure returns
     * a stream of {@link SearchHit} records.
     *
     * The arguments to this procedure are annotated with the
     * {@link Name} annotation and define the position, name
     * and type of arguments required to invoke this procedure.
     * There is a limited set of types you can use for arguments,
     * these are as follows:
     *
     * <ul>
     *     <li>{@link String}</li>
     *     <li>{@link Long} or {@code long}</li>
     *     <li>{@link Double} or {@code double}</li>
     *     <li>{@link Number}</li>
     *     <li>{@link Boolean} or {@code boolean}</li>
     *     <li>{@link org.neo4j.graphdb.Node}</li>
     *     <li>{@link org.neo4j.graphdb.Relationship}</li>
     *     <li>{@link org.neo4j.graphdb.Path}</li>
     *     <li>{@link java.util.Map} with key {@link String} and value of any type in this list, including {@link java.util.Map}</li>
     *     <li>{@link java.util.List} of elements of any valid field type, including {@link java.util.List}</li>
     *     <li>{@link Object}, meaning any of the types above</li>
     *
     * @param label the label name to query by
     * @param query the lucene query, for instance `name:Brook*` to
     *              search by property `name` and find any value starting
     *              with `Brook`. Please refer to the Lucene Query Parser
     *              documentation for full available syntax.
     * @return the nodes found by the query
     */
    @Procedure( name = "example.search", mode = WRITE )
    public Stream<SearchHit> search( @Name("label") String label,
                                     @Name("query") String query )
    {
        String index = indexName( label );

        // Avoid creating the index, if it's not there we won't be
        // finding anything anyway!
        if( !db.index().existsForNodes( index ))
        {
            // Just to show how you'd do logging
            log.debug( "Skipping index query since index does not exist: `%s`", index );
            return Stream.empty();
        }

        // If there is an index, do a lookup and convert the result
        // to our output record.
        return db.index()
                .forNodes( index )
                .query( query )
                .stream()
                .map( SearchHit::new );
    }

    /**
     * This is the second procedure defined in this class, it is used to update the
     * index with nodes that should be queryable. You can send the same node multiple
     * times, if it already exists in the index the index will be updated to match
     * the current state of the node.
     *
     * This procedure works largely the same as {@link #search(String, String)},
     * with three notable differences. One, it is annotated with `mode = SCHEMA`,
     * which is <i>required</i> if you want to perform updates to the graph in your
     * procedure.
     *
     * Two, it returns {@code void} rather than a stream. This is simply a short-hand
     * for saying our procedure always returns an empty stream of empty records.
     *
     * Three, it uses a default value for the property list, in this way you can call
     * the procedure by simply invoking {@code CALL index(nodeId)}. Default values are
     * are provided as the Cypher string representation of the given type, e.g.
     * {@code {default: true}}, {@code null}, or {@code -1}.
     *
     * @param nodeId the id of the node to index
     * @param propKeys a list of property keys to index, only the ones the node
     *                 actually contains will be added
     */
    @Procedure( name = "example.index", mode = SCHEMA )
    public void index( @Name("nodeId") long nodeId,
                       @Name(value = "properties", defaultValue = "[]") List<String> propKeys )
    {
        Node node = db.getNodeById( nodeId );

        // Load all properties for the node once and in bulk,
        // the resulting set will only contain those properties in `propKeys`
        // that the node actually contains.
        Set<Map.Entry<String,Object>> properties =
                node.getProperties( propKeys.toArray( new String[0] ) ).entrySet();

        // Index every label (this is just as an example, we could filter which labels to index)
        for ( Label label : node.getLabels() )
        {
            Index<Node> index = db.index().forNodes( indexName( label.name() ), FULL_TEXT );

            // In case the node is indexed before, remove all occurrences of it so
            // we don't get old or duplicated data
            index.remove( node );

            // And then index all the properties
            for ( Map.Entry<String,Object> property : properties )
            {
                index.add( node, property.getKey(), property.getValue() );
            }
        }
    }


    /**
     * This is the output record for our search procedure. All procedures
     * that return results return them as a Stream of Records, where the
     * records are defined like this one - customized to fit what the procedure
     * is returning.
     *
     * The fields must be one of the following types:
     *
     * <ul>
     *     <li>{@link String}</li>
     *     <li>{@link Long} or {@code long}</li>
     *     <li>{@link Double} or {@code double}</li>
     *     <li>{@link Number}</li>
     *     <li>{@link Boolean} or {@code boolean}</li>
     *     <li>{@link org.neo4j.graphdb.Node}</li>
     *     <li>{@link org.neo4j.graphdb.Relationship}</li>
     *     <li>{@link org.neo4j.graphdb.Path}</li>
     *     <li>{@link java.util.Map} with key {@link String} and value {@link Object}</li>
     *     <li>{@link java.util.List} of elements of any valid field type, including {@link java.util.List}</li>
     *     <li>{@link Object}, meaning any of the valid field types</li>
     * </ul>
     */
    public static class SearchHit
    {
        // This records contain a single field named 'nodeId'
        public long nodeId;

        public SearchHit( Node node )
        {
            this.nodeId = node.getId();
        }
    }

    private String indexName( String label )
    {
        return "label-" + label;
    }
}
```


#### 6.1.3.4. 주입할 수 있는 리소스 

프로시저를 적을 때 데이터 베이스에서 프로시저로 일부 자원을 주입할 수 있습니다. 이것을 주입하려면 ```@Context```주석을 사용하면 됩니다. 주입할 수 있는 클래스는 다음과 같습니다. 

+ ```Log```
+ ```TerminationGuard```
+ ```GraphDatabaseService```


위 클래스는 안전하고 데이터베이스 보안을 손상시키지 않습니다. 또한 주입할 수 있는 몇몇 클래스에 제한되지 않고 알림 없이 변경할 수 있습니다. 데이터 베이스는 기본적으로 모든 클래스를 로드하지 않지만, ```dbms.security.procedures.unrestricted```를 사용하면 안전하지 않은 프로시저까지 로드할 수 있습니다. 
