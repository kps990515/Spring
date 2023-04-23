## INDEX
 - INDEX는 KEY값과 ROWID(위치정보)를 가지고 있다
 - INDEX 사용하지않으면 OPTION에 STORAGE FULL이라고 나온다

### INDEX를 사용하지 못하는 경우
 1. WHERE절을 사용안하는 경우

 2. WHERE 좌측 컬럼이 변형된 경우
 ```SQL
 WHERE SUM(SAL) = 1000
 ```

 3. IS NULL 비교
  - INDEX에는 NULL이 저장되지 않는다
 ```SQL
 WHERE SAL IS NULL
 ```

 4. WHERE 좌측 컬럼의 암시적 형 변환
  - 숫자를 문자로 암시적 형 변환됨
 ```SQL
 WHERE SAL LIKE '10'
 ```

### INDEX FULL SCAN 하는 경우
 1. LIKE 조건 (앞에 있는 %,_)
  - 앞글자를 모르면 INDEX KEY값을 알 수 없다
 ```SQL
 -- FULL SCAN
 WHERE ENAME LIKE '%S'
 
 -- 이건 괜찮음
 WHERE ENAME LIKE 'S%'
 ```

 2. IS NOT NULL
 ```SQL
 WHERE ENAME IS NOT NULL
 ```

 3. 부정형 비교
 ```SQL
 WHERE DEPTNO != 30
 ```
  