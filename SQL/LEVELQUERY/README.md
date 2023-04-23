## 계층질의

### 계층질의
 - CONNECT BY : 특정 조건으로 연결해서 트리형태로 생성
 - START WITH : 최상위 루트(이게 없다면 모든 값이 루트로 가능 -> 각 행에 대한 모든 하위계층이 다 트리로 나옴)
 - LPAD : 왼쪽에 특정 값 넣기

 - MGR를 따라 상위 LEVEL과 하위 LEVEL를 계층순으로 계산해라
```SQL
SELECT LPAD(' ',LEVEL*2-2)||ename AS NAME, LEVEL, empno, mgr
  -- 왼쪽에 ' '를 LEVE*2-2만큼 추가
  FROM emp
 START WITH empno = 7839 --최상위 행(루트)
 CONNECT BY prior empno = mgr ; -- EMPNO가 MGR에 있으면 상위부여(자신의 사원번호가 다른사람의 매니저)

-- 결과
KING	1	7839	
  JONES	2	7566	7839
    SCOTT	3	7788	7566
      ADAMS	4	7876	7788
    FORD	3	7902	7566
      SMITH	4	7369	7902
  BLAKE	2	7698	7839
    ALLEN	3	7499	7698
    WARD	3	7521	7698
    MARTIN	3	7654	7698
    TURNER	3	7844	7698
    JAMES	3	7900	7698
  CLARK	2	7782	7839
    MILLER	3	7934	7782 
```

### 특정 트리노드 ~ 하위 노드를 삭제
 - 틀린방법
 ```SQL
 SELECT LPAD(' ',LEVEL*2-2)||ename AS NAME, LEVEL, empno, mgr
  FROM emp
 START WITH empno = 7839 
 CONNECT BY prior empno = mgr 
 WHERE ENAME != 'SCOTT' -- 하위노드는 살아있고 SCOTT만 빠짐
 ```

 - 맞는방법 : CONNECT BY에서 조건 추가
 ```SQL
 SELECT LPAD(' ',LEVEL*2-2)||ename AS NAME, LEVEL, empno, mgr
  FROM emp
 START WITH empno = 7839 
 CONNECT BY prior empno = mgr 
   AND ENAME != 'SCOTT'
 ```

 ### 상위노드 컬럼을 가져오는법
  - 찾고싶은 컬럼에 PRIOR 붙이기

 ```SQL
 SELECT LPAD(' ',LEVEL*2-2)||ename AS NAME, 
        ,LEVEL
        ,empno
        ,mgr
        ,PRIOR ENAME AS MANAGER -- 상위노드의 ENAME 출력
  FROM emp
 START WITH empno = 7839 
 CONNECT BY prior empno = mgr ;
 ```

### 최상위 루트 컬럼을 가져오는법
 - 찾고싶은 컬럼에 CONNECT_BY_ROOT 붙이기

 ```SQL
 SELECT LPAD(' ',LEVEL*2-2)||ename AS NAME, 
        ,LEVEL
        ,empno
        ,mgr
        ,CONNECT_BY_ROOT ENAME AS MANAGER -- 최상위 루트의 ENAME 출력
  FROM emp
 START WITH empno = 7839 
 CONNECT BY prior empno = mgr ;
 ```

### 계층구조를 유지하면서 순서정렬하는법
 - ORDER SIBLING BY 컬럼 사용

 ```SQL
 SELECT LPAD(' ',LEVEL*2-2)||ename AS NAME, 
        ,LEVEL
        ,empno
        ,mgr
  FROM emp
 START WITH empno = 7839 
 CONNECT BY prior empno = mgr 
 ORDER SIBLINGS BY ENAME;
 ``` 

### 계층구조를 하나의 연결된 값으로 만드는법
 - SYS_CONNECT_BY_PATH(컬럼, 문자열)

 ```SQL
 SELECT LPAD(' ',LEVEL*2-2)||ename AS NAME, 
        ,SYS_CONNECT_BY_PATH(ENAME, '/')
  FROM emp
 START WITH empno = 7839 
 CONNECT BY prior empno = mgr ;

 --
 KING	/KING
  JONES	/KING/JONES
    SCOTT	/KING/JONES/SCOTT
      ADAMS	/KING/JONES/SCOTT/ADAMS
 ``` 

### 리프노드 확인법
 - CONNECT_BY_ISLEAF : 리프노드면 1, 아니면 0

 ```SQL
  SELECT LPAD(' ',LEVEL*2-2)||ename AS NAME
         ,LEVEL
         ,empno
         ,mgr
         ,CONNECT_BY_ISLEAF
  FROM emp
  START WITH empno = 7839 
  CONNECT BY prior empno = mgr ;
 ```

### 계층이 맞는지 확인하는법(순환루프가 아닌지) 
 - CONNECT_BY_ISCYCLE : 순환루프이면 1 아니면 0
 - CONNECY BY NOCYCLE : 순환루프인것을 제거(위의 1값인 행~하위행 제거)

 ```SQL
  SELECT LPAD(' ',LEVEL*2-2)||ename AS NAME
         ,LEVEL
         ,empno
         ,mgr
         ,COONECT_BY_ISCYCLE
  FROM emp
  START WITH empno = 7839 
  CONNECT BY NO CYCLE prior empno = mgr ;
 ```

 
