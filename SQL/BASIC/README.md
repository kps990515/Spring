## BASIC

### AND OR 우선순위
- OR은 무조건 가로로 묶어주자

1. 사원테이블에서 COMM이 NULL 이거나 0이면서, SAL이 2000이하인 사원 검색
```
AND OR 우선순위에 따라 AND가 우선적으로 처리됨
그래서 OR조건을 가로로 묶어줘야함 
```
``` SQL
select * 
  from emp
 where (comm IS NULL OR COMM = 0)
   AND sal < 2000;
```
```SQL
SELECT EMPNO,
       ENAME,
       SAL,
       DEPTNO
  FROM EMP
 WHERE DEPTNO IN (10,30)
   AND SAL > 2000;
```

### CASE WHEN 데이터타입 변환
- 숫자형, 문자형으로 되어있는 컬럼에 CASE WHEN 으로 다른데이터타입 넣을때는 데이터타입 통일화시켜주자

``` 
MGR은 숫자 타입
하지만 NO MANAGER로 값을 넣으면서 문자형으로 변환
MGR도 문자타입으로 변경 필요
```
2. EMP테이블에서 MGR이 NULL이면 MANAGER컬럼에 'NO MANAGER' 아니면 MGR 리턴
```SQL
select EMPNO,
       ENAME,
       JOB,
       CASE WHEN MGR IS NULL THEN 'NO MANAGER' --MGR이 NULL일대는 NO MANAGER
       ELSE TO_CHAR(MGR) -- 아닐때는 MGR을 표기 대신 숫자형 -> 문자형 변환해야함
       END AS MANAGER -- 컬럼이름은 MANAGER
from emp;
```

### DEOCDE의 암시적 형변화 주의
- DECODE는 첫번쨰 반환 값 데이터타입으로 암시적 형변환 발생
- NULL이 첫 반환 값일 경우 문자타입으로 변환
- 그 뒤에 SAL이 숫자일 경우 문자값으로 변경되어 숫자비교에서 문제 발생

```SQL
- nvl은 명시적 변환
SELECT empno, ename, job, NVL(TO_CHAR(mgr), 'NO Manager') AS MANAGER
  FROM emp ; 

- case는 명시적 변환
SELECT empno, ename, job, CASE WHEN mgr IS NULL THEN 'NO Manager' 
                          ELSE TO_CHAR(mgr) END AS MANAGER
  FROM emp ;

- decode는 암시적 변환
SELECT empno, ename, job, DECODE(mgr, NULL, 'NO Manager', mgr) AS MANAGER
FROM emp ;
```
3. SELECT MAX(DECODE(JOB, 'PRESIDENT', NULL, SAL)) AS MAX_SAL이 950이 나오는 이유를 확인
```SQL
SELECT MAX(DECODE(JOB, 'PRESIDENT', TO_NUMBER(NULL), SAL)) AS MAX_SAL --SAL이 숫자이기에 NULL로 숫자 변환 필요
FROM EMP;
```

#### NVL2(A,B,C)는 A가 NULL이면 C NULL이 아니면 B
``` SQL
SELECT last_name, salary, commission_pct,
NVL2(commission_pct, 'SAL+COMM', 'SAL') income
FROM employees WHERE department_id IN (50, 80);
```

#### nullif(A,B) A=B이면 null, 아니면 A
```SQL
SELECT first_name, LENGTH(first_name) "expr1 result",
last_name, LENGTH(last_name) "expr2",
NULLIF(LENGTH(first_name), LENGTH(last_name)) result
FROM employees;
```

### LIKE || 와 LIKE OR은 다르다
4. EMP테이블에서 ENAME이 S또는 A로 시작하는 사원을 검색
 - LIKE %S || %A 는 문자결합으로 A와 S로 시작하는 조건을 모두 만족해야만 나옴(잘못된 구문)
 - LIKE %S OR LIKE %A는 A나 S로 시작하는 조건을 조회

```SQL
 SELECT EMPNO,
       ENAME,
       SAL,
       DEPTNO
FROM EMP
WHERE ENAME LIKE 'S%'  
OR ENAME LIKE 'A%';
```

### 날짜형식은 시분초까지 저장된다
5. EMP테이블에서 HIREDATE가 1980/12/17인걸 찾아라

- 시분초 무시(틀린 답)
```SQL
SELECT EMPNO,
       ENAME,
       HIREDATE,
       DEPTNO
FROM EMP
WHERE HIREDATE = TO_DATE('1980/12/17','YYYY/MM/DD'); -- 시분초 고려가 안됨
```

- 왼쪽칼럼을 수정해버리면 INDEX가 작동 안함
```SQL
SELECT EMPNO,
       ENAME,
       HIREDATE,
       DEPTNO
FROM EMP
WHERE TO_CHAR(HIREDATE) = '1980/12/17';
```

