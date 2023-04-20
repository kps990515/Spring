## Join

### ANSI / ORACLE EQUAL JOIN
```SQL
-- using, 칼럼명같고 datatype다를때 사용가능, 동일칼럼 한개만 조인에 사용가능
select empno, ename, deptno, dname
from emp join dept
using(deptno);


-- on, 칼럼명이 달라도 조인할 칼럼이나 임의의 조인조건 명시가능
select e.empno, e.ename, d.deptno, d.dname
from emp e join dept d
on(e.deptno = d.deptno);
--> ANSI SQL 표준

select e.empno, e.ename, d.deptno, d.dname
from emp e, dept d
where e.deptno = d.deptno;
--> oracle join 문법
```

### BETWEEN은 비교범위에 대해 정확히 명시해야한다
 - 2000/01/01부터 2009/12/31까지 구하시요

 ```SQL
 -- 틀린답(2000-01-01이 빠짐)
 B.TIME_ID BETWEEN TO_DATE(2000, 'YYYY') AND TO_DATE(2009, 'YYYY')

 -- 맞는답
 B.TIME_ID BETWEEN TO_DATE('2000/01/01', 'YYYY/MM/DD') AND TO_DATE('2009/12/31', 'YYYY/MM/DD')
 ```

### DISTINCT는 해롭다
 - 1980년생 고객 중에 2000년에 구매한 이력이 있는 사람
 - 1:M 관계의 테이블에서 조인을 사용하면 M을 기준으로 모든 데이터 조회한다

 1. DISTINCT 사용
 ```SQL
 -- DISTINCT 사용안하면 2000년도 구매이력이 전체 붙어서 나온다
 SELECT DISTINCT A.CUST_ID, 
       A.CUST_FIRST_NAME,
       A.CUST_LAST_NAME,
       A.CUST_GENDER,
       A.COUNTRY_ID
  FROM CUSTS A, 
       SALES B
 WHERE A.CUST_ID = B.CUST_ID
   AND A.CUST_YEAR_OF_BIRTH = 1980
   AND B.TIME_ID BETWEEN TO_DATE('2000-01-01', 'YYYY-MM-DD') 
                 AND TO_DATE('2000-12-31', 'YYYY-MM-DD');
 ``` 

 2. DISTINCT 미사용
 ```SQL
 -- B에서 구매이력한 CUST_ID를 가져와서 A테이블에 있는 고객만 조회
SELECT A.CUST_ID, 
       A.CUST_FIRST_NAME,
       A.CUST_LAST_NAME,
       A.CUST_GENDER,
       A.COUNTRY_ID
  FROM CUSTS A
 WHERE A.CUST_YEAR_OF_BIRTH = 1980
   AND A.CUST_ID IN (SELECT CUST_ID 
                       FROM SALES B
                      WHERE TIME_ID BETWEEN TO_DATE('2000-01-01', 'YYYY-MM-DD') 
                            AND TO_DATE('2000-12-31', 'YYYY-MM-DD'));
 ```

### NO EQUAL JOIN
- =이 아닌 >,<=, BETWEEN 등등을 활용한 JOIN

```SQL
SELECT E.ENAME, E.SAL, S.GRADE
FROM EMP E, SALGARDE S
WHERE E.SAL BETWEEN S.LOCAL AND S.HISAL
```

