## 3.6. 쿼리튜닝

```
이 장에서는 Cypher 쿼리 언어에 대한 쿼리 튜닝에 대해 설명하고자 합니다.
```

Neo4j는 가능한 한 빨리 쿼리를 실행하기 위해 매우 열심히 노력합니다.

그러나 최대의 쿼리 실행 성능을 위해 최적화할 때 도메인 및 응용 프로그램에 대한 지식을 사용하여 쿼리를 다시 작성하는 것이 도움이 될 수 있습니다.

수동 쿼리 성능 최적화의 전반적인 목표는 필요한 데이터만 그래프에서 검색되도록 하는 것입니다. 최소한, 쿼리 실행의 후반 단계에서 수행해야 하는 작업량을 줄이기 위해 가능한 한 빨리 데이터를 필터링해야합니다. 또한 다음이 반환됩니다. 전체 노드와 관계를 반환하지 말고 필요한 데이터를 선택하고 그 데이터만 반환해야 합니다. 그리고 가변 길이 패턴의 상한선을 설정하여 필요한 것보다 많은 부분을 다루지 않도록 해야합니다.

각 Cypher 쿼리는 최적화되어 Cypher 실행 엔진에 의해 실행 계획으로 변환됩니다. 이 작업에 사용되는 리소스를 최소화하려면 가능한 경우 리터럴 대신 매개 변수를 사용해야합니다. 이를 통해 Cypher는 새로운 실행 계획을 구문 분석하고 작성하는 대신, 쿼리를 다시 사용할 수 있습니다.

이 장에서 언급 된 실행 계획 운영자에 대한 자세한 내용은 [3.7. "실행 계획"](https://mossupport.github.io/developer-manual/cypher/execution-plans/execution-plans.html)을 참조하십시오.

+ [쿼리는 어떻게 실행됩니까?](https://mossupport.github.io/developer-manual/cypher/query-tuning/how-are-queries-executed.html)
+ [쿼리 프로파일링](https://mossupport.github.io/developer-manual/cypher/query-tuning/how-do-i-profile-a-query.html)
+ [기본 쿼리 튜닝 예제](https://mossupport.github.io/developer-manual/cypher/query-tuning/basic-query-tuning-example.html)
+ [USING](https://mossupport.github.io/developer-manual/cypher/query-tuning/using.html)