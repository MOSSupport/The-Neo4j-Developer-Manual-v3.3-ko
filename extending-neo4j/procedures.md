## 6.1. Procedures                  

User-defined procedures are written in Java, deployed into the database, and called from Cypher.

A *procedure* is a mechanism that allows Neo4j to be extended by writing custom code which can be invoked directly from Cypher.            Procedures can take arguments, perform operations on the database, and return results.         

Procedures are written in Java and compiled into *jar* files.            They can be deployed to the database by dropping a *jar* file into the *$NEO4J_HOME/plugins* directory on each standalone or clustered server.            The database must be re-started on each server to pick up new procedures.         

Procedures are the preferred means for extending Neo4j.            Examples of use cases for procedures are:         

1.  To provide access to functionality that is not available in Cypher, such as manual indexes and schema introspection.
2.  To provide access to third party systems.
3.  To perform graph-global operations, such as counting connected components or finding dense nodes.
4.  To express a procedural operation that is difficult to express declaratively with Cypher.

### 6.1.1. Calling procedures                     

To call a stored procedure, use a Cypher `CALL` clause.               The procedure name must be fully qualified, so a procedure named `findDenseNodes` defined in the package `org.neo4j.examples` could be called using:            

```
CALL org.neo4j.examples.findDenseNodes(1000)
```

