## 집계함수

### 집계함수 종류
 - ROLLUP : GROUP BY와 함께 사용, 선택한 열에 대한 계층적인 집계 계산
 ```SQL
SELECT Year, Month, Day, SUM(Sales) AS Total_Sales
FROM Sales
GROUP BY ROLLUP(Year, Month, Day)
ORDER BY 1,2,3; --1 : YEAR, 2: MONTH, 3.DAY

-- 결과
-- 년,월,일 합계
-- 년,월 합계
-- 년 합계
------------------------------
| Year  | Month | Day  | Total_Sales |
------------------------------
| 2020  | 01    | 01   | 100         |
| 2020  | 01    | 02   | 150         |
| 2020  | 01    | NULL | 250         |
| 2020  | 02    | 01   | 200         |
| 2020  | 02    | 02   | 250         |
| 2020  | 02    | NULL | 450         |
| 2020  | NULL  | NULL | 700         |
| 2021  | 01    | 01   | 300         |
| 2021  | 01    | 02   | 350         |
| 2021  | 01    | NULL | 650         |
| 2021  | 02    | 01   | 400         |
| 2021  | 02    | 02   | 450         |
| 2021  | 02    | NULL | 850         |
| 2021  | NULL  | NULL | 1500        |
| NULL  | NULL  | NULL | 2200        |
 ```

