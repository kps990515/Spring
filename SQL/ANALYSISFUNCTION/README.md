## 분석함수

### OVER, PARTITION BY
 - OVER : 윈도우함수로, 데이터 행 단위로 계산을 하기 위한 함수
 - PARTITION BY : 내부 그룹화를 시키는 함수
 - SUM(A) OVER(PARTITION BY B)
     : A를 행단위로 더할건데 B로 그룹화 해서 알려줘

```SQL
-- SAL의 합계를 어떤것으로 기준으로 할지가 없어서 에러
SELECT empno, ename, sal, deptno, SUM(sal)
  FROM emp; 

-- 각 개인을 기준으로 SAL의 합계를 구한다
SELECT empno, ename, sal, deptno, SUM(sal)
  FROM emp
 GROUP BY empno, ename, sal, deptno; 

-- 모든 사람의 SAL 합계 계산
SELECT empno, ename, sal, deptno, SUM(sal) OVER() AS TOTAL
  FROM emp; 

-- 모든 사람의 SAL 합계 + DEPTNO별 SAL 합계
SELECT empno, ename, sal, deptno
      ,SUM(sal) OVER()                    AS sum_overall
      ,SUM(sal) OVER(PARTITION BY deptno) AS sum_deptno
  FROM emp ;   

-- RANK를 행단위로 계산할건데 DEPTNO로 그룹화하고 SAL로 순서배겨서
-- DENSE_RANK ..동일
-- ROW_NUMBER.. 동일
SELECT empno, ename, sal, deptno
      ,RANK( )       OVER ( PARTITION BY deptno ORDER BY sal DESC ) AS RANK
      ,DENSE_RANK( ) OVER ( PARTITION BY deptno ORDER BY sal DESC ) AS DRANK
      ,ROW_NUMBER( ) OVER ( PARTITION BY deptno ORDER BY sal DESC ) AS RNUM
  FROM emp ; 
```

### WINDOWING
 - ROWS/RANGE 이용한 그룹화
 - ROWS : 물리적 
   1. UNBOUNDED PRECEDING : 첫번째 행
   2. UNBOUNDED FOLLOWING : 마지막 행
   3. CURRENT ROW : 현재 행

```SQL
SELECT empno, ename, sal, deptno
      ,SUM(sal) OVER(ORDER BY empno)                                       AS SUM1 
      -- EMPNO 순서대로 행 합계 계산(800, 1600, 2000...)
      ,SUM(sal) OVER(ORDER BY empno ROWS UNBOUNDED PRECEDING)              AS SUM2 
      -- 첫행부터 마지막행까지 EMPNO 순서대로 .. 동일..
      ,SUM(sal) OVER(ORDER BY empno ROWS BETWEEN UNBOUNDED PRECEDING 
                                             AND CURRENT ROW)              AS SUM3 
                                             -- 첫행부터 나까지 합계 계산
      ,SUM(sal) OVER(ORDER BY empno ROWS BETWEEN CURRENT ROW
                                             AND UNBOUNDED FOLLOWING)      AS SUM4 
                                             -- 나부터 마지막행까지 합계 계산
      ,SUM(sal) OVER(ORDER BY empno ROWS BETWEEN 1 PRECEDING 
                                             AND 1 FOLLOWING)              AS SUM5 
                                             -- 나 + 전/후 행 합계 계산
  FROM emp ; 
```
 
 - RANGE : 논리적(DATA에 따라 범위 달라짐)
   - NUMTOYMINTERVAL : 숫자형 데이터를 시간으로 변환하는 함수
     - NUMTOYMINTERVAL(12, 'MONTH')) = 12개월

 ```SQL
 SELECT empno, ename, mgr, sal
      ,hiredate - NUMTOYMINTERVAL(1,'YEAR') AS "1 years ago" -- 1년전
      ,hiredate
      ,hiredate + NUMTOYMINTERVAL(1,'YEAR') AS "1 years later" -- 1년후
      ,SUM(sal) OVER(ORDER BY hiredate RANGE NUMTOYMINTERVAL(1,'YEAR') PRECEDING)       AS SUM1
       -- 자신의 행보다 1년전까지의 SAL 합계 구하기
      ,SUM(sal) OVER(ORDER BY hiredate RANGE BETWEEN NUMTOYMINTERVAL(1,'YEAR') PRECEDING
                                               AND NUMTOYMINTERVAL(1,'YEAR') FOLLOWING) AS SUM2
       -- 자신의 행보다 1년 전 1년 후 까지 SAL 합계 구하기                                        
  FROM emp ;
 ``` 

### LAG / LEAD
 - LAG : 현재행과 이전행을 비교하는데 사용
 - LEAD : 현재행과 다음행을 비교하는데 사용

```SQL
SELECT empno, ename, hiredate,
       LAG(SAL,2,0)  OVER (ORDER BY hiredate,empno) AS PREV_ENAME, -- 현재 행보다 2번째 전
       LEAD(SAL,2,0) OVER (ORDER BY hiredate,empno) AS NEXT_ENAME  -- 현재 행보다 2번재 후
  FROM emp ;
```

### LISTAGG
 - 특정 열의 값을 그룹화하여 문자열로 결합
``` SQL
SELECT deptno,
       LISTAGG(ename,',') WITHIN GROUP (ORDER BY ename) OVER(PARTITION BY DEPTNO) AS employee
       -- DEPTNO로 그룹화 시킨다음 ENAME을 그룹화해서 ','로 결합해라
  FROM emp;

SELECT deptno,
       LISTAGG(ename,',' ON OVERFLOW TRUNCATE '...' WITH COUNT) WITHIN GROUP (ORDER BY ename) 
       -- ENAME그룹화가 너무 길어서 OVERFLOW가 발생할 경우 처리법
       AS employee
  FROM emp
 GROUP BY deptno ;
```

### RATIO_TO_REPORT
 - 백분율 구하기
```SQL
 SELECT deptno, ename, sal, 
       SUM(sal) OVER(PARTITION BY deptno ORDER BY sal, ename)       AS cum_sal,
       ROUND(100*RATIO_TO_REPORT(sal) OVER(PARTITION BY deptno),1)  AS pct_dept,
       ROUND(100*RATIO_TO_REPORT(sal) OVER(),1)                     AS pct_overall
 FROM emp ;
```
