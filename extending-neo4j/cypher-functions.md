## 6.2. User-defined functions                  

This section covers how to write, test and deploy a user-defined function for Neo4j.

User-defined functions are a simpler form of procedures that are read-only and always return a single value.            Although they are not as powerful in capability, they are often easier to use and more efficient than procedures for many            common tasks.         

### 6.2.1. Calling a user-defined function                     

User-defined functions are called in the same way as any other Cypher function.               The function name must be fully qualified, so a function named `join` defined in the package `org.neo4j.examples` could be called using:            

```
MATCH (p: Person) WHERE p.age = 36  RETURN org.neo4j.examples.join(collect(p.names))
```

### 6.2.2. Writing a user-defined function                     

User-defined functions are created similarly to how procedures are created, but are instead annotated with `@UserFunction` and instead of returning a stream of values it returns a single value.               Valid output types are `long`, `Long`, `double`, `Double`, `boolean`, `Boolean`, `String`, `Node`, `Relationship`, `Path`, `Map<String, Object`, or `List<T>`, where `T` can be any of the supported types.            

For more details, see the [API documentation for user-defined functions](https://neo4j.com/docs/java-reference/3.3/javadocs/org/neo4j/procedure/UserFunction.html).            

| **   | The correct way to signal an error from within a function is to throw a `RuntimeException`. |
| ---- | ---------------------------------------- |
|      |                                          |

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

#### 6.2.2.1. Writing integration tests                        

Tests for user-defined functions are created in the same way as those for procedures.

Below is a template for testing a user-defined function that joins a list of strings.

Writing tests for the  *join* user-defined function.                                  

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

​                                 

### 6.2.3. User-defined aggregation functions                     

This section covers how to write, test and deploy a user-defined aggregation function for Neo4j.

User-defined aggregation functions are functions that aggregate data and return a single result.

#### 6.2.3.1. Calling a user-defined aggregation function                        

User-defined aggregation functions are called in the same way as any other Cypher aggregation function.                  The function name must be fully qualified, so a function named `longestString` defined in the package `org.neo4j.examples` could be called using:               

```
MATCH (p: Person) WHERE p.age = 36  RETURN org.neo4j.examples.longestString(p.name)
```

#### 6.2.3.2. Writing a user-defined aggregation function                        

User-defined aggregation functions are annotated with `@UserAggregationFunction`.                  The annotated function must return an instance of an aggregator class.                  An aggregator class contains one method annotated with `@UserAggregationUpdate` and one method annotated with `@UserAggregationResult`.                  The method annotated with `@UserAggregationUpdate` will be called multiple times and allows the class to aggregate data.                  When the aggregation is done the method annotated with `@UserAggregationResult` is called once and the result of the aggregation will be returned.                  Valid output types are `long`, `Long`, `double`, `Double`, `boolean`, `Boolean`, `String`, `Node`, `Relationship`, `Path`, `Map<String, Object`, or `List<T>`, where `T` can be any of the supported types.               

For more details, see the [API documentation for user-defined aggregation functions](https://neo4j.com/docs/java-reference/3.3/javadocs/org/neo4j/procedure/UserAggregationFunction.html).               

| **   | The correct way to signal an error from within an aggregation function is to throw a `RuntimeException`. |
| ---- | ---------------------------------------- |
|      |                                          |

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

##### Writing integration tests                           

Tests for user-defined aggregation functions are created in the same way as those for normal user-defined functions.

Below is a template for testing a user-defined aggregation function that finds the longest string.

Writing tests for the  *longestString* user-defined function.                                        

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