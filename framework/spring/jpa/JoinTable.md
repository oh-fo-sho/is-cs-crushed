## **`@JoinTable` (다대다 관계에서 사용)**

- **설명**
  `@JoinTable`은 **다대다 관계**에서 **중간 테이블**을 생성하는 데 사용된다
  이 어노테이션은 두 테이블 간의 관계를 관리할 **중간 테이블을 생성**하고, 그 테이블의 외래 키를 정의한다
  - 예시
    ```java
    @ManyToMany
    @JoinTable(
        name = "member_project",
        joinColumns = @JoinColumn(name = "member_id"),
        inverseJoinColumns = @JoinColumn(name = "project_id")
    )
    ```
  - 이 예시는 `Member`와 `Project` 간의 다대다 관계에서, `member_project`라는 중간 테이블을 생성하고 각 테이블의 외래 키를 `member_id`, `project_id`로 설정한다
- **다대다 관계**에서 `@JoinTable`은 **단방향** 또는 **양방향**으로 설정할 수 있다
- **양방향**일 경우, 중간 테이블의 외래 키를 두 개 정의하여 두 테이블 간의 관계를 정의한다
- 하지만 대부분의 경우 사용하지 않는게 좋다
  - 개발자가 직접 관리할 수 없는 중간테이블 때문
