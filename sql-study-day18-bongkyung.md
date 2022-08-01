## 13 - 3 테이블처럼 사용하는 뷰
<br/>

> 뷰란?

- 가상 테이블(virtual table)로 부르는 뷰(view)는 하나 이상의 테이블을 조회하는 SELECT문을 저장한 객체를 뜻한다.
<br/>

> 뷰의 사용 목적(편리성)

- 뷰는 크게 다음 두 가지 목적을 위해 주로 사용한다.
  1. 편리성 : SELECT문의 복잡도를 완화하기 위해
  2. 보안성 : 테이블의 특정 열을 노출하고 싶지 않을 경우
<br/>

> 뷰의 사용 목적(보안성)

- 뷰를 사용하는 또 다른 중요한 이유는 데이터 보안과 관련이 있다.
- 테이블의 일부 데이터 또는 조인이나 여러 함수 등으로 가공을 거친 데이터만 SELECT하는 뷰 열람 권한을 제공하는 것이 불필요한 데이터 노출을 막을 수 있기 때문에 더 안전한 방법이다.
<br/>

> 뷰 생성

- 뷰는 CREATE문으로 생성할 수 있다.
```sql
CREATE [OR REPLACE] [FORCE | NOFORCE] VIEW 뷰 이름 (열 이름1, 열 이름2, ...)
	AS (저장SELECT문)
[WITH CHECK OPTION [CONSTRAINT 제약 조건]]
[WITH READ ONLY [CONSTRAINT 제약 조건]]
```

| 요소 | 설명 |
|:----:|:------------:|
| OR REPLACE | 같은 이름의 뷰가 이미 존재할 경우에 현재 생성할 뷰로 대체하여 생성(선택) |
| FORCE | 뷰가 저장할 SELECT문의 기반 테이블이 존재하지 않아도 강제로 생성(선택) |
| NOFORCE | 뷰가 저장할 SELECT문의 기반 테이블이 존재할 경우에만 생성(기본값)(선택) |
| 뷰 이름 | 생성할 뷰 이름을 지정(필수) |
| 저장할 SELECT문 | 생성할 뷰에 저장할 SELECT문 지정(필수) |
| WITH CHECK OPTION | 지정한 제약 조건을 만족하는 데이터에 한해 DML 작업이 가능하도록 뷰 생성(선택) |
| WITH READ ONLY | 뷰의 열람, 즉 SELECT만 가능하도록 뷰 생성(선택) |
<br/>

> 뷰 삭제

- 뷰를 삭제할 때 DROP문을 사용한다.
<br/>

> 인라인 뷰를 사용한 TOP-N SQL문

- CREATE문을 통해 객체로 만들어지는 뷰 외에 SQL문에서 일회성으로 만들어서 사용하는 뷰를 인라인 뷰(inline view)라고 한다. SELECT문에서 사용되는 서브쿼리, WITH절에서 미리 이름을 정의해 두고 사용하는 SELECT문 등이 이에 해당한다.
<br/>

## 13 - 4 규칙에 따라 순번을 생성하는 시퀀스
<br/>

> 시퀀스란?

- 시퀀스(sequence)는 오라클 데이터베이스에서 특정 규칙에 맞는 연속 숫자를 생성하는 객체이다.

- 단지 연속하는 새로운 번호를 만드는 일이라면 다음과 같이 MAX함수에 1을 더한 값을 사용해도 상관없을 것이다.
```sql
SELECT MAX(글 번호) + 1
  FROM 게시판 테이블
```

▶︎ 하지만 이 방식은 테이블 데이터가 많아질수록 가장 큰 데이터를 찾고 새로운 번호를 계산하는 시간이 함께 늘어나므로 아쉬운 부분이 있다. 또한 동시에 여러 곳에서 새로운 번호를 요구했을 경우에 SELECT문의 결과 값이 같게 나와 번호가 중복될 수도 있다.
이와 비교하여 시퀀스는 단순히 번호 생성을 위한 객체이지만 지속적이고 효율적인 번호 생성이 가능하므로 여러모로 자주 사용하는 객체이다.
<br/>

> 시퀀스 생성

- 시퀀스 역시 CREATE문으로 생성하며 다음과 같이 다양한 옵션을 지정할 수 있다.
```sql
CREATE SEQUENCE 시퀀스 이름 // 1.
[INCREMENT BY n] // 2.
[START WITH n] // 3.
[MAXVALUE n | NOMAXVALUE] // 4.
[MINVALUE n | NOMINVALUE] // 5.
[CYCLE | NOCYCLE] // 6.
[CACHE n | NOCACHE] // 7.
```

| 번호 | 설명 |
|:------:|:----------------------:|
| 1. | 생성할 시퀀스 이름 지정. 아래 절(2~7)들을 지정하지 않았을 경우 1부터 시작하여 1만큼 계속 증가하는 시퀀스가 생성(필수) |
| 2. | 시퀀스에서 생성할 번호의 증가 값(기본값은 1)(선택) |
| 3. | 시퀀스에서 생성할 번호의 시작 값(기본값은 1)(선택) |
| 4. | 시퀀스에서 생성할 번호의 최댓값 지정, 최댓값은 시작 값(START WITH) 이상, 최솟값(MINVALUE)을 초괏값으로 지정. NOMAXVALUE로 지정하였을 경우 오름차순이면 10^27, 내림차순일 경우 -1로 설정(선택) |
| 5. | 시퀀스에서 생성할 번호의 최솟값 지정, 최솟값은 시작 값(START WITH) 이하, 최댓값(MAXVALUE) 미만 값으로 지정. NOMINVALUE로 지정하였을 경우 오름차순이면 1, 내림차순이면 -10^26으로 설정(선택) |
| 6. | 시퀀스에서 생성한 번호가 최댓값(MAXVALUE)에 도달했을 경우 CYCLE이면 시작 값(START WITH)에서 다시 시작, NOCYCLE이면 번호 생성이 중단되고, 추가 번호 생성을 요청하면 오류 발생(선택) |
| 7. | 시퀀스가 생성할 번호를 메모리에 미리 할당해 놓은 수를 지정, NOCACHE는 미리 생성하지 않도록 설정. 옵션을 모두 생략하면 기본값은 20(선택) |

