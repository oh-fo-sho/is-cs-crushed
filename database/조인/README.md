# 조인 (JOIN)

조인은 SQL에서 두 개 이상의 테이블을 결합하여 데이터를 조회하는 데 사용되는 연산이다. 조인을 사용하면 여러 테이블에 분산된 데이터를 하나의 결과로 결합할 수 있다. 조인의 기본적인 목적은 관련이 있는 데이터들을 연결하여 한 번에 조회할 수 있게 하는 것이다.

## 조인의 종류

### INNER JOIN

- 두 테이블에서 조건에 맞는 데이터를 모두 찾아서 결합한다
- 두 테이블 모두에서 일치하는 데이터만 결과로 반환한다
- 집합에서의 교집합을 의미한다

### LEFT JOIN (LEFT OUTER JOIN)

- 왼쪽 테이블의 모든 데이터를 반환하고 오른쪽 테이블에서 일치하는 데이터가 있다면 결합한다
- 만약 오른쪽 테이블에 일치하는 데이터가 없다면 `NULL` 값으로 반환된다

### RIGHT JOIN (RIGHT OUTER JOIN)

- 오른쪽 테이블의 모든 데이터를 반환하고 왼쪽 테이블에서 일치하는 데이터가 있다면 결합한다
- 왼쪽 테이블에 일치하는 데이터가 없다면 `NULL` 값으로 반환된다

### FULL JOIN (FULL OUTER JOIN)

- 두 테이블에서 일치하는 데이터를 결합한다
- 일치하지 않는 데이터는 `NULL` 값으로 반환한다
- 왼쪽 테이블과 오른쪽 테이블의 모든 데이터를 포함한다
- 집합에서의 합집합을 의미한다

## 조인 예시

### **employees 테이블**

| employee_id | first_name | last_name | department_id |
| ----------- | ---------- | --------- | ------------- |
| 1           | John       | Doe       | 101           |
| 2           | Jane       | Smith     | 102           |
| 3           | Alice      | Brown     | 101           |
| 4           | Bob        | White     | NULL          |

### **departments 테이블**

| department_id | department_name |
| ------------- | --------------- |
| 101           | HR              |
| 102           | IT              |
| 103           | Marketing       |

이러한 형태의 테이블 2개가 있다고 가정해보자

1. INNER JOIN

   - `INNER JOIN` 은 두 테이블에서 일치하는 데이터만 반환한다

   ```sql
   SELECT employees.first_name, employees.last_name, departments.department_name
   FROM employees
   INNER JOIN departments
   ON employees.department_id = departments.department_id;
   ```

   - 결과

   | first_name | last_name | department_name |
   | ---------- | --------- | --------------- |
   | John       | Doe       | HR              |
   | Jane       | Smith     | IT              |
   | Alice      | Brown     | HR              |

   해당 쿼리는 `employess` 와 `departments` 테이블에서 `department_id` 가 일치하는 로우만 가져옵니다

2. LEFT JOIN

   - `LEFT JOIN`은 왼쪽 테이블(`employees`)의 모든 레코드를 반환하고, 오른쪽 테이블(`departments`)에서 일치하는 데이터가 없으면 `NULL`로 반환한다

   ```sql
   SELECT employees.first_name, employees.last_name, departments.department_name
   FROM employees
   LEFT JOIN departments
   ON employees.department_id = departments.department_id;
   ```

   - 결과

   | first_name | last_name | department_name |
   | ---------- | --------- | --------------- |
   | John       | Doe       | HR              |
   | Jane       | Smith     | IT              |
   | Alice      | Brown     | HR              |
   | Bob        | White     | NULL            |

   위 예시에서는 `departments` 테이블에 일치하는 `department_id`가 없으면 `NULL`이 반환되는데 Bob 이 해당되는 부서가 없기 때문에 null 로 반환된다

3. FULL JOIN

   - `FULL OUTER JOIN`은 두 테이블에서 일치하는 데이터를 결합하고, 일치하지 않는 데이터는 `NULL`로 반환한다

   ```sql
   SELECT employees.first_name, employees.last_name, departments.department_name
   FROM employees
   FULL OUTER JOIN departments
   ON employees.department_id = departments.department_id;
   ```

   - 결과

   | first_name | last_name | department_name |
   | ---------- | --------- | --------------- |
   | John       | Doe       | HR              |
   | Jane       | Smith     | IT              |
   | Alice      | Brown     | HR              |
   | Bob        | White     | NULL            |
   | NULL       | NULL      | Sales           |

   위 예시에서 `FULL OUTER JOIN`은 양쪽 테이블의 모든 레코드를 반환하며, 일치하는 항목이 없으면 `NULL`을 출력한다. Bob은 부서가 없으니 `department_name` 에 null이 반환되고 `Sales` 부서는 `employees` 테이블에 직원이 없으므로 `NULL` 값이 출력된다
