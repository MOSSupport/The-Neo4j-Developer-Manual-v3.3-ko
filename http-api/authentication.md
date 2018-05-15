## 5.2. Authentication and authorization                  

This section describes authentication and authorization using the Neo4j HTTP API.

The HTTP API supports authentication and authorization so that requests to the HTTP API must be authorized using the username            and password of a valid user.            Authentication and authorization are enabled by default.            Refer to [Operations Manual → Enabling authentication and authorization](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/enable/) for a description on how to enable and disable authentication and authorization.         

When Neo4j is first installed you can authenticate with the default user `neo4j` and the default password `neo4j`.            The default password must be changed before access to resources will be permitted.            This is done either using Neo4j Browser or via direct HTTP calls (see [Section 5.2.2, “User status and password changing”](https://neo4j.com/docs/developer-manual/3.3/http-api/authentication/#http-api-security-user-status-and-password-changing)).         

### 5.2.1. Authenticating                     

#### 5.2.1.1. Missing authorization                        

If an Authorization header is not supplied, the server will reply with an error.

*Example request*

-   **GET**  http://localhost:7474/db/data/                     
-   **Accept:** application/json; charset=UTF-8                     

*Example response*

-   **401:** Unauthorized                     
-   **Content-Type:** application/json; charset=UTF-8                     
-   **WWW-Authenticate:** Basic realm="Neo4j"                     

```
{
  "errors" : [ {
    "code" : "Neo.ClientError.Security.Unauthorized",
    "message" : "No authentication header supplied."
  } ]
}
```

#### 5.2.1.2. Authenticate to access the server                        

Authenticate by sending a username and a password to Neo4j using HTTP Basic Auth.                  Requests should include an Authorization header, with a value of Basic <payload>,                  where "payload" is a base64 encoded string of "username:password".               

*Example request*

-   **GET**  http://localhost:7474/user/neo4j                     
-   **Accept:** application/json; charset=UTF-8                     
-   **Authorization:** Basic bmVvNGo6c2VjcmV0                     

*Example response*

-   **200:** OK                     
-   **Content-Type:** application/json; charset=UTF-8                     

```
{
  "password_change_required" : false,
  "password_change" : "http://localhost:7474/user/neo4j/password",
  "username" : "neo4j"
}
```

#### 5.2.1.3. Incorrect authentication                        

If an incorrect username or password is provided, the server replies with an error.

*Example request*

-   **POST**  http://localhost:7474/db/data/                     
-   **Accept:** application/json; charset=UTF-8                     
-   **Authorization:** Basic bmVvNGo6aW5jb3JyZWN0                     

*Example response*

-   **401:** Unauthorized                     
-   **Content-Type:** application/json; charset=UTF-8                     
-   **WWW-Authenticate:** Basic realm="Neo4j"                     

```
{
  "errors" : [ {
    "code" : "Neo.ClientError.Security.Unauthorized",
    "message" : "Invalid username or password."
  } ]
}
```

#### 5.2.1.4. Required password changes                        

In some cases, like the very first time Neo4j is accessed, the user will be required to choose                  a new password. The database will signal that a new password is required and deny access.               

See [Section 5.2.2, “User status and password changing”](https://neo4j.com/docs/developer-manual/3.3/http-api/authentication/#http-api-security-user-status-and-password-changing) for how to set a new password.               

*Example request*

-   **GET**  http://localhost:7474/db/data/                     
-   **Accept:** application/json; charset=UTF-8                     
-   **Authorization:** Basic bmVvNGo6bmVvNGo=                     

*Example response*

-   **403:** Forbidden                     
-   **Content-Type:** application/json; charset=UTF-8                     

```
{
  "password_change" : "http://localhost:7474/user/neo4j/password",
  "errors" : [ {
    "code" : "Neo.ClientError.Security.Forbidden",
    "message" : "User is required to change their password."
  } ]
}
```

### 5.2.2. User status and password changing                     

#### 5.2.2.1. User status                        

Given that you know the current password, you can ask the server for the user status.

*Example request*

-   **GET**  http://localhost:7474/user/neo4j                     
-   **Accept:** application/json; charset=UTF-8                     
-   **Authorization:** Basic bmVvNGo6c2VjcmV0                     

*Example response*

-   **200:** OK                     
-   **Content-Type:** application/json; charset=UTF-8                     

```
{
  "password_change_required" : false,
  "password_change" : "http://localhost:7474/user/neo4j/password",
  "username" : "neo4j"
}
```

#### 5.2.2.2. User status on first access                        

On first access, and using the default password, the user status will indicate that the users password requires changing.

*Example request*

-   **GET**  http://localhost:7474/user/neo4j                     
-   **Accept:** application/json; charset=UTF-8                     
-   **Authorization:** Basic bmVvNGo6bmVvNGo=                     

*Example response*

-   **200:** OK                     
-   **Content-Type:** application/json; charset=UTF-8                     

```
{
  "password_change_required" : true,
  "password_change" : "http://localhost:7474/user/neo4j/password",
  "username" : "neo4j"
}
```

#### 5.2.2.3. Changing the user password                        

Given that you know the current password, you can ask the server to change a users password. You can choose any                  password you like, as long as it is different from the current password.               

*Example request*

-   **POST**  http://localhost:7474/user/neo4j/password                     
-   **Accept:** application/json; charset=UTF-8                     
-   **Authorization:** Basic bmVvNGo6bmVvNGo=                     
-   **Content-Type:** application/json                     

```
{
  "password" : "secret"
}
```

*Example response*

-   **200:** OK                     

```

```

### 5.2.3. Access when authentication and authorization are disabled                     

When authentication and authorization have been disabled, HTTP API requests can be sent without an `Authorization` header.            

### 5.2.4. Copying security configuration from one instance to another                     

The username and password combination is local to each Neo4j instance.               In many cases you want to start a Neo4j instance with preconfigured authentication and authorization.               For instructions on how to do this, refer to [Operation Manual → Propagate users and roles](https://neo4j.com/docs/operations-manual/3.3/security/authentication-authorization/native-user-role-management/propagate-users-and-roles/).            