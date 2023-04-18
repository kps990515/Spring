## INLINE VIEW 와 WITH절
 - WITH절이 한번만 실행되면 INLINE VIEW 형식으로 실행
 - 두번 이상 실행되면 임시 테이블생성 후 실행
 - 플랜에서 SYS_TEMP...이렇게 나오면 테이블형식으로 사용됨
 - WITH문 컬럼을 접근할때 마다 물리적 I/O 증가
 
### 기본
 - 소속부서 평균급여보다 높은 금액받는 사람 구하기

 ```SQL
 SELECT A.EMPNO
       ,A.ENAME
       ,A.JOB
       ,A.SAL
       ,A.DEPTNO
       ,B.AVG_SAL     
  FROM EMP A,
       (SELECT DEPTNO,
               ROUND(AVG(SAL)) AS AVG_SAL
          FROM EMP 
        GROUP BY DEPTNO
        )B
WHERE A.DEPTNO = B.DEPTNO
  AND A.SAL > B.AVG_SAL 
```

```SQL
WITH AVG_DEPTNO_SAL AS(
 SELECT DEPTNO,
        ROUND(AVG(SAL)) AS AVG_SAL
   FROM EMP 
 GROUP BY DEPTNO   
)
SELECT A.EMPNO
       ,A.ENAME
       ,A.JOB
       ,A.SAL
       ,A.DEPTNO
       ,B.AVG_SAL     
  FROM EMP A, AVG_DEPTNO_SAL B
 WHERE A.DEPTNO = B.DEPTNO
   AND A.SAL > B.AVG_SAL;  
```

### 가장 큰 값 구하기
 - FETCH FIRST, LAST ONLY: ORDER BY 절과 함께 사용되며, 정렬된 결과에서 상위 또는 하위 n개의 행을 선택하여 반환
 - FETCH FIRST 2 ROWS WITH TIES : 두번째행까지 가져오고 두번째행과 같은 값을 가진 행도 반환
 - ROWNUM + ORDERBY로하면 ROWNUM이 먼저작동하여 ORDER와 상관없이 첫 N개가 반환

 - 이렇게 하면 순서상관없이 테이블 첫 N개만 나옴
 ```SQL
 SELECT * 
   FROM EMP
  WHERE ROWNUM <=3 -- 이게 먼저 작동
 ORDER BY SAL DESC;
 ``` 

 - 맞는 답
 ```SQL
 SELECT *    
  FROM (SELECT *
          FROM EMP
        ORDER BY SAL DESC) -- 순서대로 정렬하고
  WHERE ROWNUM <4; -- 뽑기
 ```

 - FETCH 활용
 ```SQL
 SELECT *    
   FROM EMP
 ORDER BY SAL DESC
 FETCH FIRST 3 ROWS ONLY; 
 ```

#### ROWNUM은 꼭 1이 필요함
```SQL
SELECT ROWNUM, EMPNO, ENAME, SAL
FROM EMP
WHERE ROWNUM > 1; -- 값이 안나옴
```

#### ROWNUM을 활용한 테이블 레이아웃 복사
```SQL
create table emp_test02
as
SELECT *
  FROM emp 
 WHERE rownum = 2; -- 값이 없어서 레이아웃만 가져옴
 
 select * from emp_test02;
```

#### RANK, DENSE RANK, ROWNUMBER 함수
 - RANK : 특정 컬럼 값으로 순위를 매기는 함수(중복 순위가 있다면 해당 숫자 뛰어넘음 1,1,3,4)
 ```SQL
 RANK() OVER (ORDER BY [컬럼] [ASC|DESC])
 RANK() OVER (ORDER BY score ASC) AS rank
 RANK() OVER (PARTITION BY DEPTNO ORDER BY score ASC) AS rank
 ```
 
 - DENSE RANK : 숫자를 뛰어넘지않고 모든 숫자 부여(1,1,2,3,3,4,4,4)
 ```SQL
 DENSE_RANK() OVER (ORDER BY [컬럼] [ASC|DESC])
 DENSE_RANK() OVER (ORDER BY score DESC) AS dense_rank
 DENSE_RANK() OVER (PARTITION BY DEPTNO ORDER BY score DESC) AS dense_rank
 ```

 - ROW_NUMBER : 번호 부여
  ```SQL
 ROW_NUMBER() OVER (ORDER BY [컬럼] [ASC|DESC])
 ROW_NUMBER() OVER (ORDER BY score DESC) AS dense_rank
 ROW_NUMBER() OVER (PARTITION BY DEPTNO ORDER BY score DESC) AS dense_rank
 ```

#### OFFSET
 - 몇칸 건너뛰고 가져올지 선택하는 명령어
 ```SQL
 SELECT [컬럼1], [컬럼2], ... 
 FROM [테이블] 
 WHERE 조건식 
 ORDER BY 컬럼 [ASC|DESC] 
 OFFSET N ROWS FETCH FIRST 2 ROWS ONLY;
 ``` 









