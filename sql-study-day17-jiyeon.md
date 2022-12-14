## 13 객체 종류

### 13-1 데이터베이스를 위한 데이터를 저장한 데이터 사전
#### 데이터 사전이란?
오라클 데이터베이스 테이블은 사용자 테이블(user table)과 데이터 사전(data dictionary)으로 나뉜다.     
- 사용자 테이블 : 데이터베이스를 통해 관리할 데이터를 저장하는 테이블        
- 데이터 사전 : 데이터베이스를 구성하고 운영하는데 필요한 모든 정보를 저장하는 특수한 테이블. 데이터베이스가 생성되는 시점에 자동으로 만들어짐     
> 데이터 사전에는 데이터베이스 메모리, 성능, 사용자, 권한, 객체 등 오라클 데이터베이스에 중요한 데이터가 보관되어 있다. 
> 따라서 오라클 데이터베이스는 사용자가 데이터 사전 정보에 직접 접근/작업하는 것을 허용하지 않는다. 
> 그 대신 **'데이터 사전 뷰'** 를 제공하여 SELECT문으로 정보 열람을 할 수 있게 해 두었다.
>> 뷰(view)는 어떤 목적을 위해 테이블 일부 또는 전체 데이터 열람을 주 목적으로 사용하는 객체를 뜻한다.

데이터 사전 뷰는 용도에 따라 이름 아에 다음과 같은 접두어를 지정하여 분류한다.    
|접두어|설명|
|----|-----|
|USER_XXXX|현재 데이터베이스에 접속한 사용자가 소유한 객체 정보|
|ALL_XXXX|현재 데이터베이스에 접속한 사용자가 소유한 객체 또는 다른 사용자가 소유한 객체 중 사용 허가를 받은 객체. 즉, **사용 가능한** 모든 객체 정보|
|DBA_XXXX|데이터베이스 관리를 위한 정보(데이터베이스 관리 권한을 가진 SYSTEM, SYS 사용자만 열람 가능|
|V$_XXXX|데이터베이스 성능 관련 정보(X$_XXXX 테이블의 뷰)|

```sql
-- SCOTT 계정에서 사용 가능한 데이터 사전 살펴보기(DICT 사용)
SELECT * FROM DICT;

SELECT * FROM DICTIONARY;
```

#### USER_접두어를 가진 데이터 사전
현재 오라클에 접속해 있는 사용자가 소유한 객체 정보가 보관되어 있다.    
```sql
SELECT TABLE_NAME
  FROM USER_TABLES;
```
> 접두어(USER) 뒤에 복수형 단어(TABLES)로 이름을 구성하고 있다.    

#### ALL_접두어를 가진 데이터 사전
오라클 데이터베이스에 접속해 있는 사용자가 소유한 객체 및 다른 사용자가 소유한 객체 중 사용이 허락되어 있는 객체 정보를 가지고 있다.     
즉, 접속해 있는 사용자가 **사용할 수 있는** 객체 정보를 가지고 있다.      
```sql
SELECT OWNER, TABLE_NAME
  FROM ALL_TABLES;
```
ALL_TABLES에는 USER_TABLES와 달리 **OWNER열이 하나 더 있다.** OWNER열은 테이블을 소유한 사용자를 명시한다.        

#### DBA_ 접두어를 가진 데이터 사전
데이터베이스 관리 권한을 가진 사용자만 조회할 수 있다.    
> 사용 권한이 없는 사용자는 개체의 존재 여부조차 확인할 수 있다.      

### 13-2
#### 인덱스란?
데이터 검색 성능의 향상을 위해 테이블 열에 사용하는 객체를 의미한다.    
> 인덱스는 사용자가 직접 특정 테이블의 열에 지정할 수도 있지만, 열이 **기본키 또는 고유키** 일 경우에 자동으로 생성됩니다.    

#### 인덱스 생성
```sql
CREATE INDEX 인덱스 이름
    ON 테이블 이름(열 이름1 ASC or DESC,
                열 이름2 ASC or DESC,
                ...                    );
```

#### 인덱스 삭제
```sql
DROP INDEX 인덱스 이름;
```

> 무분별한 인덱스 생성은 오히려 성능을 떨어트리는 원인이 되기도한다.
