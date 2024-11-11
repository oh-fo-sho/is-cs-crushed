# SQL (Structured Query Language)

SQL는 데이터베이스에서 데이터를 정의하고 관리하며 조작하는 표준 프로그래밍 언어를 말한다. SQL은 RDBMS에서 주로 사용되며 데이터를 조회, 삽입, 업데이트, 삭제하는 등의 작업을 수행한다. SQL은 관계형 데이터베이스의 데이터를 다루기 위해 개발되었고 표준화된 명령어 세트를 통해 데이터베이스의 모든 작업을 쉽게 처리할 수 있게 한다

## 종류 및 주요 명령어

SQL은 그 기능에 따라 여러 명령어로 구분 가능하다.

1. DDL(Data Definition Language) 데이터베이스 구조를 정의하는 명령어
   1. CREATE : 데이터베이스, 테이블, 인덱스 등을 생성
   2. ALTER : 기존 테이블 구조 수정
   3. DROP : 데이터베이스나 테이블 삭제
   4. TRUNCATE : 테이블의 모든 데이터를 삭제, 테이블의 구조를 유지
2. DML(Data Manipulation Language) 데이터 조작을 위한 명령어
   1. SELECT : 테이블에서 데이터를 조회
   2. INSERT : 테이블에 새로운 데이터 삽입
   3. UPDATE : 테이블의 데이터 수정
   4. DELETE : 테이블 데이터 삭제
3. DCL(Data Control Language) 데이터베이스 접근 권한을 제어하는 명령어
   1. GRANT : 사용자에게 특정 작업을 수행할 수 있는 권한 부여
   2. REVOKE : 사용자에게 부여한 특정 작업 권한 회
4. TCL(Transaction Control Language) 트랜잭션을 제어하는 명령
   1. COMMIT : 트랜잭션의 작업을 영구히 저장
   2. ROLLBACK : 트랜잭션 작업을 취소하고 이전 상태로 되돌림
   3. SAVEPOINT : 트랜잭션 내 특정 시점에 이름을 지정해 저장하여 해당 시점으로 롤백할 수 있게 함

## 특징

- 표준화된 언어
  - SQL은 표준화 된 언어로 관계형 데이터베이스의 기본 언어로 사용된다
- 관계형 데이터베이스와 호환
  - SQL은 Oracle, MySQL, PostgreSQL, Microsoft SQL Server 등 다양한 RDBMS에서 사용되고 구조적으로 큰 차이 없이 SQL을 통해 데이터를 다룰 수 있게한다
- 높은 가독성
  - (너무 길지 않다면…) 대부분 영어 문장 처럼 구성되어 있어 가독성이 높다
- 복잡한 쿼리 처리
  - SQL은 여러 테이블의 데이터를 조인하거나 그룹화, 집계, 정렬 등을 쉽게 수행할 수 있는 기능 제공

```sql
-- Students 테이블 생성
CREATE TABLE Students (
    student_id INT PRIMARY KEY,
    name VARCHAR(100),
    major VARCHAR(100)
);

-- Students 테이블에 데이터 삽입
INSERT INTO Students (student_id, name, major) VALUES (1, 'Alice', 'Computer Science');
INSERT INTO Students (student_id, name, major) VALUES (2, 'Bob', 'Mathematics');

-- 데이터 조회
SELECT * FROM Students;

-- 데이터 수정
UPDATE Students
SET major = 'Physics'
WHERE student_id = 2;

-- 데이터 삭제
DELETE FROM Students
WHERE student_id = 1;
```

## 데이터 타입

SQL에서 데이터 타입은 데이터베이스 테이블에 저장될 값의 종류를 정의하는 것이다. 데이터 타입은 크게 문자, 숫자, 날짜, boolean 형으로 구분 가능하고 각 데이터 타입은 저장할 수 있는 데이터의 종류와 크기를 지정한다.

### 1. **문자형 데이터 타입 (Character Data Types)**

- **CHAR(size)**
  - 고정 길이 문자 데이터 타입
  - `size`는 저장할 문자의 개수를 지정
  - 만약 데이터가 더 짧으면 공백으로 채워진다
  - 예
    - `CHAR(10)`은 10자리 고정 길이 문자열을 저장
- **VARCHAR(size)**
  - 가변 길이 문자 데이터 타입
  - `size`는 최대 문자 수를 지정하며, 실제 저장된 문자열의 길이에 따라 공간이 결정됨
  - 예
    - `VARCHAR(50)`은 최대 50자까지 저장할 수 있다