- 사용법
 1. 모든 컬럼 사용
 ```SQL
 -- JOB / DEPTNO / DEPTNO, JOB별로 집계
 SELECT DEPTNO
       ,JOB
       ,SUM(SAL)  
  FROM EMP
 GROUP BY ROLLUP(DEPTNO, JOB);

 --결과
DEPTNO	JOB	SUM(SAL)
10	CLERK	1300
10	MANAGER	2450
10	PRESIDENT	5000
10	NULL	8750
20	CLERK	1900
20	ANALYST	6000
20	MANAGER	2975
20	NULL	10875
30	CLERK	950
30	MANAGER	2850
30	SALESMAN	5600
30	NULL	9400
NULL	NULL	29025

 ```

 2. 일부 컬럼 사용
 ```SQL
 -- DEPTNO로 묶은것을 job별로 따로 집계
 SELECT DEPTNO
       ,JOB
       ,SUM(SAL)  
  FROM EMP
 GROUP BY DEPTNO, ROLLUP(JOB);

--결과 
DEPTNO	JOB	SUM(SAL)
10	CLERK	1300
10	MANAGER	2450
10	PRESIDENT	5000
10	NULL	8750
20	CLERK	1900
20	ANALYST	6000
20	MANAGER	2975
20	NULL	10875
30	CLERK	950
30	MANAGER	2850
30	SALESMAN	5600
30	NULL	9400
 ```

 3. 여러 컬럼을 하나로 묶기
 ```SQL
  SELECT DEPTNO
        ,EMPNO
        ,ENAME
        ,SUM(SAL) AS SAL
   FROM EMP
  GROUP BY DEPTNO, ROLLUP((EMPNO, ENAME))
  ORDER BY DEPTNO, EMPNO

 -- 결과 
10	7782	CLARK	2450
10	7839	KING	5000
10	7934	MILLER	1300
10	NULL	NULL  8750
20	7369	SMITH	800
20	7566	JONES	2975
20	7788	SCOTT	3000
20	7876	ADAMS	1100
20	7902	FORD	3000
20	NULL	NULL	10875
30	7499	ALLEN	1600
30	7521	WARD	1250
30	7654	MARTIN	1250
30	7698	BLAKE	2850
30	7844	TURNER	1500
30	7900	JAMES	950
30	NULL	NULL	9400 
 ```

 - CUBE : ROLLUP과 유사하지만 열의 모든 경우의 수 조합에 대해 집계

 ```SQL
 SELECT Year, Month, Region, SUM(Sales) as TotalSales
FROM Sales
GROUP BY CUBE(Year, Month, Region)
ORDER BY 3; -- 1:YEAR, 2:MONTH, 3:REGION

-- 지역
-- 월
-- 년
-- 지역,월
-- 지역, 년
-- 월, 년
+------+-------+--------+------------+
| Year | Month | Region | TotalSales |
+------+-------+--------+------------+
| 2021 | 1     | North  | 100        |
| 2021 | 1     | South  | 150        |
| 2021 | 1     | NULL   | 250        |
| 2021 | 2     | North  | 200        |
| 2021 | 2     | South  | 250        |
| 2021 | 2     | NULL   | 450        |
| NULL | 1     | North  | 100        |
| NULL | 1     | South  | 150        |
| NULL | 1     | NULL   | 250        |
| NULL | 2     | North  | 200        |
| NULL | 2     | South  | 250        |
| NULL | 2     | NULL   | 450        |
| 2021 | NULL  | North  | 300        |
| 2021 | NULL 
 ```

 - GROUPING SETS : 필요한 조합만 쓰고 싶을때 사용
  - 전체 계산 후 가져오는것이기에 I/O 과다사용 가능성있음
 ```SQL
 SELECT DEPTNO, JOB, MGR, SUM(SAL)
   FROM EMP
  GROUP BY GROUPING SETS((DEPTNO,JOB), (DEPTNO, MGR))   
 ```

 - GROUPING : 해당 컬럼이 그룹핑에 참여했는지 안했는지 판단(0:참여, 1: 미참여)
  - 기존 컬럼 값이 NULL이였는지 판단(NULL인데 0이면 기존 컬럼이 NULL)
 ```SQL
 SELECT DEPTNO, GROUPING(DEPTNO), JOB, GROUPING(JOB), SUM(SAL)
   FROM EMP
  GROUP BY ROLLUP(DEPTNO, JOB)
  ORDER BY DEPTNO, JOB;

 -- 결과 
 DEPT참여여부         JOB참여여부
10	 0	     CLERK	     0	1300
10	 0	     MANAGER	   0	2450
10	 0	     PRESIDENT   0	5000
10	 0	     NULL        1	8750
20	 0	     ANALYST	   0	6000
20	 0	     CLERK	     0	1900
20	 0	     MANAGER     0	2975
20	 0	     NULL        1	10875
30	 0	     CLERK	     0	950
30	 0	     MANAGER	   0	2850
30	 0	     SALESMAN	   0 	5600
30	 0	     NULL        1	9400
NULL 1	     NULL        1	29025
 ```

 - GROUPING_ID : 하나이상 각 칼럼의 참여여부 판단(컬럼 오른쪽 순서대로 2의 0,1,2...승)
  - 기존 컬럼 값이 NULL이였는지 판단(NULL인데 0이면 기존 컬럼이 NULL)
 ```SQL
 -- JOB이 NULL이면 2의 0승 = 1
 -- DEPTNO NULL이면 2의 1승 = 2
 -- 둘다 NULL 이면 1+2 = 3
 SELECT DEPTNO, GROUPING_ID(DEPTNO, JOB)
   FROM EMP
  GROUP BY ROLLUP(DEPTNO,JOB) 
 ```

 ### 집계함수에서 집계 컬럼을 추가하고 싶을때
  - 숫자를 활용한다(SELECT 1 FROM...)

  - 부서별 급여합계와 평균(EMPNO, ENAME)
  - 전사 급여합계와 평균(DEPTNO)
 ```SQL
 SELECT DEPTNO
        ,EMPNO
        ,DECODE(GROUPING_ID(1, DEPTNO, 2, EMPNO, ENAME), -- GROUPING값 세분화를 위해
            3, 'DEPT_SUM',
            7, 'DEPT_AVG',
            15, 'TOTAL_SUM',
            31, 'TOTAL_AVG',
            ENAME) AS ENAME
        ,DECODE(GROUPING_ID(1, DEPTNO, 2, EMPNO, ENAME),
            3, SUM(SAL),
            7, ROUND(AVG(SAL),1),
            15, SUM(SAL),
            31, ROUND(AVG(SAL),1),
            SUM(SAL)) AS SAL
   FROM EMP
  GROUP BY ROLLUP(1,DEPTNO,2,(EMPNO, ENAME)) --NULL로 된 컬럼 부서별, 전사 추가하기 위해 1,2추가
  ORDER BY DEPTNO, EMPNO  
 ```

### PIVOT
 - 데이터의 행과 열을 바꿔주는 함수
 ```SQL
 SELECT *
  FROM (SELECT deptno
               ,job
               ,sal 
          FROM emp ) 
 PIVOT (SUM(sal) FOR job IN ('ANALYST'   AS analyst, --DEPTNO로 나누어진 JOB끼리 금액을 합쳐서 IN문에 있는 컬럼으로 변경
                             'CLERK'     AS clerk,
                             'MANAGER'   AS manager, 
                             'PRESIDENT' AS president, 
                             'SALESMAN'  AS salesman)) 
 ORDER BY deptno ;

 --기존
20	ANALYST	3000
20	MANAGER	2975
20	CLERK	800
20	CLERK	1100
30	SALESMAN	1600
30	SALESMAN	1250
30	SALESMAN	1250
30	MANAGER	2850
10	MANAGER	2450
30	SALESMAN	1500
30	CLERK	950
20	ANALYST	3000
10	CLERK	1300
10	PRESIDENT	5000

--변경
DEPTNO ANALYST  CLERK...
10	   NULL	    1300	2450	5000	NULL
20	   6000	    1900	2975	NULL	NULL
30	   NULL	    950  	2850	NULL	5600
 ```
 


 