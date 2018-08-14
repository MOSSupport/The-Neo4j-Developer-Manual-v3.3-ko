
### 3.2.8. 코멘트

쿼리에 코멘트를 추가할 때는 이중 슬래시를 사용합니다. 예시: 

```
MATCH (n) RETURN n 
```

```
MATCH (n)
//This is a whole line comment
RETURN n
```

```
MATCH (n) WHERE n.property = '//This is NOT a comment' RETURN n
```