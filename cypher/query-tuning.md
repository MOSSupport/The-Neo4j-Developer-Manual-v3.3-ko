## 3.6. 쿼리튜닝

```
이 장에서는 Cypher 쿼리 언어에 대한 쿼리 튜닝에 대해 설명하고자 합니다.
```

Neo4j는 최대한 빨리 쿼리를 실행하기 위해 노력합니다.

그러나 최대 쿼리 실행 성능을 위해 최적화할 때 도메인 및 응용 프로그램 지식을 활용하여 쿼리를 다시 작성하는 것이 도움됩니다.

수동-쿼리 성능 최적화의 목표는 필요한 데이터만 그래프에 검색되도록 하는 것 입니다. 쿼리 후반부에서 수행하는 작업량을 줄이기 위해서는 데이터를 가능한 빨리 필터링해야 합니다. 반환 된 내용도 마찬가지 입니다. 전체 노드 및 관계를 반환하는 것이 아니라 필요한 데이터만 선택하여 반환해야 합니다. 또한, 가변 길이 패턴의 상한선을 설정하여 필요한 것보다 많은 부분을 다루지 않도록 해야합니다.

각 Cypher 쿼리는 최적화되어 Cypher 실행 엔진이 실행 계획으로 변환합니다. 작업 내 리소스를 최소화하려면 리터럴 대신 매개 변수를 사용하는 것이 좋습니다. 이를 통해 Cypher는 새로운 실행 계획을 분석하고 작성하는 대신 쿼리를 재사용할 수 있습니다.

이 장에서 언급 된 실행 계획 운영자에 대한 자세한 내용은 [3.7. "실행 계획"](https://mossupport.github.io/developer-manual/cypher/execution-plans/execution-plans.html)을 참조하십시오.

+ [Cypher 쿼리 옵션](query-tuning/cypher-query-options.md)
+ [쿼리 프로파일링](query-tuning/how-do-i-profile-a-query.md)
+ [기본 쿼리 튜닝 예제](query-tuning/basic-query-tuning-example.md)
+ [USING](query-tuning/using.md)
	+ 소개
	+ 인덱스 힌트
	+ 스캔 힌트
	+ 결합 힌트
	+ ```PERIODIC COMMIT``` 쿼리 힌트 