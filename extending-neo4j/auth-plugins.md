
## 6.3. 플러그인 인증 및 권한 부여 

```
이 챕터에서는 맞춤형 Neo4j 플러그인 인증 및 권한 부여에 대해서 다룹니다. 
```

Neo4j는 인증 및 권한 부여 플러그인 인터페이스를 제공합니다. 이를 통해 실제로 네이티브 사용자나 내장 구성 기반 LAP 커넥터가 다루지 않는 배포 시나리오를 지원합니다. 

SPI는 org.neo5j.server.security.enterprise.auth.plugin.spi 패키지에 있습니다. 맞춤형 플러그인은 파일에 설정된 사용자 설정을 로드할 때 [<neo4j-home>](././configuration/file-locations.md) 디렉토리에 접근 가능합니다. 또한 플러그인은 보안 이벤트 로그에 기록될 수 있습니다. 

인증 플러그인은 AuthPlugin 인터페이스를 인증 메소드로 실행합니다. 아래 예는 Neo4j 사용자와 비밀번호를 체크하는 최소 인증 플러그인을 나타냅니다. 

```
@Override
public AuthenticationInfo authenticate( AuthToken authToken )
{
    String principal = authToken.principal();
    char[] credentials = authToken.credentials();

    if ( principal.equals( "neo4j" ) && Arrays.equals( credentials, "neo4j".toCharArray() ) )
    {
        return (AuthenticationInfo) () -> "neo4j";
    }
    return null;
}
```

인증 플러그인은 ```AuthPlugin``` 인터페이스를 관리 메소드로 구현합니다. 아래 예는 neo4j 사용자에게 독자 역할을 부여하는 최소 권한 부여 플러그인을 나타냅니다. 헬퍼 클래스 ```PredefinedRole``` 사용에 주목하십시오.


```
@Override
public AuthorizationInfo authorize( Collection<PrincipalAndProvider> principals )
{
    if ( principals.stream().anyMatch( p -> "neo4j".equals( p.principal() ) ) )
    {
        return (AuthorizationInfo) () -> Collections.singleton( PredefinedRoles.READER );
    }
    return null;
}
```

단일 메소드에서 인증 및 허가 권한 부여 기능을 모두 제공하는 단순 결합 플러그인 인터페이스는 `authenticateAndAuthorize`라고 부릅니다. 아래 예에서 neo4j/neo4j 자격 증명을 확인하고 독자  권한을 리턴하는 결합 플러그인을 확인할 수 있습니다. 

```
@Override
public AuthInfo authenticateAndAuthorize( AuthToken authToken )
{
    String principal = authToken.principal();
    char[] credentials = authToken.credentials();

    if ( principal.equals( "neo4j" ) && Arrays.equals( credentials, "neo4j".toCharArray() ) )
    {
        return AuthInfo.of( "neo4j", Collections.singleton( PredefinedRoles.READER ) );
    }
    return null;
}
```

 
Neo4j는 기본 LDAP 커넥터로 사용자 배포 시나리오를 설정하도록 확장 플랫폼을 제공합니다. 알려진 복잡도 중 하나는 그룹에 사용자가 있고 다른 방법은 LDAP 사용자 디렉토리와 통합하는 것 입니다. 아래 예에서는 사용자가 속해있는 그룹을 검색하고 다음 사용자 정의 빌드 ```getNeo4jRoleForGroupId```메소드를 호출하여 해당 그룹을 Neo4j 역할에 매핑합니다.  

  
```
@Override
public AuthInfo authenticateAndAuthorize( AuthToken authToken ) throws AuthenticationException
{
    try
    {
        String username = authToken.principal();
        char[] password = authToken.credentials();

        LdapContext ctx = authenticate( username, password );
        Set<String> roles = authorize( ctx, username );

        return AuthInfo.of( username, roles );
    }
    catch ( NamingException e )
    {
        throw new AuthenticationException( e.getMessage() );
    }
}

private LdapContext authenticate( String username, char[] password ) throws NamingException
{
    Hashtable<String,Object> env = new Hashtable<>();
    env.put( Context.INITIAL_CONTEXT_FACTORY, "com.sun.jndi.ldap.LdapCtxFactory" );
    env.put( Context.PROVIDER_URL, "ldap://0.0.0.0:10389" );

    env.put( Context.SECURITY_PRINCIPAL, String.format( "cn=%s,ou=users,dc=example,dc=com", username ) );
    env.put( Context.SECURITY_CREDENTIALS, password );

    return new InitialLdapContext( env, null );
}

private Set<String> authorize( LdapContext ctx, String username ) throws NamingException
{
    Set<String> roleNames = new LinkedHashSet<>();

    // Set up our search controls
    SearchControls searchCtls = new SearchControls();
    searchCtls.setSearchScope( SearchControls.SUBTREE_SCOPE );
    searchCtls.setReturningAttributes( new String[]{GROUP_ID} );

    // Use a search argument to prevent potential code injection
    Object[] searchArguments = new Object[]{username};

    // Search for groups that has the user as a member
    NamingEnumeration result = ctx.search( GROUP_SEARCH_BASE, GROUP_SEARCH_FILTER, searchArguments, searchCtls );

    if ( result.hasMoreElements() )
    {
        SearchResult searchResult = (SearchResult) result.next();

        Attributes attributes = searchResult.getAttributes();
        if ( attributes != null )
        {
            NamingEnumeration attributeEnumeration = attributes.getAll();
            while ( attributeEnumeration.hasMore() )
            {
                Attribute attribute = (Attribute) attributeEnumeration.next();
                String attributeId = attribute.getID();
                if ( attributeId.equalsIgnoreCase( GROUP_ID ) )
                {
                    // We found a group that the user is a member of. See if it has a role mapped to it
                    String groupId = (String) attribute.get();
                    String neo4jGroup = getNeo4jRoleForGroupId( groupId );
                    if ( neo4jGroup != null )
                    {
                        // Yay! Add it to our set of roles
                        roleNames.add( neo4jGroup );
                    }
                }
            }
        }
    }
    return roleNames;
}

```

다른 플러그 예시는 [https://github.com/neo4j/neo4j-example-auth-plugins](https://github.com/neo4j/neo4j-example-auth-plugins)에서 확인 가능합니다. 