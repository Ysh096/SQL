# SQL JOIN

앞으로 치게 될 코테에 SQL 문제가 나올 것 같아 준비하려고 한다.

지금까지는 JOIN을 잘 몰라서 하나의 테이블에서 어떤 정보를 얻은 후, 다른 테이블에서 그 정보를 이용하여 새로운 정보를 얻는 귀찮은 과정을 거쳤던 것 같다. 오늘 단단히 배워놔서 두고두고 잊어먹지 않고 사용해야겠다.



### 정의

JOIN은 둘 이상의 테이블을 연결해서 데이터를 검색하는 방법으로, 연결하려는 테이블들이 적어도 하나의 컬럼을 공유하고 있어야 한다.

예를 들어, 어떤 기업의 직원 리스트가 다음과 같이 주어진다고 해보자.

| No   | Name   | job  | deptNo |
| ---- | ------ | ---- | ------ |
| 1001 | 김범수 | 사원 | 20     |
| 1002 | 정영길 | 사원 | 30     |
| 1003 | 나일환 | 사원 | 30     |
| 1004 | 김덕환 | 대리 | 20     |
| 1005 | 김형준 | 사원 | 30     |
| 1006 | 채병훈 | 과장 | 30     |
| 1007 | 김병섭 | 과장 | 10     |
| 1008 | 이명섭 | 부장 | 30     |

여기서 각 직원의 이름과 부서 번호만 읽어오면 다음과 같다.

| Name   | deptNo |
| ------ | ------ |
| 김범수 | 20     |
| 정영길 | 30     |
| 나일환 | 30     |
| 김덕환 | 20     |
| 김형준 | 30     |
| 채병훈 | 30     |
| 김병섭 | 10     |
| 이명섭 | 30     |

그런데 번호로 주어진 부서는 사람이 알아볼 수 없다. 이 부서에 대한 정보는 다른 테이블에 다음과 같이 존재한다고 하자.

| No   | deptNo | deptName | Region |
| ---- | ------ | -------- | ------ |
| 1    | 10     | 경리부   | 서울   |
| 2    | 20     | 인사부   | 인천   |
| 3    | 30     | 영업부   | 대전   |
| 4    | 40     | 전산부   | 부산   |

이 두 테이블을 엮어서 조회하면 한번에 직원의 이름과 부서 이름을 나타낼 수 있을 것이다.

이렇게 조인을 하는 방법에는 크게 네 가지가 있다.



### INNER JOIN

가장 기본적인 조인 방법으로, 두 개의 테이블에서 공통된 요소들을 통해 결합하는 조인 방식

정의에서 하고자 한 조인 방식이 INNER JOIN이다.

기본적인 이너조인의 SQL 형태는 다음과 같다.

```sql
SELECT table1.col1, table1.col2, ..., table2.col1, table2.col2, ...
FROM table1 [table1의 별칭]
JOIN table2 [table2의 별칭] ON table1.col1 = table2.col2
```

보고자 하는 결과를 SELECT에 모두 써주고

FROM 에서 기본이 되는 테이블을 선택하고

JOIN 을 통해 조인할 테이블을 선택하고

ON 을 통해 FROM의 테이블과 JOIN의 테이블의 어떤 컬럼을 엮을지 선택한다.



INNER JOIN을 통해 직원의 이름과 부서명을 함께 출력해보자.

```sql
SELECT employee.Name, department.deptName FROM employee JOIN department
on employee.deptNo = department.deptNo
```

이렇게 엮어주려고 할 때 테이블명도 컬럼 명(Name, deptName 등) 앞에 써줘야 한다.



### OUTER JOIN

아우터 조인은 레프트 아우터 조인, 라이트 아우터 조인, 이 둘을 합친 풀 아우터 조인의 세 가지가 있다.

아우터 조인은 두 테이블의 공통영역을 포함하여 한쪽 테이블의 다른 데이터를 포함하는 조인 방식이다.

INNER JOIN에서 두 테이블을 연결한 결과는 다음과 같다.

| Name   | deptNo |
| ------ | ------ |
| 김범수 | 인사부 |
| 정영길 | 영업부 |
| 나일환 | 영업부 |
| 김덕환 | 인사부 |
| 김형준 | 영업부 |
| 채병훈 | 영업부 |
| 김병섭 | 경리부 |
| 이명섭 | 영업부 |

그런데 만약 문정진이라는 사람이 deptNo가 없는 (NULL) 상태로 DB에 저장이 되어 있었다고 하면, 위 결과에서 문정진은 누락된다.

만약 이렇게 누락된 결과까지 보고자 할 때 OUTER JOIN을 이용한다.

- left와 right outer의 기준 => from에 적어준 테이블이 left, join에 적어준 테이블이 right

**부서코드가 NULL이거나 부서테이블의 deptNo와 일치하는 값이 없는 사원까지 출력하는 left 아우터 조인**

```sql
SELECT employee.Name, department.deptName
FROM employee
LEFT OUTER JOIN department on employee.deptNo = department.deptNo
```

이렇게 하면 다음의 결과를 얻는다.

