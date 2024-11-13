## MySQL

작성되는 모든 문법은 `mysql` 기준입니다.

# 데이터베이스 생성, 수정, 삭제

## 데이터베이스 생성

```sql
-- test_db라는 데이터베이스 생성
create database test_db;
```

## 데이터베이스 이름 수정

```sql
-- 새로운 데이터베이스 생성
create database new_test_db;

-- 기존 데이터베이스의 데이터를 새로운 데이터베이스로 옮기기
-- mysqldump 명령어를 사용하여 데이터 이동 가능 (명령줄에서 실행)
-- mysqldump -u username -p test_db | mysql -u username -p new_test_db

-- 기존 데이터베이스 삭제 (필요 시)
drop database test_db;
```

## 데이터베이스 삭제

```sql
-- test_db라는 데이터베이스 삭제
drop database if exists test_db;
```

# 데이터베이스 유저 생성, 수정, 삭제, 권한 관리

## 유저 생성

```sql
-- 'username'이라는 이름과 'password'라는 비밀번호를 가진 사용자 생성
create user 'username'@'localhost' identified by 'password';
```

- localhost는 해당 유저가 로컬에서 접속할 수 있도록 설정하는 부분
- 외부 접속을 허용하기 위해서는 `%` 혹은 구체적인 Ip를 적어줘야 한다
  - 맥에서는 [Localhost](http://Localhost) 대신 127.0.0.1 을 작성해야 하는 이슈가 있다

## 유저 수정

```sql
-- 'username' 사용자의 비밀번호를 'newpassword'로 변경
alter user 'username'@'localhost' identified by 'newpassword';
```

## 유저 삭제

```sql
-- 'username' 사용자를 삭제
drop user 'username'@'localhost';
```

## 권한 부여

```sql
-- 'username' 사용자에게 'test_db' 데이터베이스의 모든 권한을 부여
grant all privileges on test_db.* to 'username'@'localhost';

-- 특정 권한만 부여 (예: select, insert, update, delete)
grant select, insert, update, delete on test_db.* to 'username'@'localhost';
```

- test_db.\* → test_db 데이터베이스의 모든 테이블에 대한 권한 부여
- all privileges → 모든 권한 부여, 특정 권한만 부여할 경우 select, insert등 지정 가능

## 권한 철회

```sql
-- 'username' 사용자의 'test_db' 데이터베이스에 대한 모든 권한을 철회
revoke all privileges on test_db.* from 'username'@'localhost';

-- 특정 권한만 철회 (예: select, insert)
revoke select, insert on test_db.* from 'username'@'localhost';
```

## 권한 적용(갱신)

```sql
flush privileges;
```

# 테이블 생성, 수정, 삭제

## 테이블 생성

```sql
CREATE TABLE 테이블명 (
    컬럼명 데이터타입 [UNSIGNED] [NOT NULL] [AUTO_INCREMENT],
    컬럼명 데이터타입 [UNIQUE],
    PRIMARY KEY (컬럼명),
    FOREIGN KEY (컬럼명) REFERENCES 참조테이블(참조컬럼) [ON DELETE 옵션] [ON UPDATE 옵션]
);
```

### 사용 예제

```sql
CREATE TABLE users (
    id INT UNSIGNED NOT NULL AUTO_INCREMENT,
    username VARCHAR(50) NOT NULL UNIQUE,
    email VARCHAR(100) NOT NULL,
    age INT UNSIGNED,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP,
    PRIMARY KEY (id)
);
```

## 테이블 수정

```sql
ALTER TABLE 테이블명
    ADD 컬럼명 데이터타입 [UNSIGNED] [NOT NULL] [UNIQUE] [AUTO_INCREMENT],
    MODIFY 컬럼명 데이터타입 [UNSIGNED] [NOT NULL],
    DROP COLUMN 컬럼명,
    ADD PRIMARY KEY (컬럼명),
    ADD UNIQUE (컬럼명),
    ADD FOREIGN KEY (컬럼명) REFERENCES 참조테이블(참조컬럼) [ON DELETE 옵션] [ON UPDATE 옵션];
```

### 사용 예제

```sql
ALTER TABLE users
    ADD COLUMN phone VARCHAR(15) UNIQUE,
    MODIFY COLUMN age TINYINT UNSIGNED NOT NULL;
```

- `DROP COLUMN` : 특정 컬럼을 삭제할 때 사용

## 테이블 삭제

```sql
DROP TABLE 테이블명;
```

## `UNIQUE` 제약 조건

```sql
-- 테이블 생성 시 추가
CREATE TABLE example (
    username VARCHAR(50) UNIQUE
);

-- 테이블 생성 후 추가
ALTER TABLE example
ADD UNIQUE (username);
```

## `PRIMARY KEY` 설정

```sql
-- 테이블 생성 시 설정
CREATE TABLE orders (
    order_id INT UNSIGNED NOT NULL AUTO_INCREMENT,
    PRIMARY KEY (order_id)
);

-- 테이블 생성 후 설정
ALTER TABLE orders
ADD PRIMARY KEY (order_id);
```

## `FOREIGN KEY` 설정

```sql
-- 테이블 생성 시 설정
CREATE TABLE orders (
    order_id INT UNSIGNED NOT NULL AUTO_INCREMENT,
    user_id INT UNSIGNED,
    PRIMARY KEY (order_id),
    FOREIGN KEY (user_id) REFERENCES users(id) ON DELETE CASCADE ON UPDATE CASCADE
);

-- 테이블 생성 후 설정
ALTER TABLE orders
ADD FOREIGN KEY (user_id) REFERENCES users(id);
```

## 추가 고려 사항

- NOT NULL
  - NULL 값 허용 여부를 설정 외래키나 주요 데이터 컬럼에 자주사용
- AUTO_INCREMENT
  - 고유 값 자동으로 증가, 주로 pk에서 자주 사용
  - unsigned와 함께 자주 사용
- 데이터 타입 일치
  - 외래 키와 참조 대상 컬럼의 데이터 타입이 반드시 일치해야 한다
  - `INT UNSIGNED` 로 참조시 참조받는 컬럼도 `INT UNSIGNED` 여야 한다
- UNIQUE 수정
  - ALTER TABLE을 사용해 컬럼에 UNIQUE 제약 조건을 추가하거나 제거 가능

# 데이터 생성, 수정, 삭제

## INSERT

```sql
INSERT INTO 테이블명 (컬럼1, 컬럼2, ...) VALUES (값1, 값2, ...);
INSERT INTO 테이블명 SET 컬럼1 = 값1, 컬럼2 = 값2, ...;
```

## UPDATE

```sql
UPDATE 테이블명 SET 컬럼1 = 값1, 컬럼2 = 값2, ... WHERE 조건;
```

## DELETE

```sql
DELETE FROM 테이블명 WHERE 조건;
```

# 데이터 조회

## 기본 조회

```sql
SELECT * FROM 테이블명;
```

```sql
SELECT 컬럼1, 컬럼2, FROM 테이블명;
```

## 조건절

```sql
SELECT * FROM 테이블명 WHERE 조건;
```

```sql
SELECT * FROM employees WHERE salary > 500000000000000;
```

## `GROUP BY` (그룹화)

```sql
SELECT 컬럼1, COUNT(*) FROM 테이블명 GROUP BY 컬럼1;
```

```sql
SELECT department, COUNT(*) FROM employees GROUP BY department;
```

## `HAVING` (그룹화 후 조건절)

```sql
SELECT 컬럼1, COUNT(*) FROM 테이블명 GROUP BY 컬럼1 HAVING COUNT(*) > 5;
```

```sql
SELECT department, COUNT(*) FROM employees GROUP BY department HAVING COUNT(*) > 10;
```

## `SELECT` 절에 `CASE` 문 사용

```sql
SELECT 컬럼1,
       CASE
           WHEN 조건 THEN '값1'
           WHEN 조건2 THEN '값2'
           ELSE '기타'
       END AS alias
FROM 테이블명;
```

```sql
SELECT salary,
       CASE
           WHEN salary > 50000 THEN 'High'
           WHEN salary BETWEEN 30000 AND 50000 THEN 'Medium'
           ELSE 'Low'
       END AS salary_level
FROM employees;
```

## `IF` 문 사용

```sql
SELECT 컬럼1,
       IF(조건, 'True', 'False') AS alias
FROM 테이블명;
```

```sql
SELECT name, IF(salary > 50000, 'High Salary', 'Low Salary') AS salary_status FROM employees;
```

## `NULL` 처리 함수

- `COALESCE` : 첫번째로 `NULL` 이 아닌 값 반환

```sql
SELECT COALESCE(컬럼1, 컬럼2, '기본값') FROM 테이블명;
```

- `IFNULL` : `NULL` 일 경우 지정된 값 반환

```sql
SELECT IFNULL(컬럼1, '기본값') FROM 테이블명;
```

## 집계 함수

- 참고로 집계함수에서 `null` 은 그냥 무시된다
  - 사칙연산에선 `null` 이 포함된 연산은 전부 결과가 `null`
- `SUM`

```sql
SELECT SUM(컬럼명) FROM 테이블명;
```

- `MIN`

```sql
SELECT MIN(컬럼명) FROM 테이블명;
```

- `MAX`

```sql
SELECT MAX(컬럼명) FROM 테이블명;
```

- `AVG`

```sql
SELECT AVG(컬럼명) FROM 테이블명;
```

- `COUNT`

```sql
SELECT COUNT(*) FROM 테이블명;
```

## `DISTINCT` (중복 제거)

```sql
SELECT DISTINCT 컬럼1 FROM 테이블명;
```

# JOIN

## INNER JOIN (기본적인 INNER JOIN)

```sql
SELECT a.컬럼1, b.컬럼2
FROM 테이블1 a
INNER JOIN 테이블2 b ON a.공통컬럼 = b.공통컬럼;
```

## INNER JOIN (별칭 없이 JOIN)

```sql
SELECT a.컬럼1, b.컬럼2
FROM 테이블1 a
JOIN 테이블2 b ON a.공통컬럼 = b.공통컬럼;
```

## LEFT JOIN

```sql
SELECT a.컬럼1, b.컬럼2
FROM 테이블1 a
LEFT JOIN 테이블2 b ON a.공통컬럼 = b.공통컬럼;
```

# 서브 쿼리

## 단일 값 서브쿼리

- WHERE 절에서 사용

```sql
SELECT name, salary
FROM employees
WHERE salary > (SELECT AVG(salary) FROM employees);
```

- SELECT 절에서 사용

```sql
SELECT name, (SELECT AVG(salary) FROM employees) AS average_salary
FROM employees;
```

## 다수 행 서브쿼리

- IN 연산자 사용

```sql
SELECT name
FROM employees
WHERE department_id
	IN (SELECT department_id FROM departments WHERE location = 'New York');
```

## EXISTS 서브쿼리

- EXISTS 서브쿼리

```sql
SELECT name
FROM employees e
WHERE EXISTS
	(SELECT 1
		FROM departments d
		WHERE e.department_id = d.department_id
		AND d.location = 'New York');
```