### OUTER JOIN
 - 한쪽에 데이터가 없어도 전체 다 보고 싶을때 사용
 - (+) 붙인 반대쪽 데이터를 다 보여줌

 - ORACLE
 ```SQL
 SELECT E.EMPNO, E.ENAME, D.DEPT_NAME
   FROM EMP E, DEPT D
  WHERE E.DEPTNO = D.DEPTNO(+) -- EMP테이블을 다 보여주고 DEPT를 붙임
 ``` 

 -ANSI
 ```SQL
 SELECT E.EMPNO, E.ENAME, D.DEPT_NAME
   FROM EMP E 
   LEFT OUTER JOIN DEPT D
     ON E.DEPTNO = D.DEPTNO;
 ```

 #### 2000이상의 급여를 받는 사원정보와 부서정보를 가져와라(사원이 없는 부서도 포함)
 - ORACLE : (+)를 붙인 곳 까지 ON조건으로 들어감(중요!!!)
 ```SQL
 SELECT A.DEPTNO AS DEPT_DEPTNO
       ,A.DNAME
       ,B.DEPTNO AS EMP_DEPTNO
       ,B.EMPNO
       ,B.ENAME
       ,B.SAL
  FROM DEPT A, EMP B
 WHERE A.DEPTNO = B.DEPTNO(+) -- 부서정보를 다 가져오고 사원을 붙임 -> 사원없는 부서도 포함됨
   AND B.SAL(+) >= 2000 ; -- (+)를 연속적으로 넣었기에 LEFT OUTER JOIN 유지(사원없는 부서 정보 유지) 
 ```

 - ANSI
 ```SQL
 SELECT d.deptno AS dept_deptno, d.dname
      ,e.deptno AS emp_deptno, e.empno, e.ename, e.sal
  FROM dept d LEFT OUTER JOIN emp e
    ON d.deptno = e.deptno
   AND e.sal   >= 2000 ;

 -- 이렇게하면 틀림 마치 B.SAL에서 (+)뺀 효과
 SELECT d.deptno AS dept_deptno, d.dname
      ,e.deptno AS emp_deptno, e.empno, e.ename, e.sal
  FROM dept d LEFT OUTER JOIN emp e
    ON d.deptno = e.deptno
 WHERE e.sal   >= 2000 ; 
```

 ### FULL OUTER JOIN
  - 양쪽 테이블 전체 다 보여주기

  - ANSI
  ```SQL
  SELECT e.last_name, d.department_id, d.department_name
  FROM employees e FULL OUTER JOIN departments d
  ON (e.department_id = d.department_id) ;
  ```

### OUTER JOIN 효율화
 - 필요한 정보만 먼저 추출한 뒤 OUTER JOIN으로 붙이자

 - 기존방식 : B에서는 합계만 필요함에도, 모든 정보를 조인 후 붙임
 ```SQL
 SELECT A.PROD_ID
       ,A.PROD_NAME
       ,SUM(B.QUANTITY_SOLD) AS SOLD_SUM
 FROM PRODS A, SALES B
 WHERE A.PROD_ID = B.PROD_ID(+)
 GROUP BY A.PROD_ID, A.PROD_NAME
 ORDER BY A.PROD_ID;
 ```

 - CORRELATED SUBQUERY : A테이블 건 마다 서브쿼리 실행되면서 비교함 
 ```SQL
  SELECT A.PROD_ID
        ,A.PROD_NAME
        (SELECT SUM(QUANTITY_SOLD) AS SOLD_SUM
           FROM SALES
          WHERE PROD_ID = A.PROD_ID) AS SOLD_SUM
   FROM PRODS A, 
  WHERE A.PROD_ID = B.PROD_ID(+)
  ORDER BY A.PROD_ID;
 ```

 - 효율화 방식 : 필요한 정보(B의 SUM)만 추출 후 조인
 ```SQL
 SELECT A.PROD_ID
       ,A.PROD_NAME
       ,B.SOLD_SUM
 FROM PRODS A, 
      (SELECT PROD_ID, SUM(QUANTITY_SOLD) AS SOLD_SUM
         FROM SALES
       GROUP BY PROD_ID)B  
 WHERE A.PROD_ID = B.PROD_ID(+)
 ORDER BY A.PROD_ID;
 ```

