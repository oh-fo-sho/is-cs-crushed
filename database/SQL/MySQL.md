## MySQL 기초 SQL문

### 데이터베이스 생성 및 선택

```sql
-- 1. 기존에 같은 이름의 데이터베이스가 있다면 삭제
DROP DATABASE IF EXISTS sample_db;

-- 2. 새로운 데이터베이스 생성
CREATE DATABASE sample_db;

-- 3. 데이터베이스 선택
USE sample_db;
```

### 사용자 생성 및 권한 부여

```sql
-- 1. 사용자 생성 (패스워드는 'password'로 설정)
CREATE USER 'sample_user'@'localhost' IDENTIFIED BY 'password';

-- 2. sample_db에 대한 모든 권한을 sample_user에게 부여
GRANT ALL PRIVILEGES ON sample_db.* TO 'sample_user'@'localhost';

-- 3. 권한 적용
FLUSH PRIVILEGES;
```

새로운 사용자를 생성하고 권한 부여, 여기에서는 `sample_user` 라는 이름의 사용자를 생성하여 `sample_db` 에 대한 모든 권한을 부여

### 테이블 생성

```sql
-- customers 테이블 생성
CREATE TABLE customers (
    id INT AUTO_INCREMENT PRIMARY KEY,
    name VARCHAR(50) NOT NULL,
    email VARCHAR(100) NOT NULL UNIQUE,
    created_at TIMESTAMP DEFAULT CURRENT_TIMESTAMP
);
```

### 데이터 삽입(Create)

```sql
-- 데이터 삽입
INSERT INTO customers (name, email)
VALUES ('Alice', 'alice@example.com'),
       ('Bob', 'bob@example.com');
```

### 데이터 조회(Read)

```sql
-- 모든 고객 정보 조회
SELECT * FROM customers;

-- 특정 고객 조회
SELECT * FROM customers WHERE name = 'Alice';
```

### 데이터 수정(Update)

```sql
-- 'Alice' 고객의 이메일을 업데이트
UPDATE customers
SET email = 'alice.new@example.com'
WHERE name = 'Alice';
```

### 데이터 삭제(Delete)

```sql
-- 'Bob' 고객 데이터 삭제
DELETE FROM customers WHERE name = 'Bob';
```
