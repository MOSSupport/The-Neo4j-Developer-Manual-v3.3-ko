### 3.2.7. Operators

- 연산자 개요
- 일반 연산자
  - Using the `DISTINCT` operator
  - Accessing properties of a nested literal map using the `.` operator
  - Filtering on a dynamically-computed property key using the `[\]` operator
  - 수리 연산자
  - Using the exponentiation operator `^`
  - Using the unary minus operator `-`
- 비교 연산자
  - 두 가지 숫자 비교 
  - 이름 필터링을 위한 ```STARTS WITH``` 사용
- 논리 연산자
  - 숫자 필터링을 위한 논리 연산자 사용
- 문자열 연산자
  - Using a regular expression with `=~` to filter words
- 임시 연산자
  - Adding and subtracting a *Duration* to or from a temporal instant]
  - Adding and subtracting a *Duration* to or from another *Duration*
  - Multiplying and dividing a *Duration* with or by a number
- 리스트 연산자
  - [Concatenating two lists using `+`
  - [Using `IN` to check if a number is in a list]
  - [Using `IN` for more complex list membership operations
  - [Accessing elements in a list using the `[\]` operator
- 속성 연산자 
- 값 일치 및 비교
- 값 순서 및 비교
- 비교 연산자 결합

#### 3.2.7.1. 연산자 개요

| [General operators](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/operators/#query-operators-general) | `DISTINCT`, `.` for property access, `[]` for dynamic property access 

| [Mathematical operators](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/operators/#query-operators-mathematical) | `+`, `-`, `*`, `/`, `%`, `^`

| [Comparison operators](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/operators/#query-operators-comparison) | `=`, `<>`, `<`, `>`, `<=`, `>=`, `IS NULL`, `IS NOT NULL`    |
| [String-specific comparison operators](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/operators/#query-operators-comparison) | `STARTS WITH`, `ENDS WITH`, `CONTAINS`                       |
| [Boolean operators](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/operators/#query-operators-boolean) | `AND`, `OR`, `XOR`, `NOT`                                    |
| [String operators](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/operators/#query-operators-string) | `+` for concatenation, `=~` for regex matching               |
| [Temporal operators](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/operators/#query-operators-temporal) | `+` and `-` for operations between durations and temporal instants/durations, `*` and `/` for operations between durations and numbers |
| [List operators](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/operators/#query-operators-list) | `+` for concatenation, `IN` to check existence of an element in a list, `[]` for accessing element(s) |


#### 3.2.7.2. General operators

The general operators comprise:

- remove duplicates values: `DISTINCT`
- access the property of a node, relationship or literal map using the dot operator: `.`
- dynamic property access using the subscript operator: `[]`

##### Using the `DISTINCT` operator

Retrieve the unique eye colors from `Person` nodes.

Query. 

```
CREATE (a:Person { name: 'Anne', eyeColor: 'blue' }),(b:Person { name: 'Bill', eyeColor: 'brown' }),(c:Person { name: 'Carol', eyeColor: 'blue' })
WITH [a, b, c] AS ps
UNWIND ps AS p
RETURN DISTINCT p.eyeColor
```

Even though both **'Anne'** and **'Carol'** have blue eyes, **'blue'** is only returned once.

| p.eyeColor                                                |
| --------------------------------------------------------- |
| 2 rows Nodes created: 3 Properties set: 6 Labels added: 3 |
| `"blue"`                                                  |
| `"brown"`                                                 |

`DISTINCT` is commonly used in conjunction with [aggregating functions](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/aggregating/).

##### Accessing properties of a nested literal map using the `.` operator

**쿼리**

```
WITH { person: { name: 'Anne', age: 25 }} AS p
RETURN p.person.name
```

| p.person.name |
| ------------- |
| 1 row         |
| `"Anne"`      |

##### Filtering on a dynamically-computed property key using the `[]` operator

**쿼리**. 

```
CREATE (a:Restaurant { name: 'Hungry Jo', rating_hygiene: 10, rating_food: 7 }),(b:Restaurant { name: 'Buttercup Tea Rooms', rating_hygiene: 5, rating_food: 6 }),(c1:Category { name: 'hygiene' }),(c2:Category { name: 'food' })
WITH a, b, c1, c2
MATCH (restaurant:Restaurant),(category:Category)
WHERE restaurant["rating_" + category.name]> 6
RETURN DISTINCT restaurant.name
```

| restaurant.name                                          |
| -------------------------------------------------------- |
| 1 row Nodes created: 4 Properties set: 8 Labels added: 4 |
| `"Hungry Jo"`                                            |

See [Section 3.3.7.2, “Basic usage”](https://neo4j.com/docs/developer-manual/3.4/cypher/clauses/where/#query-where-basic) for more details on dynamic property access.

|      | The behavior of the `[]` operator with respect to `null` is detailed [here](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/working-with-null/#cypher-null-bracket-operator). |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

#### 3.2.7.3. Mathematical operators

The mathematical operators comprise:

- addition: `+`
- subtraction or unary minus: `-`
- multiplication: `*`
- division: `/`
- modulo division: `%`
- exponentiation: `^`

##### Using the exponentiation operator `^`

**쿼리** 

```
WITH 2 AS number, 3 AS exponent
RETURN number ^ exponent AS result
```

| result |
| ------ |
| 1 row  |
| `8.0`  |

##### Using the unary minus operator `-`

**쿼리** 

```
WITH -3 AS a, 4 AS b
RETURN b - a AS result
```

| result |
| ------ |
| 1 row  |
| `7`    |

#### 3.2.7.4. Comparison operators

The comparison operators comprise:

- equality: `=`
- inequality: `<>`
- less than: `<`
- greater than: `>`
- less than or equal to: `<=`
- greater than or equal to: `>=`
- `IS NULL`
- `IS NOT NULL`

##### String-specific comparison operators comprise:

- `STARTS WITH`: perform case-sensitive prefix searching on strings
- `ENDS WITH`: perform case-sensitive suffix searching on strings
- `CONTAINS`: perform case-sensitive inclusion searching in strings

##### Comparing two numbers

**쿼리** 

```
WITH 4 AS one, 3 AS two
RETURN one > two AS result
```

| result |
| ------ |
| 1 row  |
| `true` |

See [Section 3.2.7.10, “Equality and comparison of values”](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/operators/#cypher-comparison) for more details on the behavior of comparison operators, and [Section 3.3.7.8, “Using ranges”](https://neo4j.com/docs/developer-manual/3.4/cypher/clauses/where/#query-where-ranges) for more examples showing how these may be used.

##### Using `STARTS WITH` to filter names

**쿼리** 

```
WITH ['John', 'Mark', 'Jonathan', 'Bill'] AS somenames
UNWIND somenames AS names
WITH names AS candidate
WHERE candidate STARTS WITH 'Jo'
RETURN candidate
```

| candidate    |
| ------------ |
| 2 rows       |
| `"John"`     |
| `"Jonathan"` |

[Section 3.3.7.3, “String matching”](https://neo4j.com/docs/developer-manual/3.4/cypher/clauses/where/#query-where-string) contains more information regarding the string-specific comparison operators as well as additional examples illustrating the usage thereof.

#### 3.2.7.5. 논리 연산자 

The boolean operators — also known as logical operators — comprise:

- conjunction: `AND`
- disjunction: `OR`,
- exclusive disjunction: `XOR`
- negation: `NOT`

Here is the truth table for `AND`, `OR`, `XOR` and `NOT`.

| a       | b       | a `AND` b | a `OR` b | a `XOR` b | `NOT` a |
| ------- | ------- | --------- | -------- | --------- | ------- |
| `false` | `false` | `false`   | `false`  | `false`   | `true`  |
| `false` | `null`  | `false`   | `null`   | `null`    | `true`  |
| `false` | `true`  | `false`   | `true`   | `true`    | `true`  |
| `true`  | `false` | `false`   | `true`   | `true`    | `false` |
| `true`  | `null`  | `null`    | `true`   | `null`    | `false` |
| `true`  | `true`  | `true`    | `true`   | `false`   | `false` |
| `null`  | `false` | `false`   | `null`   | `null`    | `null`  |
| `null`  | `null`  | `null`    | `null`   | `null`    | `null`  |
| `null`  | `true`  | `null`    | `true`   | `null`    | `null`  |

##### Using boolean operators to filter numbers

**쿼리** 

```
WITH [2, 4, 7, 9, 12] AS numberlist
UNWIND numberlist AS number
WITH number
WHERE number = 4 OR (number > 6 AND number < 10)
RETURN number
```

| number |
| ------ |
| 3 rows |
| `4`    |
| `7`    |
| `9`    |

#### 3.2.7.6. 문자열 연산자 

String operators comprise:

- concatenating strings: `+`
- matching a regular expression: `=~`

##### Using a regular expression with `=~` to filter words

**쿼리** 

```
WITH ['mouse', 'chair', 'door', 'house'] AS wordlist
UNWIND wordlist AS word
WITH word
WHERE word =~ '.*ous.*'
RETURN word
```

| word      |
| --------- |
| 2 rows    |
| `"mouse"` |
| `"house"` |

Further information and examples regarding the use of regular expressions in filtering can be found in [Section 3.3.7.4, “Regular expressions”](https://neo4j.com/docs/developer-manual/3.4/cypher/clauses/where/#query-where-regex). In addition, refer to [the section called “String-specific comparison operators comprise:”](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/operators/#query-operator-comparison-string-specific) for details on string-specific comparison operators.

#### 3.2.7.7. 임시 연산자 

Temporal operators comprise:

- adding a [*Duration*](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/temporal/#cypher-temporal-durations) to either a [temporal instant](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/temporal/#cypher-temporal-instants) or another *Duration*: `+`
- subtracting a *Duration* from either a temporal instant or another *Duration*: `-`
- multiplying a *Duration* with a number: `*`
- dividing a *Duration* by a number: `/`

The following table shows — for each combination of operation and operand type — the type of the value returned from the application of each temporal operator:

| Operator                                                     | Left-hand operand                                            | Right-hand operand                                           | Type of result                   |
| ------------------------------------------------------------ | ------------------------------------------------------------ | ------------------------------------------------------------ | -------------------------------- |
| [`+`](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/operators/#syntax-add-subtract-duration-to-temporal-instant) | Temporal instant                                             | *Duration*                                                   | The type of the temporal instant |
| [`+`](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/operators/#syntax-add-subtract-duration-to-temporal-instant) | *Duration*                                                   | Temporal instant                                             | The type of the temporal instant |
| [`-`](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/operators/#syntax-add-subtract-duration-to-temporal-instant) | Temporal instant                                             | *Duration*                                                   | The type of the temporal instant |
| [`+`](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/operators/#syntax-add-subtract-duration-to-duration) | *Duration*                                                   | *Duration*                                                   | *Duration*                       |
| [`-`](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/operators/#syntax-add-subtract-duration-to-duration) | *Duration*                                                   | *Duration*                                                   | *Duration*                       |
| [`*`](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/operators/#syntax-multiply-divide-duration-number) | *Duration*                                                   | [Number](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/values/#property-types) | *Duration*                       |
| [`*`](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/operators/#syntax-multiply-divide-duration-number) | [Number](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/values/#property-types) | *Duration*                                                   | *Duration*                       |
| [`/`](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/operators/#syntax-multiply-divide-duration-number) | *Duration*                                                   | [Number](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/values/#property-types) | *Duration*                       |

##### Adding and subtracting a *Duration* to or from a temporal instant

**쿼리** 

```
WITH localdatetime({ year:1984, month:10, day:11, hour:12, minute:31, second:14 }) AS aDateTime, duration({ years: 12, nanoseconds: 2 }) AS aDuration
RETURN aDateTime + aDuration, aDateTime - aDuration
```

| aDateTime + aDuration           | aDateTime - aDuration           |
| ------------------------------- | ------------------------------- |
| 1 row                           |                                 |
| `1996-10-11T12:31:14.000000002` | `1972-10-11T12:31:13.999999998` |

[Components of a *Duration*](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/temporal/#cypher-temporal-duration-component) that do not apply to the temporal instant are ignored. For example, when adding a *Duration* to a *Date*, the *hours*, *minutes*, *seconds* and *nanoseconds* of the *Duration* are ignored (*Time* behaves in an analogous manner):

**쿼리** 

```
WITH date({ year:1984, month:10, day:11 }) AS aDate, duration({ years: 12, nanoseconds: 2 }) AS aDuration
RETURN aDate + aDuration, aDate - aDuration
```

| aDate + aDuration | aDate - aDuration |
| ----------------- | ----------------- |
| 1 row             |                   |
| `1996-10-11`      | `1972-10-11`      |

Adding two durations to a temporal instant is not an associative operation. This is because non-existing dates are truncated to the nearest existing date:

**쿼리** 

```
RETURN (date("2011-01-31")+ duration("P1M"))+ duration("P12M") AS date1, date("2011-01-31")+(duration("P1M")+ duration("P12M")) AS date2
```

| date1        | date2        |
| ------------ | ------------ |
| 1 row        |              |
| `2012-02-28` | `2012-02-29` |

##### Adding and subtracting a *Duration* to or from another *Duration*

**쿼리** 

```
WITH duration({ years: 12, months: 5, days: 14, hours: 16, minutes: 12, seconds: 70, nanoseconds: 1 }) AS duration1, duration({ months:1, days: -14, hours: 16, minutes: -12, seconds: 70 }) AS duration2
RETURN duration1, duration2, duration1 + duration2, duration1 - duration2
```

| duration1                       | duration2           | duration1 + duration2       | duration1 - duration2       |
| ------------------------------- | ------------------- | --------------------------- | --------------------------- |
| 1 row                           |                     |                             |                             |
| `P12Y5M14DT16H13M10.000000001S` | `P1M-14DT15H49M10S` | `P12Y6MT32H2M20.000000001S` | `P12Y4M28DT24M0.000000001S` |

##### Multiplying and dividing a *Duration* with or by a number

These operations are interpreted simply as component-wise operations with overflow to smaller units based on an average length of units in the case of division (and multiplication with fractions).

**쿼리** 

```
WITH duration({ days: 14, minutes: 12, seconds: 70, nanoseconds: 1 }) AS aDuration
RETURN aDuration, aDuration * 2, aDuration / 3
```

| aDuration               | aDuration * 2           | aDuration / 3            |
| ----------------------- | ----------------------- | ------------------------ |
| 1 row                   |                         |                          |
| `P14DT13M10.000000001S` | `P28DT26M20.000000002S` | `P4DT16H4M23.333333333S` |

#### 3.2.7.8. List operators

List operators comprise:

- concatenating lists `l1` and `l2`: `[l1] + [l2]`
- checking if an element `e` exists in a list `l`: `e IN [l]`
- accessing an element(s) in a list using the subscript operator: `[]`

|      | The behavior of the `IN` and `[]` operators with respect to `null` is detailed [here](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/working-with-null/). |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

##### Concatenating two lists using `+`

**쿼리** 

```
RETURN [1,2,3,4,5]+[6,7] AS myList
```

| myList                                 |
| -------------------------------------- |
| 1 row                                  |
| `JavaListWrapper(1, 2, 3, 4, 5, 6, 7)` |

##### Using `IN` to check if a number is in a list

**쿼리** 

```
WITH [2, 3, 4, 5] AS numberlist
UNWIND numberlist AS number
WITH number
WHERE number IN [2, 3, 8]
RETURN number
```

| number |
| ------ |
| 2 rows |
| `2`    |
| `3`    |

##### Using `IN` for more complex list membership operations

The general rule is that the `IN` operator will evaluate to `true` if the list given as the right-hand operand contains an element which has the same *type and contents (or value)* as the left-hand operand. Lists are only comparable to other lists, and elements of a list `l` are compared pairwise in ascending order from the first element in `l` to the last element in `l`.

The following query checks whether or not the list `[2, 1]` is an element of the list `[1, [2, 1], 3]`:

**쿼리** 

```
RETURN [2, 1] IN [1,[2, 1], 3] AS inList
```

The query evaluates to `true` as the right-hand list contains, as an element, the list `[1, 2]` which is of the same type (a list) and contains the same contents (the numbers `2` and `1` in the given order) as the left-hand operand. If the left-hand operator had been `[1, 2]` instead of `[2, 1]`, the query would have returned `false`.

| inList |
| ------ |
| 1 row  |
| `true` |

At first glance, the contents of the left-hand operand and the right-hand operand *appear* to be the same in the following query:

**쿼리** 

```
RETURN [1, 2] IN [1, 2] AS inList
```

However, `IN` evaluates to `false` as the right-hand operand does not contain an element that is of the same *type* — i.e. a *list* — as the left-hand-operand.

| inList  |
| ------- |
| 1 row   |
| `false` |

The following query can be used to ascertain whether or not a list `llhs` — obtained from, say, the [labels()](https://neo4j.com/docs/developer-manual/3.4/cypher/functions/list/#functions-labels) function — contains at least one element that is also present in another list `lrhs`:

```
MATCH (n)
WHERE size([l IN labels(n) WHERE l IN ['Person', 'Employee'] | 1]) > 0
RETURN count(n)
```

As long as `labels(n)` returns either `Person` or `Employee` (or both), the query will return a value greater than zero.

##### Accessing elements in a list using the `[]` operator

**쿼리** 

```
WITH ['Anne', 'John', 'Bill', 'Diane', 'Eve'] AS names
RETURN names[1..3] AS result
```

The square brackets will extract the elements from the start index `1`, and up to (but excluding) the end index `3`.

| result                        |
| ----------------------------- |
| 1 row                         |
| `JavaListWrapper(John, Bill)` |

More details on lists can be found in [Section 3.2.11.1, “Lists in general”](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/lists/#cypher-lists-general).

#### 3.2.7.9. Property operators

|      | Since version 2.0, the previously supported property operators `?` and `!` have been removed. This syntax is no longer supported. Missing properties are now returned as `null`. Please use `(NOT(exists(<ident>.prop)) OR <ident>.prop=<value>)` if you really need the old behavior of the `?` operator. — Also, the use of `?` for optional relationships has been removed in favor of the newly-introduced `OPTIONAL MATCH` clause. |
| ---- | ------------------------------------------------------------ |
|      |                                                              |

#### 3.2.7.10. Equality and comparison of values

##### Equality

Cypher supports comparing values (see [Section 3.2.1, “Values and types”](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/values/)) by equality using the `=` and `<>` operators.

Values of the same type are only equal if they are the same identical value (e.g. `3 = 3` and `"x" <> "xy"`).

Maps are only equal if they map exactly the same keys to equal values and lists are only equal if they contain the same sequence of equal values (e.g. `[3, 4] = [1+2, 8/2]`).

Values of different types are considered as equal according to the following rules:

- Paths are treated as lists of alternating nodes and relationships and are equal to all lists that contain that very same sequence of nodes and relationships.
- Testing any value against `null` with both the `=` and the `<>` operators always is `null`. This includes `null = null`and `null <> null`. The only way to reliably test if a value `v` is `null` is by using the special `v IS NULL`, or `v IS NOT NULL` equality operators.

All other combinations of types of values cannot be compared with each other. Especially, nodes, relationships, and literal maps are incomparable with each other.

It is an error to compare values that cannot be compared.

#### 3.2.7.11. Ordering and comparison of values

The comparison operators `<=`, `<` (for ascending) and `>=`, `>` (for descending) are used to compare values for ordering. The following points give some details on how the comparison is performed.

- Numerical values are compared for ordering using numerical order (e.g. `3 < 4` is true).
- The special value `java.lang.Double.NaN` is regarded as being larger than all other numbers.
- String values are compared for ordering using lexicographic order (e.g. `"x" < "xy"`).
- Boolean values are compared for ordering such that `false < true`.
- **Comparison** of spatial values:
  - Point values can only be compared within the same Coordinate Reference System (CRS) — otherwise, the result will be `null`.
  - For two points `a` and `b` within the same CRS, `a` is considered to be greater than `b` if `a.x > b.x` and `a.y > b.y` (and `a.z > b.z` for 3D points).
  - `a` is considered less than `b` if `a.x < b.x` and `a.y < b.y` (and `a.z < b.z` for 3D points).
  - If none if the above is true, the points are considered incomparable and any comparison operator between them will return `null`.
- **Ordering** of spatial values:
  - `ORDER BY` requires all values to be orderable.
  - Points are ordered after arrays and before temporal types.
  - Points of different CRS are ordered by the CRS code (the value of SRID field). For the currently supported set of [Coordinate Reference Systems](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/spatial/#cypher-spatial-crs) this means the order: 4326, 4979, 7302, 9157
  - Points of the same CRS are ordered by each coordinate value in turn, `x` first, then `y` and finally `z`.
  - Note that this order is different to the order returned by the spatial index, which will be the order of the space filling curve.
- **Comparison** of temporal values:
  - [Temporal instant values](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/temporal/#cypher-temporal-instants) are comparable within the same type. An instant is considered less than another instant if it occurs before that instant in time, and it is considered greater than if it occurs after.
  - Instant values that occur at the same point in time — but that have a different time zone — are not considered equal, and must therefore be ordered in some predictable way. Cypher prescribes that, after the primary order of point in time, instant values be ordered by effective time zone offset, from west (negative offset from UTC) to east (positive offset from UTC). This has the effect that times that represent the same point in time will be ordered with the time with the earliest local time first. If two instant values represent the same point in time, and have the same time zone offset, but a different named time zone (this is possible for *DateTime* only, since *Time* only has an offset), these values are not considered equal, and ordered by the time zone identifier, alphabetically, as its third ordering component.
  - [*Duration*](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/temporal/#cypher-temporal-durations) values cannot be compared, since the length of a *day*, *month* or *year* is not known without knowing which *day*, *month* or *year* it is. Since *Duration* values are not comparable, the result of applying a comparison operator between two *Duration* values is `null`. If the type, point in time, offset, and time zone name are all equal, then the values are equal, and any difference in order is impossible to observe.
- **Ordering** of temporal values:
  - `ORDER BY` requires all values to be orderable.
  - Temporal instances are ordered after spatial instances and before strings.
  - Comparable values should be ordered in the same order as implied by their comparison order.
  - Temporal instant values are first ordered by type, and then by comparison order within the type.
  - Since no complete comparison order can be defined for *Duration* values, we define an order for `ORDER BY`specifically for *Duration*:
    - *Duration* values are ordered by normalising all components as if all years were `365.2425` days long (`PT8765H49M12S`), all months were `30.436875` (`1/12` year) days long (`PT730H29M06S`), and all days were `24`hours long [[1\]](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/operators/#ftn.d0e7032).
- Comparing for ordering when one argument is `null` (e.g. `null < 3` is `null`).

#### 3.2.7.12. Chaining comparison operations

Comparisons can be chained arbitrarily, e.g., `x < y <= z` is equivalent to `x < y AND y <= z`.

Formally, if `a, b, c, ..., y, z` are expressions and `op1, op2, ..., opN` are comparison operators, then `a op1 b op2 c ... y opN z` is equivalent to `a op1 b and b op2 c and ... y opN z`.

Note that `a op1 b op2 c` does not imply any kind of comparison between `a` and `c`, so that, e.g., `x < y > z` is perfectly legal (although perhaps not elegant).

The example:

```
MATCH (n) WHERE 21 < n.age <= 30 RETURN n
```

is equivalent to

```
MATCH (n) WHERE 21 < n.age AND n.age <= 30 RETURN n
```

Thus it will match all nodes where the age is between 21 and 30.

This syntax extends to all equality and inequality comparisons, as well as extending to chains longer than three.

For example:

```
a < b = c <= d <> e
```

Is equivalent to:

```
a < b AND b = c AND c <= d AND d <> e
```

For other comparison operators, see [Section 3.2.7.4, “Comparison operators”](https://neo4j.com/docs/developer-manual/3.4/cypher/syntax/operators/#query-operators-comparison).