```sql
// 시퀀스 생성하기
CREATE SEQUENCE SEQ_DEPT_SEQUENCE
INCREMENT BY 10
START WITH 10
MAXVALUE 90
MINVALUE 0
NOCYCLE
CACHE 2;
```
<br/>

> 시퀀스 사용


- 생성된 시퀀스를 사용할 때는 [시퀀스 이름.CURRVAL]과 [시퀀스 이름.NEXTVAL]을 사용할 수 있다. CURRVAL은 시퀀스에서 마지막으로 생성한 번호를 반환하며 NEXTVAL는 다음 번호를 생성한다. 그리고 CURRVAL은 시퀀스를 생성하고 바로 사용하면 번호가 만들어진 적이 없으므로 오류가 난다.
<br/>

> 시퀀스 수정

- ALTER 명령어로 시퀀스를 수정하고 DROP 명령어로 시퀀스를 삭제한다.
```sql
// 기본 형식
ALTER SEQUENCE 시퀀스 이름
[INCREMENT BY n]
[MAXVALUE n | NOMAXVALUE]
[MINVALUE n | NOMINVALUE]
[CYCLE | NOCYCLE]
[CACHE n | NOCACHE]
```
<br/>

> 시퀀스 삭제

- DROP SEQUENCE를 사용하면 시퀀스를 삭제할 수 있다.
<br/>

## 13 - 5 공식 별칭을 지정하는 동의어
<br/>

> 동의어란?

동의어(synonym)는 테이블•뷰•시퀀스 등 객체 이름 대신 사용할 수 있는 다른 이름을 부여하는 객체이다. 주로 테이블 이름이 너무 길어 사용이 불편할 때 좀 더 간단하고 짧은 이름을 하나 더 만들어 주기 위해 사용한다.
```sql
// 기본 형식
CREATE [PUBLIC] SYNONYM 동의어 이름
FOR [사용자.][객체 이름];
```

| 요소 | 설명 |
|:-----:|:---------------:|
| PUBLIC | 동의어를 데이터베이스 내 모든 사용자가 사용할 수 있도록 설정. 생략할 경우 동의어를 생성한 사용자만 사용 가능(PUBLIC으로 생성되어도 본래 객체의 사용 권한이 있어야 사용 가능)(선택) |
| 동의어 이름 | 생성할 동의어 이름(필수) |
| 사용자. | 생성할 동의어의 본래 객체 소유 사용자를 지정. 생략할 경우 현재 접속한 사용자로 지정(선택) |
| 객체이름 | 동의어를 생성할 대상 객체 이름(필수) |

▶︎ 동의어는 SELECT문의 SELECT절, FROM절에서 사용한 열 또는 테이블 별칭과 유사하지만, 오라클 데이터베이스에 저장되는 객체이기 때문에 일회성이 아니라는 점에서 차이가 난다.
<br/>

> 동의어 생성

▶︎ 책 355p
<br/>

> 동의어 삭제

- DROP SYNONYM을 사용하여 동의어를 삭제한다.
<br/>

## 잊기 전에 한 번 더!
<br/>

Q1.
```sql
// 1.
CREATE TABLE EMPIDX
AS SELECT * FROM EMP;
```
```sql
// 2.
CREATE INDEX IDX_EMPIDX_EMPNO
ON EMPIDX(EMPNO);
```
```sql
// 3.   :(
SELECT * 
  FROM USER_INDEXES 
 WHERE INDEX_NAME = 'IDX_EMPIDX_EMPNO';
 ```

Q2.
 ```sql
 CREATE VIEW OR REPLACE EMPIDX_OVER15K
 AS (SELECT EMPNO, ENAME, JOB, DEPTNO, SAL,
 	NVL2(COMM, 'O', 'X') AS COMM
    FROM EMPIDX
    WHERE SAL > 1500;
```

Q3.
```sql
// 1.
CREATE TABLE DEPTSEQ
AS SELECT * FROM DEPT;
```
```sql
// 2.
CREATE SEQUENCE SEQ_DEPTSEQ_SEQUENCE
START WITH 1
INCREMENT BY 1
MAXVALUE 99
MINVALUE 1
NOCYCLE
NOCACHE;
```
```sql
// 3.   :(
INSERT INTO DEPTSEQ (DEPTNO, DNAME, LOC) 
VALUES (SEQ_DEPTSEQ.NEXTVAL, 'DATABASE', 'SEOUL');

INSERT INTO DEPTSEQ (DEPTNO, DNAME, LOC) 
VALUES (SEQ_DEPTSEQ.NEXTVAL, 'WEB', 'BUSAN');

INSERT INTO DEPTSEQ (DEPTNO, DNAME, LOC) 
VALUES (SEQ_DEPTSEQ.NEXTVAL, 'MOBILE', 'ILSAN');
```


---
