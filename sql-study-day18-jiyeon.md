### 13-3 테이블처럼 사용하는 뷰
- 가상테이블. 하나 이상의 테이블을 조회하는 SELECT문을 저장한 객체를 뜻한다.     
- 물리적 데이터를 따로 저장하지는 않는다.    
- 뷰를 SELECT문의 FROM절에 사용하면 특정 테이블을 조회하는 것과 같은 효과를 얻을 수 있다.      

#### 뷰의 사용 목적(편리성)
1. 편리성 : SELECT문의 복잡도를 완화하기 위해
2. 보안성 : 테이블의 특정 열을 노출하고 싶지 않을 경우      

#### 뷰 생성
뷰 생성에 앞서 뷰 생성 권한을 부여해줘야 한다. 
```
SQLPLUS SYSTEM/oracle

GRANT CREATE VIEW TO SCOTT;
```
```SQL
CREATE [OR REPLACE] [FORCE | NOFORCE] 뷰 이름 (열 이름1, 열 이름2, ...)

    AS (저장할 SELECT문)

[WITH CHECK OPTION [CONSTRAINT 제약 조건]]

[WITH READ ONLY [CONSTRAINT 제약 조건]];
```

#### 뷰 삭제
```sql
DROP VIEW 뷰 이름;
```
> 뷰는 실제 데이터가 아닌 SELECT문만 저장하므로 뷰를 삭제해도 테이블이나 데이터가 삭제되는 것은 아니다.    

#### 인라인 뷰를 사용한 TOP-N SQL문
- 인라인 뷰 : SQL문에서 일회성으로 만들어서 사용하는 뷰     
- ROWNUM : pseudo column. 데이터가 저장되는 실제 테이블에 존재하지는 않지만, 
특정 목적을 위해 테이블에 저장되어 있는 열처럼 사용 가능한 열      

인라인 뷰와 ROWNUM을 사용하면 ORDER BY절을 통해 정렬된 결과 중 최상위 몇 개 데이터만 출력 가능    
```sql
-- 인라인 뷰로 TOP-N 추출하기(서브쿼리사용)

SELECT ROWNUM, E.*
  FROM (SELECT *
          FROM EMP E
         ORDER BY SAL DESC) E
 WHERE ROWNUM <= 3;
 
 
-- 인라인 뷰로 TOP-N 추출하기(WITH절 사용)
WITH E AS (SELECT * FROM EMP ORDER BY SAL DESC)
SELECT ROWNUM, E.*
  FROM E
 WHERE ROWNUM <= 3;
```

### 13-4 규칙에 따라 순번을 생성하는 시퀀스
특정 규칙에 맞는 연속 숫자를 생성하는 객체.     

#### 시퀀스 생성
```sql
CREATE SEQUENCE 시퀀스 이름
[INCREMENT BY n] -- 증가 값
[START WITH n] -- 시작 값
[MAXVALUE n | NOMAXVALUE n]
[MINVALUE n | NOMINVALUE n]
[CYCLE | NOCYCLE]
[CACHE n | NOCACHE]
```

#### 시퀀스 사용
**[시퀀스 이름.CURRVAL]** : 시퀀스에서 마지막으로 생성한 번호를 반환 (시퀀스 생성 후 바로 사용하면 오류남. 왜냐하면 번호가 만들어진 적이 없으니까)                
**[시퀀스 이름.NEXTVAL]** : 다음 번호를 생성    
```sql
-- 시퀀스 생성하기
CREATE SEQUENCE SEQ_DEPT_SEQUENCE
 INCREMENT BY 10
 START WITH 10
 MAXVALUE 90
 MINVALUE 0
 NOCYCLE
 CACHE 2;
 
-- 시퀀스에서 생성한 순번을 사용한 INSERT문 실행하기
INSERT INTO DEPT_SEQUENCE (DEPTNO, DNAME, LOC)
VALUES (SEQ_DEPT_SEQUENCE.NEXTVAL, 'DATABASE', 'SEOUL');
```
#### 시퀀스 수정
ALTER 명령어로 시퀀스를 수정할 수 있다. 시퀀스 수정 == 옵션 재설정     
> START WITH 값은 변경할 수 없다.    

#### 시퀀스 삭제
```SQL
DROP SEQUENCE SEQ_DEPT_SEQUENCE;
```

### 13-5 공식 별칭을 지정하는 동의어
#### 동의어란?
테이블, 뷰, 시퀀스 등 객체 이름 대신 사용할 수 있는 다른 이름을 부여하는 객체    
```sql
CREATE [PUBLIC] SYNONYM 동의어 이름 
FOR [사용자.][객체 이름];
```
> 동의어는 SELECT문의 SELECT절, FROM절에서 사용한 열 또는 별칭과 유사하지만,
> 오라클 데이터베이스에 저장되는 객체이기 때문에 일회성이 아니라는 점에서 차이가 난다.    

#### (권한 부여 후) 동의어 생성
```sql
CREATE SYNONYM E
   FOR EMP;
```

#### 동의어 삭제
```SQL
DROP SYNONYM E;
```

#### 잊기전에
```sql
-- Q1
-- 1) 
CREATE TABLE EMPIDX
    AS SELECT * FROM EMP;

-- 2)
CREATE INDEX IDX_EMPIDX_EMPNO
    ON EMPIDX(EMPNO);

-- 3) 
SELECT * FROM USER_IND_COLUMNS;

-- Q2
CREATE OR REPLACE VIEW EMPIDX_OVER15K
  AS (SELECT EMPNO, ENAME, JOB, DEPTNO, SAL, NVL2(COMM, 'O', 'X') AS COMM
        FROM EMPIDX
        WHERE SAL > 1500);
        
-- Q3

-- 1) 
CREATE TABLE DEPTSEQ
AS SELECT * FROM DEPT;

-- 2)
CREATE SEQUENCE SEQ_DEPTSEQ
  INCREMENT BY 1
  START WITH 1
  MAXVALUE 90
  MINVALUE 1
  NOCYCLE
  NOCACHE;
  
-- 3)
INSERT INTO DEPTSEQ (DEPTNO, DNAME, LOC)
VALUES (SEQ_DEPTSEQ.NEXTVAL, 'DATABASE', 'SEOUL');

INSERT INTO DEPTSEQ (DEPTNO, DNAME, LOC)
VALUES (SEQ_DEPTSEQ.NEXTVAL, 'WEB', 'BUSAN');

INSERT INTO DEPTSEQ (DEPTNO, DNAME, LOC)
VALUES (SEQ_DEPTSEQ.NEXTVAL, 'MOBILE', 'ILSAN');
```
