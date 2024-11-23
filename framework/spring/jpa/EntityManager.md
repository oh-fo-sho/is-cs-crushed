## 엔티티 매니저 (EntityManager)

- EntityMangaerFactory가 생성하는 객체
- 애플리케이션 로직에서 엔티티와 데이터베이스 간 작업 담당
- 역할
  - SQL 쿼리 생성, 실행
  - 영속성 컨텍스트(Persistence Context) 관리
  - 트랜잭션 단위로 작업 수행

**특징**

- `EntityManager` 는 스레드에 안전하지 않는다. 여러 스레드에서 공유해서는 안됨
- 사용후 반드시 닫아야한다
- 각 `EntityManager` 는 **독립적인 영속성 컨텍스트**를 보유
