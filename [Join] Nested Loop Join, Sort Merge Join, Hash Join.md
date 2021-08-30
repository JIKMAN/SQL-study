> 데이터 처리

MYSQL 기준, SQL을 병렬 처리하지 않는다. 3rd Party 스토리지 엔진을 설치하여 병렬처리를 할 수 있지만, 기본적인 스토리지 엔진은 **단일 코어**로만 데이터를 처리한다. 따라서 Mysql 입장에서는 CPU 코어 개수를 늘리는 Scale-Out보다는 단위 처리량이 좋은 CPU로 **Scale-Up**하는 것이 훨씬 좋다



> Nested Loop Join

○ 선행 테이블 A의 조건 검색 결과값 하나하나를 테이블 B와 비교하여 조인하는 방식

○ 이중 for문과 흡사한 구조이다.

○ 결국 처리할 데이터가 적으면 빠르지만, 테이블 A,B 중 하나라도 연산할 데이터가 많으면 쿼리 효율이 기하급수적으로 떨어진다.

○ 데이터를 처리하면 매번 데이터 접근 요청을 하기 때문에 매우 비효율적이다

○ 이를 개선한게 조인 버퍼를 이용한 "Block Nested Loop Join"이다.



![img](https://postfiles.pstatic.net/MjAyMTA4MDJfMjQg/MDAxNjI3OTA2NTM1NjM3.AHPjanCKFsVSQq7-hkE3rzOzH6MBb2pmOcyJ0HdSc4kg.9BxG8V1TNC6Gs8O9ARESoVRTfNaCgDcygJJSB-YgwPYg.PNG.hgstudy_/image.png?type=w966)

○  선행 테이블(dept)의 조건을 먼저 검사한 후 조인컬럼의 emp의 dept_no 인덱스 참조 후 59000을 만족하는 데이터만 최종 결과가 된다.

   



**Block Nested Loop Join**

○  테이블 조인 시 필요한 데이터를 메모리에 일시적으로 저장하여 효율적으로 데이터에 접근

○ STEP1. 테이블 A의 조인 대상을 찾는다.

○ STEP2. 조인할 데이터를 [조인 버퍼]가 가득 찰 때까지 채운다.

○ STEP3. 테이블 B에 테이블 풀스캔, 인덱스 풀스캔, 인덱스 범위 스캔 등으로 데이터 접근한다.

○ 여기서 테이블 B의 스캔하는 횟수는 "조인 버퍼에 데이터가 적재되는 횟수"와 동일하다.

![img](https://postfiles.pstatic.net/MjAyMTA4MDJfMjA5/MDAxNjI3OTA2NzM2ODgz.6NUyBafniYBKfTCUU5HbHPKu_JAThl93GV71xsubTOEg.IKdurrr78eT4SuPCbN3KOhqlMYcLpVcqH8DwOQM6Uvcg.PNG.hgstudy_/image.png?type=w966)

****

****

> Sort Merge Join

○ 조인 키를 모두 정렬한 후에 조인을 수행하는 특징이 있지만, 조인 작업을 위해 항상 정렬 작업이 발생하는 것은 아니다.

○ 이미 앞 단계에서 작업을 수행하는 도중 정렬작업이 미리 되어있다면 조인을 위한 정렬 작업은 발생하지 않을 수 있다.

○ 정렬한 데이터가 많아 메모리에서 모든 정렬 작업을 수행하기 어려운 경우 임시 영역(디스크)을 사용하기 때문에 성능이 떨어질 수 있다.

○ 그래서 일반적으로 대량의 조인 작업에서 정렬 작업을 필요로하는 Sort Merge Join 보다는 "CPU 작업 위주"로 처리하는 "Hash Join"이 성능상 유리하다.



![img](https://postfiles.pstatic.net/MjAyMTA4MDJfNzEg/MDAxNjI3OTA2OTI5MDA1.cPVA8AdxtT9ACQ_wWIlVawzaXXYnKFau08aPzuoAWycg.iMkq-5higAa5vmUl1Ee1fLnCaF1y4XaXhTFCUbrLsuIg.PNG.hgstudy_/image.png?type=w966)



**예제)**

​    ○ dept_no으로 정렬하고 dept_no이 90미만인 것을 SORT로 가져온 후, 결과 값을 dept_no 순으로 정렬한다.

​    ○ dept_no으로 정렬하고 E.sal이 59000것을 가져와서 dept_no 순으로 정렬한다.

​    ○ dept_no이 90미만인 것과 E.sal이 59000것을 다시 정렬하여 운반단위에 넣는다.

![img](https://postfiles.pstatic.net/MjAyMTA4MDJfMTI4/MDAxNjI3OTA2OTQ0NTAy.ypoHju0R81wdwbv8uDhzQHfmAa5ca2r8qels_Rd-6GQg.5aNwH5FfpvtHhxJlsHbsTlVL6w4wJpvhGBdObeVvk4Ug.PNG.hgstudy_/image.png?type=w966)





> Hash Join

○ 해쉬 테이블을 생성하여 조인하는 방식

○ 선행 테이블에서 dept_no이 90미만은 데이터에 해쉬 펑션을 적용하여 "**해쉬 테이블을 생성**"하게 된다. 이 작업을 모든 행위에 대해 "반복 수행"한다.

○ 후행 테이블은 해쉬 테이블의 해쉬 밸류를 검사하며, 샐러리가 59000원 초과하는 행을 찾는다.

○ 그리고 후행 테이블의 조인키를 기준으로 해시 함수를 적용하여 해시 테이블 내에 동일한 데이터를 찾는 것

○ 매칭이 되면 운반 단위로 보내게 된다.

○ 해시 테이블은 조건키(dept_no)으로 찾기 때문에 **등치조건(D.dept_no = E.dept_no)**이 반드시 필요하다.

SELECT * FROM DEPT D JOIN EMP E ON D.dept_no = E.dept WHERE D.dept_no < 90 and E.salary > 59000



![img](https://postfiles.pstatic.net/MjAyMTA4MDJfNjMg/MDAxNjI3OTA3MDc5MTk0.5x99KdqDf1WyWEGb8u994zt37AZunF3xRrhJUUWS0LYg.wtZnHR_MLp72VFD1nnDloBtzu4VUsd0WhjwshPJUGpMg.PNG.hgstudy_/image.png?type=w966)



> Mysql 8.0.18부터 조인 시, 인덱스를 사용하지 않는 모든 쿼리에서 "Hash Join" 사용

ㅇ hash_test_a 테이블 생성

![img](https://postfiles.pstatic.net/MjAyMTA4MDJfMTkx/MDAxNjI3OTA3Mzg4MTUx.VaoDnFffPDFCbS8KxYuSHGBGRVs7HmuTmyYB5n4QAWAg.tcYwqyM4ragAYp-5ztxDyu55DrhMk5El9AoFcrFQxk4g.PNG.hgstudy_/image.png?type=w966)



ㅇ hash_test_b 테이블 생성

![img](https://postfiles.pstatic.net/MjAyMTA4MDJfMjkw/MDAxNjI3OTA3NDExMDI0.7TMi7i7nYAa9z6SIowEgPBpbypJ1i2J6IeNXQAlF8rEg.1B2cAYCX5iLmlCT8GuDbbM09b82GEtJp2F3lFBxJMswg.PNG.hgstudy_/image.png?type=w966)



**ㅇ hash_test_a 테이블과 hash_test_b 테이블 조인 (인덱스 X)**

EXPLAIN FORMAT = TREE SELECT * FROM hash_test_a a  JOIN hash_test_b b ON a.join_key = b.join_key



**ㅇ 결과**

  **=> Hash Join이 일어난 것을 확인할 수 있다.**

-> Inner hash join (a.join_key = b.join_key)  (cost=1.70 rows=2)     -> Table scan on a  (cost=0.18 rows=5)     -> Hash         -> Table scan on b  (cost=0.45 rows=2) 

**ㅇ 인덱스 추가**

ALTER TABLE hash_test_a ADD INDEX join_key_index(join_key) ALTER TABLE hash_test_b ADD INDEX join_key_index(join_key)



**ㅇ hash_test_a 테이블과 hash_test_b 테이블 조인 (인덱스 O)**

EXPLAIN FORMAT = TREE SELECT * FROM hash_test_a a  JOIN hash_test_b b ON a.join_key = b.join_key



**ㅇ 결과**

  **=> Nested Loop Join 이 일어난 것을 확인할 수 있다.**

-> Nested loop inner join  (cost=1.62 rows=3)     -> Filter: (b.join_key is not null)  (cost=0.45 rows=2)         -> Table scan on b  (cost=0.45 rows=2)     -> Index lookup on a using join_key_index (join_key=b.join_key), with index condition: (a.join_ke...



> 정리

|             | Nested Loop Join         | Sort Merge Join                                              | Hash Join                                                  |
| ----------- | ------------------------ | ------------------------------------------------------------ | ---------------------------------------------------------- |
| 동작 방식   | - 이중포문과 유사한 방식 | - 조인 컬럼 기준으로 데이터 정렬- 동등, 비동등- 메모리, 디스크 사용 | - Hash 기법으로 조인- 동등 조인만 가능- CPU 작업 위주 처리 |
| 데이터 읽기 | 랜덤 액세스              | 스캔 방식(넓은 데이터 범위)                                  |                                                            |
| 인덱스      | 사용 O                   | 사용 X                                                       | 없어도 가능                                                |
| 사용        | 온라인 프로그램          | 데이터 집계 업무                                             | 대용량 데이터 집계 업무                                    |





**[출처]** [[Join\] Nested Loop Join, Sort Merge Join, Hash Join](https://blog.naver.com/hgstudy_/222454706706)