- **TEXT**
  - 매우 긴 텍스트 데이터를 저장할 때 사용
  - 길이 제한이 거의 없으며, 길이가 매우 긴 텍스트를 저장할 수 있다
- **CLOB (Character Large Object)**
  - 대용량 텍스트 데이터를 저장하는데 사용
  - 예를 들어, 기사, 책 등 긴 텍스트 정보를 저장할 때 사용

### 2. **숫자형 데이터 타입 (Numeric Data Types)**

- **INT (INTEGER)**
  - 정수형 데이터 타입
    - 숫자를 저장할 수 있으며, 저장할 수 있는 값의 범위는 데이터베이스 시스템에 따라 다르다
- **SMALLINT**
  - `INT`보다 작은 정수형 데이터 타입
  - 대체로 `32,768`에서 `32,767`까지의 값을 저장할 수 있다
- **BIGINT**
  - 더 큰 정수형 데이터 타입으로
  - `INT`보다 더 큰 범위의 값을 저장할 수 있다
- **DECIMAL(p, s) 또는 NUMERIC(p, s)**
  - 고정 소수점 숫자를 저장
  - `p`는 총 자릿수(정수 부분과 소수 부분의 합)이고, `s`는 소수점 이하 자릿수를 의미함
  - 예
    - `DECIMAL(10, 2)`는 최대 8자리 정수와 2자리 소수를 저장할 수 있다(`99999999.99`)
- **FLOAT**
  - 부동 소수점 숫자를 저장
  - 실수형 데이터를 저장할 수 있으며, `FLOAT`의 범위는 구현에 따라 다르다
- **REAL**
  - `FLOAT`와 유사하지만, 좀 더 적은 정밀도를 가진 부동 소수점 숫자 타입이다

### 3. **날짜/시간형 데이터 타입 (Date/Time Data Types)**

- **DATE**
  - 날짜를 저장하는 데이터 타입으로, 일반적으로 `YYYY-MM-DD`
- **TIME**
  - 시간을 저장하는 데이터 타입으로, 일반적으로 `HH:MM:SS`
- **DATETIME**
  - 날짜와 시간을 함께 저장하는 데이터 타입
  - `YYYY-MM-DD HH:MM:SS` 형식
  - `DATE`와 `TIME`을 합친 형태
- **TIMESTAMP**
  - 날짜와 시간을 저장하는 데이터 타입으로, `DATETIME`과 유사하지만, 데이터베이스 서버의 시스템 시간에 의존하여 자동으로 값을 삽입하거나 갱신할 수 있다
- **YEAR**
  - 연도를 저장하는 데이터 타입
  - `YYYY` 형식으로 4자리 숫자를 저장

### 4. **불리언형 데이터 타입 (Boolean Data Types)**

- **BOOLEAN**
  - 참(True) 또는 거짓(False) 값을 저장하는 데이터 타입
  - `TRUE` 또는 `FALSE` 값을 사용
  - 지원하지 않는 경우도 있다

### 5. **이진 데이터 타입 (Binary Data Types)**

- **BINARY(size)**
  - 고정 길이 이진 데이터를 저장
  - `size`는 저장할 이진 데이터의 크기를 바이트 단위로 지정
- **VARBINARY(size)**
  - 가변 길이 이진 데이터를 저장
  - `size`는 최대 바이트 크기를 지정하며, 실제 저장된 데이터의 크기에 맞게 공간이 할당된다
- **BLOB (Binary Large Object)**
  - 대용량 이진 데이터를 저장하는 데 사용
  - 이미지, 오디오 파일, 비디오 파일 등 이진 데이터를 저장할 때 사용

### 6. **기타 데이터 타입**

- **ENUM**
  - 열거형 데이터 타입으로, 미리 정의된 값 중 하나만을 저장할 수 있다. 예를 들어, '여름', '가을', '겨울'과 같은 값을 저장할 수 있습니다.
  - 예
    - `ENUM('여름', '가을', '겨울')`
- **SET**
  - 여러 개의 값 중에서 여러 값을 동시에 저장할 수 있는 데이터 타입
  - `ENUM`과 비슷하지만, 여러 개의 값이 동시에 선택될 수 있다
  - 예
    - `SET('빨강', '파랑', '초록')`

### 7. **UUID (Universally Unique Identifier)**

- **UUID**
  - 128비트 고유 식별자를 저장하는 데이터 타입
  - 주로 시스템 내에서 고유한 식별자를 생성할 때 사용
  - 예
    - `e8b5b3fc-6cfe-4b89-94a1-08b4d347eb8b`
