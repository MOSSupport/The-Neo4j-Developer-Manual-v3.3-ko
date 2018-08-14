
## 6.2. 사용자 정의 함수

```
이 섹션에서는 Neo4j에 대한 사용자 정의 함수를 작성, 테스트 및 배포하는 방법에 대해서 다룹니다.
```

사용자 정의 함수는 읽기만 가능하고 항상 단일 값을 리턴하는 단일 절차 형식 입니다. 기능이 뛰어나지는 않지만 비슷한 업무에서 사용할 때 보다 쉽고 효율적입니다. 
 
### 6.2.1. 사용자 정의 함수 호출

사용자 정의 함수는 다른 사이퍼 함수와 같은 방식으로 호출됩니다. ```org.neo4j.examples``` 패키지에 정의 된 ```join``` 함수를 아래를 이용해서 호출하려면 함수 이름은 완전해야 합니다. 

```
MATCH (p: Person) WHERE p.age = 36  RETURN org.neo4j.examples.join(collect(p.names))
```


### 6.2.2. 사용자 정의 함수 작성

사용자 정의 함수는 프로시저가 생성되는 방법과 비슷하지만 ```@UserFunction```로 주석처리하고 스트림 값을 반환하는 대신 단일 값을 리턴합니다. 유효한 출력 타입은 ```T```가 지원되는 유형중 하나인 ```long```, ```Long```, ```double```, ```Double```, ```boolean```, ```Boolean```, ```String```, ```Node```, ```Relationship```, ```Path```, ```Map<String, Object``` 또는 ```List<T>``` 입니다. 

