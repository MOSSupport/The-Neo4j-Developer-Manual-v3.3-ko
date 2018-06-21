# Chapter 5. HTTP API

This chapter covers the HTTP API for operating and querying Neo4j.

## 5.1. Transactional Cypher HTTP endpoint                     

The Neo4j transactional HTTP endpoint allows you to execute a series of Cypher statements within the scope of a transaction.               The transaction may be kept open across multiple HTTP requests, until the client chooses to commit or roll back.               Each HTTP request can include a list of statements, and for convenience you can include statements along with a request to               begin or commit a transaction.            

The server guards against orphaned transactions by using a timeout.               If there are no requests for a given transaction within the timeout period, the server will roll it back.               You can configure the timeout in the server configuration, by setting `Operations Manual → dbms.rest.transaction.idle_timeout` to the number of seconds before timeout.               The default timeout is 60 seconds.            

| **   | Literal line breaks are not allowed inside Cypher statements.                                 Open transactions are not shared among members of an HA cluster.                                    Therefore, if you use this endpoint in an HA cluster, you must ensure that all requests for a given transaction are sent to                                    the same Neo4j instance.                                                                  Cypher queries with `USING PERIODIC COMMIT`  (see [Section 3.6.4.5, “`PERIODIC COMMIT` query hint”](https://neo4j.com/docs/developer-manual/3.3/cypher/query-tuning/using/#query-using-periodic-commit-hint)) may only be executed when creating a new transaction and immediately committing it with a single HTTP request (see [Section 5.1.2, “Begin and commit a transaction in one request”](https://neo4j.com/docs/developer-manual/3.3/http-api/#rest-api-begin-and-commit-a-transaction-in-one-request) for how to do that).                                                                  When a request fails the transaction will be rolled back.                                    By checking the result for the presence/absence of the `transaction` key you can figure out if the transaction is still open. |
| ---- | ---------------------------------------- |
|      |                                          |

### 5.1.1. Streaming                        

Responses from the HTTP API can be transmitted as JSON streams, resulting in                  better performance and lower memory overhead on the server side. To use                  streaming, supply the header `X-Stream: true` with each request.               

| **   | In order to speed up queries in repeated scenarios, try not to use literals but replace them with parameters wherever possible.                                 This will let the server cache query plans.                                 See [Section 3.2.6, “Parameters”](https://neo4j.com/docs/developer-manual/3.3/cypher/syntax/parameters/) for more information. |
| ---- | ---------------------------------------- |
|      |                                          |

### 5.1.2. Begin and commit a transaction in one request                        

If there is no need to keep a transaction open across multiple HTTP requests, you can begin a transaction,                  execute statements, and commit with just a single HTTP request.               

*Example request*

-   **POST**  http://localhost:7474/db/data/transaction/commit                     
-   **Accept:** application/json; charset=UTF-8                     
-   **Content-Type:** application/json                     

```
{
  "statements" : [ {
    "statement" : "CREATE (n) RETURN id(n)"
  } ]
}
```

*Example response*

-   **200:** OK                     
-   **Content-Type:** application/json                     

```
{
  "results" : [ {
    "columns" : [ "id(n)" ],
    "data" : [ {
      "row" : [ 6 ],
      "meta" : [ null ]
    } ]
  } ],
  "errors" : [ ]
}
```

### 5.1.3. Execute multiple statements                        

You can send multiple Cypher statements in the same request.                  The response will contain the result of each statement.               

*Example request*

-   **POST**  http://localhost:7474/db/data/transaction/commit                     
-   **Accept:** application/json; charset=UTF-8                     
-   **Content-Type:** application/json                     

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

*Example response*

-   **200:** OK                     
-   **Content-Type:** application/json                     

```
{
  "results" : [ {
    "columns" : [ "id(n)" ],
    "data" : [ {
      "row" : [ 2 ],
      "meta" : [ null ]
    } ]
  }, {
    "columns" : [ "n" ],
    "data" : [ {
      "row" : [ {
        "name" : "My Node"
      } ],
      "meta" : [ {
        "id" : 3,
        "type" : "node",
        "deleted" : false
      } ]
    } ]
  } ],
  "errors" : [ ]
}
```

### 5.1.4. Begin a transaction                        

You begin a new transaction by posting zero or more Cypher statements                  to the transaction endpoint. The server will respond with the result of                  your statements, as well as the location of your open transaction.               

*Example request*

-   **POST**  http://localhost:7474/db/data/transaction                     
-   **Accept:** application/json; charset=UTF-8                     
-   **Content-Type:** application/json                     

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

*Example response*

-   **201:** Created                     
-   **Content-Type:** application/json                     
-   **Location:** http://localhost:7474/db/data/transaction/10                     

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
        "id" : 10,
        "type" : "node",
        "deleted" : false
      } ]
    } ]
  } ],
  "transaction" : {
    "expires" : "Thu, 26 Apr 2018 14:56:41 +0000"
  },
  "errors" : [ ]
}
```

### 5.1.5. Execute statements in an open transaction                        

Given that you have an open transaction, you can make a number of requests, each of which executes additional                  statements, and keeps the transaction open by resetting the transaction timeout.               

*Example request*

-   **POST**  http://localhost:7474/db/data/transaction/12                     
-   **Accept:** application/json; charset=UTF-8                     
-   **Content-Type:** application/json                     

```
{
  "statements" : [ {
    "statement" : "CREATE (n) RETURN n"
  } ]
}
```

*Example response*

-   **200:** OK                     
-   **Content-Type:** application/json                     

```
{
  "commit" : "http://localhost:7474/db/data/transaction/12/commit",
  "results" : [ {
    "columns" : [ "n" ],
    "data" : [ {
      "row" : [ { } ],
      "meta" : [ {
        "id" : 11,
        "type" : "node",
        "deleted" : false
      } ]
    } ]
  } ],
  "transaction" : {
    "expires" : "Thu, 26 Apr 2018 14:56:41 +0000"
  },
  "errors" : [ ]
}
```

### 5.1.6. Reset transaction timeout of an open transaction                        

Every orphaned transaction is automatically expired after a period of inactivity.  This may be prevented                  by resetting the transaction timeout.               

The timeout may be reset by sending a keep-alive request to the server that executes an empty list of statements.                  This request will reset the transaction timeout and return the new time at which the transaction will                  expire as an RFC1123 formatted timestamp value in the ``transaction'' section of the response.               

*Example request*

-   **POST**  http://localhost:7474/db/data/transaction/2                     
-   **Accept:** application/json; charset=UTF-8                     
-   **Content-Type:** application/json                     

```
{
  "statements" : [ ]
}
```

*Example response*

-   **200:** OK                     
-   **Content-Type:** application/json                     

```
{
  "commit" : "http://localhost:7474/db/data/transaction/2/commit",
  "results" : [ ],
  "transaction" : {
    "expires" : "Thu, 26 Apr 2018 14:56:40 +0000"
  },
  "errors" : [ ]
}
```

### 5.1.7. Commit an open transaction                        

Given you have an open transaction, you can send a commit request. Optionally, you submit additional statements                  along with the request that will be executed before committing the transaction.               

*Example request*

-   **POST**  http://localhost:7474/db/data/transaction/6/commit                     
-   **Accept:** application/json; charset=UTF-8                     
-   **Content-Type:** application/json                     

```
{
  "statements" : [ {
    "statement" : "CREATE (n) RETURN id(n)"
  } ]
}
```

*Example response*

-   **200:** OK                     
-   **Content-Type:** application/json                     

```
{
  "results" : [ {
    "columns" : [ "id(n)" ],
    "data" : [ {
      "row" : [ 5 ],
      "meta" : [ null ]
    } ]
  } ],
  "errors" : [ ]
}
```

### 5.1.8. Rollback an open transaction                        

Given that you have an open transaction, you can send a rollback request. The server will rollback the                  transaction. Any further statements trying to run in this transaction will fail immediately.               

*Example request*

-   **DELETE**  http://localhost:7474/db/data/transaction/3                     
-   **Accept:** application/json; charset=UTF-8                     

*Example response*

-   **200:** OK                     
-   **Content-Type:** application/json; charset=UTF-8                     

```
{
  "results" : [ ],
  "errors" : [ ]
}
```

### 5.1.9. Include query statistics                        

By setting `includeStats` to `true` for a statement, query statistics will be returned for it.               

*Example request*

-   **POST**  http://localhost:7474/db/data/transaction/commit                     
-   **Accept:** application/json; charset=UTF-8                     
-   **Content-Type:** application/json                     

```
{
  "statements" : [ {
    "statement" : "CREATE (n) RETURN id(n)",
    "includeStats" : true
  } ]
}
```

*Example response*

-   **200:** OK                     
-   **Content-Type:** application/json                     

```
{
  "results" : [ {
    "columns" : [ "id(n)" ],
    "data" : [ {
      "row" : [ 4 ],
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

### 5.1.10. Return results in graph format                        

If you want to understand the graph structure of nodes and relationships returned by your query,                  you can specify the "graph" results data format. For example, this is useful when you want to visualize the                  graph structure. The format collates all the nodes and relationships from all columns of the result,                  and also flattens collections of nodes and relationships, including paths.               

*Example request*

-   **POST**  http://localhost:7474/db/data/transaction/commit                     
-   **Accept:** application/json; charset=UTF-8                     
-   **Content-Type:** application/json                     

```
{
  "statements" : [ {
    "statement" : "CREATE ( bike:Bike { weight: 10 } ) CREATE ( frontWheel:Wheel { spokes: 3 } ) CREATE ( backWheel:Wheel { spokes: 32 } ) CREATE p1 = (bike)-[:HAS { position: 1 } ]->(frontWheel) CREATE p2 = (bike)-[:HAS { position: 2 } ]->(backWheel) RETURN bike, p1, p2",
    "resultDataContents" : [ "row", "graph" ]
  } ]
}
```

*Example response*

-   **200:** OK                     
-   **Content-Type:** application/json                     

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
        "id" : 7,
        "type" : "node",
        "deleted" : false
      }, [ {
        "id" : 7,
        "type" : "node",
        "deleted" : false
      }, {
        "id" : 0,
        "type" : "relationship",
        "deleted" : false
      }, {
        "id" : 8,
        "type" : "node",
        "deleted" : false
      } ], [ {
        "id" : 7,
        "type" : "node",
        "deleted" : false
      }, {
        "id" : 1,
        "type" : "relationship",
        "deleted" : false
      }, {
        "id" : 9,
        "type" : "node",
        "deleted" : false
      } ] ],
      "graph" : {
        "nodes" : [ {
          "id" : "7",
          "labels" : [ "Bike" ],
          "properties" : {
            "weight" : 10
          }
        }, {
          "id" : "8",
          "labels" : [ "Wheel" ],
          "properties" : {
            "spokes" : 3
          }
        }, {
          "id" : "9",
          "labels" : [ "Wheel" ],
          "properties" : {
            "spokes" : 32
          }
        } ],
        "relationships" : [ {
          "id" : "0",
          "type" : "HAS",
          "startNode" : "7",
          "endNode" : "8",
          "properties" : {
            "position" : 1
          }
        }, {
          "id" : "1",
          "type" : "HAS",
          "startNode" : "7",
          "endNode" : "9",
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

### 5.1.11. Handling errors                        

The result of any request against the transaction endpoint is streamed back to the client.                  Therefore the server does not know whether the request will be successful or not when it sends the HTTP status                  code.               

Because of this, all requests against the transactional endpoint will return 200 or 201 status code, regardless                  of whether statements were successfully executed. At the end of the response payload, the server includes a list                  of errors that occurred while executing statements. If this list is empty, the request completed successfully.               

If any errors occur while executing statements, the server will roll back the transaction.

In this example, we send the server an invalid statement to demonstrate error handling.

For more information on the status codes, see [Section A.1, “Neo4j Status Codes”](https://neo4j.com/docs/developer-manual/3.3/reference/status-codes/).               

*Example request*

-   **POST**  http://localhost:7474/db/data/transaction/11/commit                     
-   **Accept:** application/json; charset=UTF-8                     
-   **Content-Type:** application/json                     

```
{
  "statements" : [ {
    "statement" : "This is not a valid Cypher Statement."
  } ]
}
```

*Example response*

-   **200:** OK                     
-   **Content-Type:** application/json                     

```
{
  "results" : [ ],
  "errors" : [ {
    "code" : "Neo.ClientError.Statement.SyntaxError",
    "message" : "Invalid input 'T': expected <init> (line 1, column 1 (offset: 0))\n\"This is not a valid Cypher Statement.\"\n ^"
  } ]
}
```

### 5.1.12. Handling errors in an open transaction                        

Whenever there is an error in a request the server will rollback the transaction.                  By inspecting the response for the presence/absence of the `transaction` key you can tell if the transaction is still open               

*Example request*

-   **POST**  http://localhost:7474/db/data/transaction/9                     
-   **Accept:** application/json; charset=UTF-8                     
-   **Content-Type:** application/json                     

```
{
  "statements" : [ {
    "statement" : "This is not a valid Cypher Statement."
  } ]
}
```

*Example response*

-   **200:** OK                     
-   **Content-Type:** application/json                     

```
{
  "commit" : "http://localhost:7474/db/data/transaction/9/commit",
  "results" : [ ],
  "errors" : [ {
    "code" : "Neo.ClientError.Statement.SyntaxError",
    "message" : "Invalid input 'T': expected <init> (line 1, column 1 (offset: 0))\n\"This is not a valid Cypher Statement.\"\n ^"
  } ]
}
```