### 서브쿼리의 문제점
 - 쿼리 실행 시 서브쿼리를 실행, 이를 메모리나 임시테이블에 저장한 다음 메인쿼리 실행함
 - SELECT절 : 한번 실행되지만 서브쿼리가 큰 경우 결과반환 시 많은 자원 사용
 - FROM절 : SELECT절과 동일
 - WHERE절 : 매 비교시마다 실행됨(각 행마다 서브쿼리 > 메인쿼리 > 비교 반복)
 - CORRELATED SUBQUERY : 서브쿼리에서 메인컬럼을 참조하기에, 메인쿼리의 각 행마다 실행됨
 - JOIN, IN, EXISTS 사용하자

### NOT IN에서는 NULL체크가 안되니 주의하자
```SQL
SELECT DEPTNO
       ,DNAME
       ,LOC
FROM DEPT 
WHERE DEPTNO NOT IN (SELECT DEPTNO --해당 경우의 DEPTNO = NULL도 포함
                       FROM EMP);
                    --WHERE DEPTNO IS NOT NULL 넣어주자
``` 

### 두개 테이블 비교 EXISTS/NOT EXISTS 에서는 꼭 JOIN 조건을 넣자
- 이렇게하면 EMP테이블에 값이 있는 경우 FALSE를 반환해서 값이 나오지 않는다
- EXISTS문법은 TRUE,FALSE만 반환하기에 성능이 좋다

```SQL
SELECT DEPTNO
       ,DNAME
       ,LOC
FROM DEPT A
WHERE NOT EXISTS (SELECT 1 -- A,B비교 안하고 EMP테이블에 값이 있는 경우 FALSE 리턴
                    FROM EMP B)
```

- 맞는 쿼리 : A,B를 비교해서 A에 없는 값이 나온 경우 TRUE반환하고 결과값으로 출력
```SQL
SELECT DEPTNO
       ,DNAME
       ,LOC
FROM DEPT A
WHERE NOT EXISTS (SELECT 1 -- A,B비교 후 A값과 일치하면 FALSE 다르면 TRUE 리턴
                    FROM EMP B
                   WHERE A.DEPTNO = B.DEPTNO);
```

### 중복행을 없애고 싶을때는 DISTINCT / GROUPBY를 사용하자
 - GROUP BY가 DISTINCT보다 더 높은 비용을 쓰지만, 집계함수에서 성능이 좋다
 - 데이터양이 많을때는 GROUP BY가 더 좋다

### Carteisan Product = Cross Join
 - 조인 조건이 없어 A,B의 모든 데이터가 나온다(A곱B)
```SQL
-- ORACLE
SELECT A.NAME, B.NAME
FROM EMP A, DEPT B

-- ANSI
SELECT A.NAME, B.NAME
FROM EMP
CROSS JOIN DEPT ;
```

### SUBQUERY보다는 JOIN, IN, EXISTS 사용하자
 - CUSTOMERS, ORDERS, WISHLIST 티에블을 이용, WISHLIST.DELETED = N인 고객의 주문합계(ORDER.ORDER_TOTLA의 SUM)을 구하라

 - 서브쿼리 방식 : 코스트 12
  1. ORDERS FULL SCAN
  2. CUSTOMERS도 FULL SCAN 해서 CUSTOMERS, ORDERS 머지조인 (자원 폭발)
  3. CUSTOMERS X ORDERS의 합으로 WISHLIST.DELETED = 'N' 해시조인
 ```SQL
 SELECT A.CUST_ID
       ,A.CUST_FNAME
       ,A.CUST_LNAME
       ,SUM(C.ORDER_TOTAL) AS ORDER_TOTAL
  FROM CUSTOMERS A, 
       (SELECT A.CUST_ID
          FROM CUSTOMERS A, WISHLIST B 
         WHERE A.CUST_ID = B.CUST_ID
           AND B.DELETED = 'N'
         GROUP BY A.CUST_ID) B, ORDERS C
 WHERE A.CUST_ID = B.CUST_ID  
   AND A.CUST_ID = C.CUST_ID -- 쓸데 없이 모든 A와 C를 비교한다
GROUP BY A.CUST_ID, A.CUST_FNAME, A.CUST_LNAME
ORDER BY CUST_ID;
 ```