```SQL
SELECT EMPNO,
       ENAME,
       HIREDATE,
       DEPTNO
FROM EMP
WHERE HIREDATE = BETWEEN TO_DATE('1980/12/17','YYYY/MM/DD');
                     AND TO_DATE('1980/12/18','YYYY/MM/DD') 
```

#### 날짜 함수
- TRUNC 절삭
```SQL
SELECT SYSDATE                        --2023/04/17
     ,TRUNC(SYSDATE, 'YYYY') AS YYYY -- 2023/01/01
     ,TRUNC(SYSDATE,'MM')    AS MM   -- 2023/04/01
 FROM dual ;
```

- ROUND 반올림
```SQL
select ROUND(SYSDATE, 'YYYY') -- 2023/01/01
      ,ROUND(SYSDATE,'MM')    -- 2023/05/01
from dual;
```

- 다음 요일 구하기(NEXT_DAY)
```SQL
select NEXT_DAY ('2004/03/20','금') from dual;
```

- 날짜 더하기
```SQL
SELECT '2023-04-17' + 3 AS result
FROM dual;
```

### 유효한 날짜 확인하기

- VALIDATE_CONVERSION(12C R2부터) 함수
- 0이면 맞는 값, 1이면 틀린 값
- TO_DATE: 날짜문자열을 실제 날짜 데이터타입으로 변환
- AS DATE : 쿼리결과를 특정 날짜 데이터타입으로 명식적으로 변환할때

```SQL
SELECT EMPNO,
       ENAME,
       HIREDATE
FROM EMPDT
WHERE VALIDATE_CONVERSION(HIREDATE AS DATE, 'YYYYMMDD') = 0;
```

- PL/SQL
```SQL
CREATE OR REPLACE FUNCTION CK_DATE (P_VAL VARCHAR2)
    RETURN VARCHAR2 IS
        V_CK DATE;
    BEGIN
        V_CK := TO_DATE(P_VAL, 'YYYYMMDD'); 
    RETURN 'VALID' --에러 안나면 VALID 리턴
    EXCEPTION 
        WHEN OTHERS THEN
            RETURN 'INVALID' -- EXCEPTION나면 INVALID
END;

SELECT EMPNO,
       ENAME,
       HIREDATE,
       CK_DATE(HIREDATE) AS CK_DATE
FROM EMPDT
WHERE CK_DATE(HIREDATE) = 'INVALID'
```

- 일반방법
```SQL
-- 1980/01/01 부터 100까지 월을 더해서 날짜 테이블 생성
SELECT empno, ename, hiredate 
  FROM empdt a
 WHERE NOT EXISTS (SELECT * 
                     FROM (SELECT TO_CHAR(dt,'YYYYMMDD')           AS START_DT, 
                                  TO_CHAR(last_day(dt),'YYYYMMDD') AS END_DT
                             FROM (SELECT ADD_MONTHS(TO_DATE('1980/01/01','YYYY/MM/DD')
                                                    ,level - 1) AS dt
                                     FROM dual
                                    CONNECT BY level <= 100)
                           )
                      WHERE a.hiredate BETWEEN start_dt AND end_dt) ;

-- WITH 절 사용 + 위에 테이블에 없는 날짜 구하기
WITH ck_dt AS (SELECT TO_CHAR(dt,'YYYYMMDD')           AS START_DT, 
                      TO_CHAR(last_day(dt),'YYYYMMDD') AS END_DT
                 FROM (SELECT ADD_MONTHS(TO_DATE('1980/01/01','YYYY/MM/DD'),level - 1) AS dt
                         FROM dual
                       CONNECT BY level <= 100))
SELECT empno, ename, hiredate 
  FROM empdt a
 WHERE NOT EXISTS (SELECT * 
                     FROM ck_dt 
                    WHERE a.hiredate BETWEEN start_dt AND end_dt) ;
```

### 순서정렬 / NULL은 따로처리
```SQL
SELECT EMPNO,
       ENAME,
       SAL,
       COMM
FROM EMP
ORDER BY NVL(COMM,-1) DESC;

SELECT EMPNO,
       ENAME,
       SAL,
       COMM
FROM EMP
ORDER BY COMM DESC NULLS LAST;
```


### 기타 날짜관련 함수
- CURRENT_DATE : 서버에 접속한 클라이언트의 시간 반환(세션)
```SQL
-- 연도내의 일
select to_char( sysdate, 'ddd') from dual; --137

-- 월내의 일
select to_char( sysdate, 'dd') from dual; -- 17

-- 주내의 일 =>  1:일 2:월 3:화 4:수 5:목 6:금 7:토
select to_char( sysdate, 'd') from dual; --2

-- 한 주의 시작일, 일요일
select trunc( sysdate, 'day') from dual; --2023/04/16

-- 올해의 시작일
select trunc( sysdate, 'yyyy') from dual; -- 2023/01/01 
```