| Name   | deptName |
| ------ | -------- |
| 김범수 | 인사부   |
| 정영길 | 영업부   |
| 나일환 | 영업부   |
| 김덕환 | 인사부   |
| 김형준 | 영업부   |
| 채병훈 | 영업부   |
| 김병섭 | 경리부   |
| 이명섭 | 영업부   |
| 문정진 | NULL     |

즉, FROM에 적은 left의 결과는 모두 보여주고, 일치하는게 없으면 join 테이블의 결과로는 NULL이 나오는 것..

이제 [프로그래머스의 없어진 기록 찾기](https://programmers.co.kr/learn/courses/30/lessons/59042) 문제를 풀 수 있다.

```sql
SELECT ANIMAL_OUTS.ANIMAL_ID, ANIMAL_OUTS.NAME
FROM ANIMAL_OUTS LEFT OUTER JOIN ANIMAL_INS
ON ANIMAL_OUTS.ANIMAL_ID = ANIMAL_INS.ANIMAL_ID
WHERE ANIMAL_INS.ANIMAL_ID is NULL
```

더 보기 좋게, 반복을 없애서 풀면,

```sql
SELECT OUTS.ANIMAL_ID, OUTS.NAME
FROM ANIMAL_OUTS OUTS LEFT OUTER JOIN ANIMAL_INS INS
ON OUTS.ANIMAL_ID = INS.ANIMAL_ID
WHERE INS.ANIMAL_ID is NULL
```



RIGHT OUTER JOIN은 JOIN에 적은 테이블의 결과(right)는 모두 보여주고, 일치하는게 없는 FROM에 적은 left의 결과는 NULL 처리하여 보여준다.

따라서 결과는 다음과 같을 것이다.

| Name   | deptName |
| ------ | -------- |
| 김범수 | 인사부   |
| 김덕환 | 인사부   |
| 나일환 | 영업부   |
| 정영길 | 영업부   |
| 김형준 | 영업부   |
| 채병훈 | 영업부   |
| 이명섭 | 영업부   |
| 김병섭 | 경리부   |
| NULL   | 전산부   |

문정진이가 있는지 없는지는 이제 중요하지 않고, deptName의 전산부에 해당하는 직원이 없다는 것이 표시된다.



FULL OUTER JOIN은 위 둘을 섞은 것으로, 양 쪽에서 해당되는게 없는 결과까지 모두 보여준다.

| Name   | deptName |
| ------ | -------- |
| 김범수 | 인사부   |
| 정영길 | 영업부   |
| 나일환 | 영업부   |
| 김덕환 | 인사부   |
| 김형준 | 영업부   |
| 채병훈 | 영업부   |
| 김병섭 | 경리부   |
| 이명섭 | 영업부   |
| 문정진 | NULL     |
| NULL   | 전산부   |



### CROSS JOIN

크로스 조인은 두 테이블로 만들 수 있는 가능한 모든 경우의 수에 대한 결합을 결과로 보여준다.

6개 행짜리 테이블과 4개 행짜리 테이블을 cross 하면 24개 행의 결과가 나온다.





### SELF JOIN

참조할 테이블이 다른 테이블이 아니라 자기 자신인 경우!

예를 들어 각 직원마다 manager라는 사람이 배정되어 있다고 할 때,

| No   | Name   | job  | manager | deptNo |
| ---- | ------ | ---- | ------- | ------ |
| 1001 | 김범수 | 사원 | 1004    | 20     |
| 1002 | 정영길 | 사원 | 1005    | 30     |
| 1003 | 나일환 | 사원 | 1005    | 30     |
| 1004 | 김덕환 | 대리 | 1008    | 20     |
| 1005 | 김형준 | 사원 | 1007    | 30     |
| 1006 | 채병훈 | 과장 | 1002    | 30     |
| 1007 | 김병섭 | 과장 | 1002    | 10     |
| 1008 | 이명섭 | 부장 | 1003    | 30     |

manager가 누군지 알고 싶을 때, 다른 테이블이 아니라 자기 자신을 참조하면 이름을 알아낼 수 있다.

```sql
SELECT emp1.No 사원번호, emp1.Name 직원이름, emp1.manager 매니저번호, emp2.Name 매니저이름
FROM employee emp1
JOIN employee emp2 on emp1.manager = emp2.empNo
```

이렇게 같은 테이블을 join 하지만 별칭을 적어줘서 구분한다.

그 결과는 다음과 같을 것이다.

| 사원번호 | 직원이름 | 매니저번호 | 매니저이름 |
| -------- | -------- | ---------- | ---------- |
| 1001     | 김범수   | 1004       | 김덕환     |
| 1002     | 정영길   | 1005       | 김형준     |
| 1003     | 나일환   | 1005       | 김형준     |
| 1004     | 김덕환   | 1008       | 이명섭     |
| 1005     | 김형준   | 1007       | 김병섭     |
| 1006     | 채병훈   | 1002       | 정영길     |
| 1007     | 김병섭   | 1002       | 정영길     |
| 1008     | 이명섭   | 1003       | 나일환     |



https://pearlluck.tistory.com/46

https://doorbw.tistory.com/223

https://doorbw.tistory.com/223