더 자세한 정보는 [사용자 정의 함수 관련 API 문서](https://neo4j.com/docs/java-reference/3.4/javadocs/org/neo4j/procedure/UserFunction.html)에서 확인할 수 있습니다. 

 ```RuntimeException```를 예외처리 하여 함수 내 에러 신호를 알립니다. 
 
```
package example;

import org.neo4j.procedure.Name;
import org.neo4j.procedure.Procedure;
import org.neo4j.procedure.UserFunction;

public class Join
{
    @UserFunction
    @Description("example.join(['s1','s2',...], delimiter) - join the given strings with the given delimiter.")
    public String join(
            @Name("strings") List<String> strings,
            @Name(value = "delimiter", defaultValue = ",") String delimiter) {
        if (strings == null || delimiter == null) {
            return null;
        }
        return String.join(delimiter, strings);
    }
}
```

### 6.2.2.1. 통합 테스트 작성

사용자 정의 함수 테스트는 프로시저와 같은 방법으로 생성됩니다. 

아래는 문자열 리스트를 결합하는 사용자 정의 함수를 테스트하는 템플릿입니다. 

**사용자 정의 함수를 결합하기 위한 테스트 작성**

```
package example;

import org.junit.Rule;
import org.junit.Test;
import org.neo4j.driver.v1.*;
import org.neo4j.harness.junit.Neo4jRule;

import static org.hamcrest.core.IsEqual.equalTo;
import static org.junit.Assert.assertThat;

public class JoinTest
{
    // This rule starts a Neo4j instance
    @Rule
    public Neo4jRule neo4j = new Neo4jRule()

            // This is the function we want to test
            .withFunction( Join.class );

    @Test
    public void shouldAllowIndexingAndFindingANode() throws Throwable
    {
        // This is in a try-block, to make sure we close the driver after the test
        try( Driver driver = GraphDatabase.driver( neo4j.boltURI() , Config.build().withEncryptionLevel( Config.EncryptionLevel.NONE ).toConfig() ) )
        {
            // Given
            Session session = driver.session();

            // When
            String result = session.run( "RETURN example.join(['Hello', 'World']) AS result").single().get("result").asString();

            // Then
            assertThat( result, equalTo( "Hello,World" ) );
        }
    }
}

```

### 6.2.3. 사용자 정의 집합 함수

```
이 섹션에서는 Neo4j 사용자 정의 집합 함수에서 작성하고 테스트 하는 방법에 대해 다룹니다.
```

사용자 정의 집합 함수는 데이터를 통합하고 단일 결과를 리턴하는 함수입니다.  

### 6.2.3.1. 사용자 정의 집합 함수 호출

사용자 정의 집합 함수는 다른 사이퍼 통합 함수와 같은 방식으로 호출됩니다. 함수 이름 ```longestString```가 ```org.neo4j.examples``` 패키지에서 아래를 호출하려면 함수 이름을 정규화해야 합니다.  

```
MATCH (p: Person) WHERE p.age = 36  RETURN org.neo4j.examples.longestString(p.name)
```

### 6.2.3.2. 사용자 정의 집합 함수 작성 

사용자 정의 집함 함수는 ```@UserAggregationFunction```로 주석처리 되어있습니다. 주석처리 된 함수는 집합 클래스 인스턴스를 리턴해야 합니다. 집합 클래스에는 각각 ```@UserAggregationUpdate```와 ```@UserAggregationResult``` 로 주석 처리된 하나의 함수가 있습니다. ```@UserAggregationUpdate```로 주석처리된 메소드는 여러번 호출되며 클래스가 데이터를 통합하도록 합니다. 통합 작업 후 ```@UserAggregationResult```로 주석 처리 된 함수는 한 번 호출되고 통합 결과를 리턴합니다. 유효한 출력 타입은 ```T```가 지원되는 유형중 하나인 ```long```, ```Long```, ```double```, ```Double```, ```boolean```, ```Boolean```, ```String```, ```Node```, ```Relationship```, ```Path```, ```Map<String, Object``` 또는 ```List<T>``` 입니다. 

더 자세한 정보는 [사용자 정의 결합 함수에서 이용가능한 API 문서](https://neo4j.com/docs/java-reference/3.4/javadocs/org/neo4j/procedure/UserAggregationFunction.html)에서 확인할 수 있습니다. 


결합 함수에서 에러를 감지하는 올바른 방법은 ```RuntimeException```를 예외 처리 하는 것 입니다. 

```
package example;

import org.neo4j.procedure.Description;
import org.neo4j.procedure.Name;
import org.neo4j.procedure.UserAggregationFunction;
import org.neo4j.procedure.UserAggregationResult;
import org.neo4j.procedure.UserAggregationUpdate;

public class LongestString
{
    @UserAggregationFunction
    @Description( "org.neo4j.function.example.longestString(string) - aggregates the longest string found" )
    public LongStringAggregator longestString()
    {
        return new LongStringAggregator();
    }

    public static class LongStringAggregator
    {
        private int longest;
        private String longestString;

        @UserAggregationUpdate
        public void findLongest(
                @Name( "string" ) String string )
        {
            if ( string != null && string.length() > longest)
            {
                longest = string.length();
                longestString = string;
            }
        }

        @UserAggregationResult
        public String result()
        {
            return longestString;
        }
    }
}

```

### 통합 테스트 작성

사용자 정의 통합 함수 테스트는 사용자 정의 함수와 같은 방식으로 생성됩니다. 아래는 가장 긴 문자열을 검색할 때 사용자 정의 통합 함수를 테스트하는 템플릿 입니다. 

**longString을 위한 사용자 정의 함수 테스트 작성**

```
package example;

import org.junit.Rule;
import org.junit.Test;
import org.neo4j.driver.v1.*;
import org.neo4j.harness.junit.Neo4jRule;

import static org.hamcrest.core.IsEqual.equalTo;
import static org.junit.Assert.assertThat;

public class LongestStringTest
{
    // This rule starts a Neo4j instance
    @Rule
    public Neo4jRule neo4j = new Neo4jRule()

            // This is the function we want to test
            .withAggregationFunction( LongestString.class );

    @Test
    public void shouldAllowIndexingAndFindingANode() throws Throwable
    {
        // This is in a try-block, to make sure we close the driver after the test
        try( Driver driver = GraphDatabase.driver( neo4j.boltURI() , Config.build().withEncryptionLevel( Config.EncryptionLevel.NONE ).toConfig() ) )
        {
            // Given
            Session session = driver.session();

            // When
            String result = session.run( "UNWIND ["abc", "abcd", "ab"] AS string RETURN example.longestString(string) AS result").single().get("result").asString();

            // Then
            assertThat( result, equalTo( "abcd" ) );
        }
    }
}
```