- EXISTS 방식 : 코스트 11
 1. WISHLIST.DELETED = N 만 추출
 2. WISHLIST.DELETED = N 과 CUSTOMERS 해시조인(필요한 값만 먼저 추출)
 3. ORDERS와 머지조인
```SQL
SELECT A.CUST_ID
       ,A.CUST_FNAME
       ,A.CUST_LNAME
       ,SUM(B.ORDER_TOTAL) AS ORDER_TOTAL
  FROM CUSTOMERS A, ORDERS B
 WHERE A.CUST_ID = B.CUST_ID -- 2. A,B비교
   AND EXISTS (SELECT 1
                 FROM WISHLIST C
                WHERE A.CUST_ID = C.CUST_ID -- 1. 먼저 A의 범위를 줄여놓고
                  AND C.DELETED = 'N')
GROUP BY A.CUST_ID, A.CUST_FNAME, A.CUST_LNAME
ORDER BY CUST_ID;
```

### CARDINALITY와 COST고려
 - COST가 낮은게 더 효율적이지만
 - 데이터가 커지면 커질수록 CARDINALITY도 고려해야한다

 - CUSTOMERS, ORDERS, WISHLIST 테이블을 이용, WISHLIST.DELETED = N인 고객의 주문합계와 관심상품목록합계를 구하라

 1. COST고려방식(12/158)
 ```SQL
 SELECT A.CUST_ID
       ,A.CUST_FNAME
       ,A.CUST_LNAME
       ,SUM(B.ORDER_TOTAL) AS ORD_TOT -- A X B X 필터된 C의 SUM
       ,C.WISH_TOT
  FROM CUSTOMERS A, ORDERS B,
       (SELECT A.CUST_ID
              ,SUM(C.UNIT_PRICE*C.QUANTITY) AS WISH_TOT 
          FROM CUSTOMERS A, WISHLIST C
         WHERE A.CUST_ID = C.CUST_ID  
           AND C.DELETED = 'N'
         GROUP BY A.CUST_ID) C 
 WHERE A.CUST_ID = B.CUST_ID -- A X B
   AND A.CUST_ID = C.CUST_ID -- A X B X 필터된 C
GROUP BY A.CUST_ID, A.CUST_FNAME, A.CUST_LNAME,C.WISH_TOT
ORDER BY CUST_ID;
```

2. CARDINALITY 고려방식(13/47)
```SQL
SELECT A.CUST_ID
       ,A.CUST_FNAME
       ,A.CUST_LNAME
       ,B.ORD_TOT
       ,C.WISH_TOT
  FROM CUSTOMERS A, 
       (SELECT CUST_ID,
               SUM(ORDER_TOTAL) AS ORD_TOT -- ORDER_TOT이 크면 클수록 느려짐
          FROM ORDERS
         GROUP BY CUST_ID) B,
       (SELECT CUST_ID
              ,SUM(UNIT_PRICE*QUANTITY) AS WISH_TOT
          FROM WISHLIST 
         WHERE DELETED = 'N'
         GROUP BY CUST_ID) C 
 WHERE A.CUST_ID = B.CUST_ID
   AND A.CUST_ID = C.CUST_ID
ORDER BY CUST_ID;
```

### UNION ALL에서 서로 컬럼이 다를때
 - 없는 컬럼은 NULL AS 컬럼명으로 맞춰준다
 - 중복된 컬럼의 숫자는 SUM으로 해준다

 ```SQL
 SELECT ITEM, DATE SUM(QTY), SUM(RESULT)
 FROM(
  SELECT ITEM, DATE, QTY, NULL AS RESULT
  UNION ALL
  SELECT ITEM, DATE, NULL, RESULT)
 ```