A `CALL` may be the only clause within a Cypher statement or may be combined with other clauses.               Arguments can be supplied directly within the query or taken from the associated parameter set.               For full details, see the Cypher documentation on [the `CALL` clause.](https://neo4j.com/docs/developer-manual/3.3/cypher/clauses/call/)

### 6.1.2. Built-in procedures                     

Neo4j comes bundled with a number of built-in procedures.               These can be used to:            

-   Inspect schema.
-   Inspect meta data.
-   Explore procedures and components.
-   Monitor management data.
-   Set user password.

A subset of these are described in [Operations Manual → Built-in procedures](https://neo4j.com/docs/operations-manual/3.3/reference/procedures/).               Running `CALL dbms.procedures()` will display the full list of all the procedures.            

### 6.1.3. User-defined procedures                     

This section covers how to write, test and deploy a procedure for Neo4j.

Custom procedures are written in the Java programming language.               Procedures are deployed via a *jar* file that contains the code itself along with any dependencies (excluding Neo4j).               These files should be placed into the *plugin* directory of each standalone database or cluster member and will become available following the next database restart.            

The example that follows shows the steps to create and deploy a new procedure.

| **   | The example discussed below is available as [a repository on GitHub](https://github.com/neo4j-examples/neo4j-procedure-template).                              To get started quickly you can fork the repository and work with the code as you follow along in the guide below. |
| ---- | ---------------------------------------- |
|      |                                          |

#### 6.1.3.1. Set up a new project                        

A project can be set up in any way that allows for compiling a procedure and producing a *jar* file.                  Below is an example configuration using the [Maven](https://maven.apache.org/) build system.                  For readability, only excerpts from the Maven *pom.xml* file are shown here, the whole file is available from the [Neo4j Procedure Template](https://github.com/neo4j-examples/neo4j-procedure-template) repository.               

Setting up a project with Maven.                                  

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
   <neo4j.version>3.3.5</neo4j.version>
 </properties>
```

​                                 

Next, the build dependencies are defined.                  The following two sections are included in the *pom.xml* between `<dependencies></dependencies>` tags.               

The first dependency section includes the procedure API that procedures use at runtime.                  The scope is set to `provided`, because once the procedure is deployed to a Neo4j instance, this dependency is provided by Neo4j.                  If non-Neo4j dependencies are added to the project, their scope should normally be `compile`.               

```
<dependency>
  <groupId>org.neo4j</groupId>
  <artifactId>neo4j</artifactId>
  <version>${neo4j.version}</version>
  <scope>provided</scope>
</dependency>
```

Next, the dependencies necessary for testing the procedure are added:

-   Neo4j Harness, a utility that allows for starting a lightweight Neo4j instance.                        It is used to start Neo4j with a specific procedure deployed, which greatly simplifies testing.                     
-   The Neo4j Java driver, used to send cypher statements that call the procedure.
-   JUnit, a common Java test framework.

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

Along with declaring the dependencies used by the procedure it is also necessary to define the steps that Maven will go through                  to build the project.                  The goal is first to *compile* the source, then to *package* it in a *jar* that can be deployed to a Neo4j instance.               

| **   | Procedures require at least Java 8, so the version `1.8` should be defined as the *source* and *target version* in the configuration for the Maven compiler plugin. |
| ---- | ---------------------------------------- |
|      |                                          |

The [Maven Shade](https://maven.apache.org/plugins/maven-shade-plugin/) plugin is used to package the compiled procedure.                  It also includes all dependencies in the package, unless the dependency scope is set to *test* or *provided*.               

Once the procedure has been deployed to the *plugins* directory of each Neo4j instance and the instances have restarted, the procedure is available for use.               

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

#### 6.1.3.2. Writing integration tests                        

The test dependencies include *Neo4j Harness* and *JUnit*.                  These can be used to write integration tests for procedures.               

First, we decide what the procedure should do, then we write a test that proves that it does it right.                  Finally we write a procedure that passes the test.               

Below is a template for testing a procedure that accesses Neo4j’s full-text indexes from Cypher.

Writing tests for procedures.                                  

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

#### 6.1.3.3. Writing a procedure                        

With the test in place, we write a procedure procedure that fulfils the expectations of the test.                  The full example is available in the [Neo4j Procedure Template](https://github.com/neo4j-examples/neo4j-procedure-template) repository.               

Particular things to note:

-   All procedures are annotated `@Procedure`.                     
-   The procedure annotation can take two arguments, `name` and `mode`.                        
    -   `name` is used to specify a different name for the procedure than the default generated, which is `class.path.nameOfMethod`.                                 If `mode` is specified then `name` must be specified as well.                              
    -   `mode` is used to declare the types of interactions that the procedure will perform.                                    The default `mode` is `READ`.                                    The following modes are available:                                 
        -   `READ` This procedure will only perform read operations against the graph.                                       
        -   `WRITE` This procedure will perform read and write operations against the graph.                                       
        -   `SCHEMA` This procedure will perform operations against the schema, i.e. create and drop indexes and constraints.                                          A procedure with this mode is able to read graph data, but not write.                                       
        -   `DBMS` This procedure will perform system operations such as user management and query management.                                          A procedure with this mode is not able to read or write graph data.                                       
-   The *context* of the procedure, which is the same as each resource that the procedure wants to use, is annotated `@Context`.                     
-   The *input* and *output* to and from a procedure must be one of the supported types, see [Section 3.2.1, “Values and types”](https://neo4j.com/docs/developer-manual/3.3/cypher/syntax/values/).                        In Java this corresponds to `String`, `Long`, `Double`, `Boolean`, `org.neo4j.graphdb.Node`, `org.neo4j.graphdb.Relationship`, `org.neo4j.graphdb.Path`, and `org.neo4j.graphdb.spatial.Point`.                        Composite types are also supported via `List<T>` where `T` is one the supported types and `Map<String, Object>` where the values in the map must have one of the supported types.                        For the case where the type is not known beforehand we also support `Object`, however note that the actual value must still have one of the aforementioned types.                     

For more details, see the [API documentation for procedures](https://neo4j.com/docs/java-reference/3.3/javadocs/index.html?org/neo4j/procedure/Procedure.html).               

| **   | The correct way to signal an error from within a procedure is to throw a `RuntimeException`. |
| ---- | ---------------------------------------- |
|      |                                          |

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

#### 6.1.3.4. Injectable resources                        

When writing procedures, some resources can be injected into the procedure from the database.                  To inject these, use the `@Context` annotation.                  The classes that can be injected are:               

-   `Log`
-   `TerminationGuard`
-   `GraphDatabaseService`

All of the above classes are considered safe and future-proof, and will not compromise the security of the database.                  There are also several classes that can be injected that are unsupported restricted and can be changed with little or no notice.                  The database will not load all classes by default but can be toggled with `dbms.security.procedures.unrestricted` to load unsafe procedures.               