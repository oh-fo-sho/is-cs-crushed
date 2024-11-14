## Gradle

Gradle은 스프링 프로젝트에서 빌드, 외부 라이브러리 의존성 관리, 배포 등을 자동화 하고 효율적으로 수행하는 빌드 도구이다. `Gradle Wrapper` 는 Gradle 버전 관리를 쉽게 하기 위한 스크립트로 Gradle를 설치하지 않아도 프로젝트가 지정한 Gradle 버전으로 빌드를 수행하도록 돕는다

1. **Gradle 역할**
   1. 빌드 관리
      1. 소스 코드 컴파일, 테스트 코드 실행, 패키징 등 수행
   2. 의존성 관리
      1. `build.gradle` 파일에 정의된 외부 라이브러리나 플러그인 의존성을 중상 저장소 (Maven Repository, JCenter)에서 자동으로 다운로드하고 클래스패스에 추가함
   3. 배포
      1. 배포 가능한 형태(jar, war)로 패키징 하여 배포 준비
2. **Gradle Wrapper의 역할**
   1. Gradle Wrapper는 Gradle 버전을 프로젝트 단위로 관리할 수 있게 하며 프로젝트와 함께 제공됨
   2. Wrapper는 `gradlew` 혹은 `gradlew.bat` 과 같은 스크립트 파일과 `gradle-wrapper.properties` 설정 파일로 구성된다
   3. 버전 관리
      1. `gradle-wrapper.properties`에서 특정 버전을 지정하면, 해당 버전이 자동으로 다운로드되어 일관된 버전으로 빌드를 실행
   4. 빌드 실행
      1. 개발 환경에 Gradle이 설치되어 있지 않더라도 프로젝트에서 Wrapper 파일을 통해 Gradle 빌드를 실행
3. **Gradle 사용 이유**
   1. 프로젝트에 필요한 **특정 Gradle 버전**을 고정할 수 있어 개발 환경에 관계없이 동일한 빌드 결과를 얻을 수 있음
   2. **의존성 관리**가 수월해지고, `build.gradle`에 의존성을 추가하면 자동으로 필요한 라이브러리를 추가해줌
   3. **빌드 및 배포 작업을 자동화**할 수 있어 CI/CD 환경에서 일관된 빌드와 배포를 지원에 용이함
