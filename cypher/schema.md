## 3.5. 스키마


```
이 섹션에서는 Neo4j에서 Cypher 쿼리 언어로 선택적 스키마를 작업하는 방법 대해 다룹니다.
```

Neo4j 2.0은 레이블 개념 기반 그래프를 위한 선택적 스키마를 도입했습니다. 레이블은 인덱스 설명서 및 그래프 제약 조건을 정의할 때 사용됩니다. 인덱스와 제약 조건은 그래프의 스키마입니다. Cypher는 스키마 조작을 위한 DDL(Data Definition Language) 문을 포함합니다. 

- [인덱스](https://mossupport.github.io/developer-manual/cypher/schema/index.html)
	+ 단일-속성 인덱스 생성
	+ 데이터베이스의 모든 인덱스 리스트 가져오기
	+ 복합 인덱스 생성
	+ 단일-속성 인덱스 삭제
	+ 복합 인덱스 삭제 
	+ 인덱스 사용
	+ 동일성(equality)을 사용하는 ```WHERE```에 단일-속성 색인 사용
	+ 동일성(equality)을 사용하는 ```WHERE```에 반대 인덱스 사용
	+ 범위 비교를 사용하는 ```WHERE```를 사용하여 인덱스 사용
	+ ```IN```과 인덱스 함께 사용
	+ ```STARTS WITH```과 인덱스 함께 사용
	+ 속성 존재를 확인할 때 인덱스 사용 
	+ 공간 거리 검색을 실행할 때 인덱스 사용
	+ 내장 프로시저를 사용하여 명시적 인덱스 관리 및 사용

 - [제약조건](https://mossupport.github.io/developer-manual/cypher/schema/constraints.html)

 	+ 고유한 노드 특성 제약조건 
 	+ 데이터베이스의 모든 제약조건 가져오기
 	+ 노드 속성 존재 제약조건
 	+ 관계 속성 존재 제약조건
 	+ 노드 키 

