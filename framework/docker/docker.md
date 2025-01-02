# Docker

<aside>
💡

https://docs.docker.com/

</aside>

Docker는 **컨테이너(Container) 기반 가상화 플랫폼**으로, 애플리케이션과 실행 환경을 하나의 단위로 패키징하여 어디서나 실행 가능한 환경을 제공한다. Docker는 **리눅스 컨테이너 기술(LXC)**을 기반으로 경량화된 가상화를 구현하며, 클라우드 네이티브 애플리케이션 개발과 배포에 필수적인 도구로 자리매김 하였다

## Docker Engine

Docker의 핵심 구성 요소로, 컨테이너의 생성, 실행, 이미지 빌드, 네트워크 및 스토리지 관리 등 모든 작업을 담당

### **1. 도커 엔진 구성요소**

1. **Docker Daemon**
   - 백그라운드에서 실행되며, 컨테이너 생성, 이미지 빌드 등의 요청을 처리
   - REST API를 통해 CLI와 통신
2. **Docker CLI**
   - Docker와 상호작용하는 명령줄 도구(Command Line Interface)
   - 예: `docker run`, `docker build`, `docker stop` 등
3. **REST API**
   - Docker Daemon과 통신하는 HTTP 기반 인터페이스
   - Python SDK, Postman 등을 통해 직접 요청 가능(제한을 풀어줬을 경우)

### **2. Docker Engine의 주요 역할**

### **2-1. 컨테이너 관리**

- **컨테이너 생성 및 실행**
  - Docker CLI 명령을 통해 컨테이너를 생성하고 실행
  - Docker Daemon이 컨테이너의 모든 상태를 관리
- **컨테이너 상태 변경**
  - 시작, 중지, 삭제, 재시작 등의 작업을 수행

### **2-2. 이미지 관리**

- **이미지 빌드**
  - Dockerfile을 기반으로 이미지를 빌드
- **이미지 저장 및 배포**
  - 이미지를 로컬 저장소에 저장하거나 Docker Hub와 같은 레지스트리에 업로드

### **2-3. 네트워크 관리**

- 컨테이너 간 통신을 위한 네트워크 생성 및 관리
- 기본 브리지 네트워크(`docker0`) 및 사용자 정의 네트워크 생성 가능

### **2-4. 데이터 스토리지 관리**

- Docker 볼륨을 생성 및 관리하여 컨테이너 데이터의 영속성을 보장

### **2-5. 리소스 관리**

- CPU, 메모리, 네트워크 대역폭 등의 자원 제한 설정(Cgroups 활용)

## Docker Image

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/e8f11927-b70c-4524-9227-a3efac08e7aa/9df04d61-e6dc-42ad-812f-6dbca0b5afcf/image.png)

컨테이너의 실행 환경을 정의하는 읽기 전용 템플릿으로, 애플리케이션과 그 실행에 필요한 모든 파일과 설정을 포함

- **특징**
  - **계층 구조(Layered Structure)**
    - 변경 사항만 새로운 계층으로 추가하여 효율적으로 관리
  - **불변성(Immutable)**
    - 읽기 전용으로 상태값을 가지지 않음
      - 삭제 후 다시 실행시 변경된 데이터가 반영되지 않음
- **작업 방식**
  - Dockerfile(스크립트)로 작성하여 빌드 → Docker Image 생성
  - 예: `docker build` 명령으로 이미지 생성
- **관리**
  - Docker Hub 및 프라이빗 레지스트리를 통해 이미지 공유 및 배포

### **1. 계층의 기본 개념**

- **불변성(Immutable)**
  - 각 계층은 **읽기 전용**이며 변경 불가
  - 새로운 변경 사항이 생기면 기존 계층을 수정하지 않고, **새로운 계층이 추가됨**
    - 레이어 형태로 쌓이기만함
    - 빌드 할 경우 새로 쌓이는 부분만 빌드하기 때문에 빠름
- **저장 방식**
  - Docker는 계층을 파일 시스템 스냅샷처럼 저장하며, 필요한 경우 계층을 재사용
  - 동일한 베이스 레이어를 사용하는 여러 이미지가 있을 경우, **한 번 다운로드된 레이어를 재사용**

### **2. Docker 이미지 계층 구조**

### **2-1. Base Layer (베이스 레이어)**

- **정의**
  - Docker 이미지의 기본 계층으로, 운영체제 또는 런타임 환경을 제공
  - 예를 들어, `ubuntu`, `alpine`, `debian` 등과 같은 기본 운영체제 이미지가 될 수 있다
- **특징**:
  - 다른 모든 계층의 기반이 되는 레이어
    - 대부분 [`docker.io`](http://docker.io) `docker hub` 에서 공식이미지를 사용
  - 대부분의 Dockerfile에서 `FROM` 키워드로 지정
    ```
    FROM ubuntu:20.04
    ```
  - 이미지를 빌드할 때 반드시 하나의 베이스 레이어가 필요함
- **장점**
  - 동일한 베이스 레이어를 공유하는 여러 이미지는 이를 재사용하기 때문에 저장 공간과 빌드 시간을 절약

### **2-2. Image Layer (이미지 레이어)**

- **정의**
  - 베이스 레이어 위에 추가된 계층으로, 애플리케이션 실행에 필요한 라이브러리, 의존성, 설정 파일 등을 포함합
  - Dockerfile의 명령어(`RUN`, `COPY`, `ADD` 등)로 생성
- **특징**
  - 각 명령어는 새로운 레이어를 생성하며, 계층은 **순서대로 쌓인다**
    ```
    FROM ubuntu:20.04
    RUN apt-get update && apt-get install -y python3
    COPY . /app
    ```
  - **계층화**된 구조 덕분에 이전 계층이 변경되지 않는 한, **캐시된 결과를 재사용**하여 빌드 속도를 높인다
  - 예를 들어, 위의 Dockerfile에서 `COPY . /app` 단계만 변경된다면, 이전 단계(`RUN apt-get ...`)는 재실행되지 않음
- **역할**
  - 애플리케이션 코드와 실행에 필요한 모든 설정 및 의존성을 포함

### **2-3. Container Layer (컨테이너 레이어)**

- **정의**
  - 컨테이너가 실행될 때 생성되는 **읽기/쓰기 가능한 계층**
  - 컨테이너의 동작 중 발생하는 데이터 변경 사항이 저장
- **특징**
  - 이미지 레이어 위에 생성되며, 컨테이너 실행 중의 모든 상태 변화(예: 파일 생성, 수정 등)는 이 계층에 기록
  - 컨테이너가 삭제되면 컨테이너 레이어도 함께 삭제
- **역할**
  - 컨테이너 실행 중의 동적 데이터 저장
  - 로그 파일, 애플리케이션이 생성한 임시 파일 등이 이 계층에 기록됨
- **영구 데이터**
  - 컨테이너 레이어는 컨테이너 삭제 시 사라지므로, **도커 볼륨**을 사용하여 데이터를 영구적으로 저장해야 함

### **3. 계층의 동작 방식**

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/e8f11927-b70c-4524-9227-a3efac08e7aa/42f22a0a-549b-4a0d-a93d-1c86dd793cbc/image.png)

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/e8f11927-b70c-4524-9227-a3efac08e7aa/b25952d5-ef41-4376-a56d-3436208f3d56/image.png)

1. **Dockerfile로 이미지 생성**
   - Dockerfile의 각 명령어는 새로운 **이미지 레이어**를 생성
   - 이전 레이어를 기반으로 명령어를 실행한 결과를 저장
2. **캐싱과 효율성**
   - 이미지 빌드 시, Docker는 이전에 생성된 레이어를 캐싱하여 재사용
   - 변경된 단계 이후의 레이어만 다시 생성하므로 빌드 속도가 빠르다
3. **컨테이너 실행**
   - 이미지 레이어를 기반으로 **읽기/쓰기 가능한 컨테이너 레이어**를 추가하여 실행 → 컨테이너 레이어
   - 컨테이너 레이어는 일시적이며, 컨테이너가 삭제되면 함께 삭제

## Docker Container

Docker Image로부터 생성된 실행 환경. 독립적인 파일 시스템과 네트워크 공간을 가지며, 애플리케이션을 실행하는 단위

### **1. Docker 컨테이너의 특징**

### **1-1. 독립성과 격리성**

- **독립적인 실행 환경**
  - 컨테이너는 애플리케이션과 그 실행 환경(코드, 라이브러리, 설정 등)을 호스트 시스템과 격리하여 실행
  - 컨테이너 간의 네트워크, 파일 시스템, 프로세스 등이 독립적으로 분리되어 다른 컨테이너와 영향을 주고받지 앟ㄴ는다
- **커널 공유**
  - 컨테이너는 가상머신과 달리 별도의 게스트 운영체제를 실행하지 않고, 호스트 운영체제의 커널을 공유
  - 이를 통해 가상머신에 비해 경량화되고 실행 속도가 빠름

### **1-2. 경량성**

- **리소스 효율성**
  - 컨테이너는 가상머신에 비해 훨씬 적은 자원을 필요로함
  - 컨테이너 간에 커널을 공유하며 **프로세스 수준에서 격리됨**
    - 컨테이너는 호스트 운영체제 위에서 실행되고 컨테이너 내부에서 실행되는 프로세스는 호스트의 프로세스와는 독립적인 것처럼 보인다. 하지만 실제로 호스트의 프로세스 목록에 포함됨.
    - 이는 프로세스가 컨테이너 내부에서 독립적으로 실행됨을 의미
    - 리소스 제한 및 격리
      - CPU, 메모리, 네트워크 인터페이스 등 컨테이너에 할당된 자원은 호스트의 다른 컨테이너 또는 프로세스와 독립적으로 사용됨
- **빠른 실행**
  - 컨테이너는 수 초 이내로 실행 가능
  - 이는 별도의 운영체제를 부팅할 필요가 없기 때문이다

### **1-3. 이식성과 일관성**

- **Build Once, Run Anywhere**
  - 도커의 철학에 따라 한 번 빌드된 컨테이너 이미지는 어떤 환경에서도 동일하게 실행된다
  - 이는 개발 환경과 배포 환경 간의 차이를 최소화시켜 배포를 용이하게 함
- **플랫폼 독립성**
  - 도커 이미지는 호스트 운영체제에 무관하게 실행 가능
  - 도커는 깔려야함
    - JVM과 비슷한 특성

### **1-4. 데이터 비영속성**

- **컨테이너 레이어**
  - 컨테이너 실행 시 생성되는 쓰기 가능한 계층은 일시적
  - 컨테이너 삭제 시 이 계층의 데이터는 함께 삭제
- **데이터 영속성을 위한 볼륨 사용**
  - 영구 데이터를 저장하기 위해 도커 볼륨 또는 바인드 마운트를 사용

### **1-5. 네트워크 격리**

- **기본 네트워크 격리**
  - 컨테이너는 기본적으로 외부 네트워크와 격리된 상태로 실행
  - 네트워크 설정을 통해 컨테이너 간 통신 및 외부 통신 가능

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/e8f11927-b70c-4524-9227-a3efac08e7aa/d8024e5b-6d6d-490d-bac6-429f30232dd9/image.png)

### **2. Docker 컨테이너의 생명주기**

### **2-1. 생성 (Create)**

- **명령어**: `docker create`
  - 도커 이미지를 기반으로 컨테이너를 생성하지만, 실행하지 않음
  - 생성된 컨테이너는 준비 상태에서 실행 대기
- **예**
  ```bash
  docker create --name my-container ubuntu:latest
  ```

### **2-2. 시작 (Start)**

- **명령어** : `docker start`
  - 생성된 컨테이너를 실행
- **예**
  ```bash
  docker start my-container
  ```

### **2-3. 실행 (Run)**

- **명령어** : `docker run`
  - 컨테이너를 생성하고, 시작하며, 실행까지 한 번에 수행
  - 가장 일반적으로 사용되는 명령
- **예**
  ```bash
  docker run --name my-container ubuntu:latest
  ```

### **2-4. 정지 (Stop)**

- **명령어** : `docker stop`
  - 실행 중인 컨테이너를 정지
  - 애플리케이션 실행 중지 및 상태 저장
- **예**
  ```bash
  docker stop my-container
  ```

### **2-5. 재시작 (Restart)**

- **명령어** : `docker restart`
  - 정지된 컨테이너를 다시 시작
  - 컨테이너 내부 상태는 초기화되지 않음
- **예**
  ```bash
  docker restart my-container
  ```

### **2-6. 삭제 (Remove)**

- **명령어** : `docker rm`
  - 컨테이너를 삭제
  - 삭제 시 컨테이너 레이어에 저장된 모든 데이터도 함께 삭제
- **예**
  ```bash
  docker rm my-container
  ```

### **2-7. 중단된 상태 확인 및 재사용**

- **컨테이너 상태 확인** : `docker ps -a`
  - 정지된 컨테이너를 포함한 모든 컨테이너 목록 조회
  - 실행중인 컨테이너만 확인하려면 `a` 옵션 빼면 됨
- **정지된 컨테이너 재사용**
  - 이미 생성된 컨테이너를 다시 실행할 수 있음
  ```bash
  docker start my-container
  ```

## Docker Network

Docker 네트워크는 **컨테이너 간의 통신**과 **외부 네트워크와의 연결**을 지원하는 Docker의 중요한 기능 중 하나다. 기본적으로 Docker는 격리된 환경에서 컨테이너를 실행하지만, 필요에 따라 컨테이너 간의 통신 및 외부와의 연결을 가능하게 한다

### **1. Docker 네트워크의 기본 개념**

- **격리된 네트워크 환경**
  - 기본적으로 컨테이너는 서로 격리된 네트워크 환경에서 동작
  - 연결되지 않은 컨테이너 간에는 통신이 불가능함
- **네트워크 드라이버**
  - Docker는 다양한 네트워크 유형(드라이버)을 제공하며, 사용자는 상황에 따라 적절한 네트워크를 선택할 수 있음
- **IP 주소와 서브넷**
  - Docker는 컨테이너에 고유한 **IP 주소**를 할당
  - Docker 네트워크는 서브넷팅을 통해 IP 대역을 관리하며, 기본적으로 `/16` 서브넷을 사용

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/e8f11927-b70c-4524-9227-a3efac08e7aa/32dc76d6-5565-4f9a-bc03-08df06634036/image.png)

### **2. 기본 네트워크 드라이버**

### **2-1. Bridge Network**

- **설명**
  - Docker의 기본 네트워크로, 컨테이너 간 통신을 지원함
  - 동일한 브리지 네트워크에 연결된 컨테이너끼리는 통신 가능함
  - 컨테이너는 NAT(Network Address Translation)를 통해 외부 네트워크와 통신함
    - 내부적으로 가상 NAT
- **특징**
  - 기본적으로 생성되는 **docker0 브릿지 네트워크를 사용**
  - 호스트 네트워크와는 별도의 네트워크 환경 제공
  - IP 대역: 기본적으로 `172.17.0.0/16`
- **서브넷팅**
  - Docker는 브릿지 네트워크 생성 시 기본 서브넷을 자동으로 할당함
  - 필요하면 사용자 지정 서브넷도 설정 가능하다고 한다.
  - 예
    ```bash
    docker network create \
      --subnet=192.168.1.0/24 \
      my-custom-bridge
    ```
- **사용 예시**
  ```bash
  docker network connect bridge my-container
  ```

### **2-2. Host Network**

- **설명**
  - 컨테이너가 호스트 머신의 네트워크 스택을 공유
  - 컨테이너는 호스트의 IP 주소를 사용하며, 포트 매핑 없이 외부 네트워크와 통신
- **특징**
  - 네트워크 격리가 없으므로 성능이 가장 뛰어남
  - 컨테이너의 네트워크 설정이 호스트와 동일
  - 보안상 안 좋음
- **사용 예시**
  ```bash
  docker run --network host nginx
  ```

### **2-3. None Network**

- **설명**
  - 네트워크를 사용하지 않는 고립된 컨테이너를 생성
  - 외부 네트워크와의 연결이 불가능하며 컨테이너 간 통신도 불가능 → 고립
- **특징**
  - 테스트 환경이나 특정 목적의 애플리케이션 실행 시 사용
- **사용 예시**
  ```bash
  docker run --network none busybox
  ```

### **2-4. Overlay Network**

- **설명**
  - Docker Swarm이나 Kubernetes와 같은 **멀티 호스트 환경**에서 사용
  - **여러 호스트**에 있는 컨테이너 간 통신을 지원
- **특징**
  - VXLAN(Virtual Extensible LAN) 기술을 사용하여 가상 네트워크를 생성
  - 네트워크는 Docker 클러스터 내의 모든 노드에 걸쳐 확장 가능
- **서브넷팅**
  - Overlay 네트워크도 서브넷 대역을 지정 가능
  - 예: `192.168.0.0/16`
- **사용 예시**
  ```bash
  docker network create \
    --driver overlay \
    --subnet=192.168.2.0/24 \
    my-overlay-network
  ```

### **3. Docker 네트워크 구성**

![image.png](https://prod-files-secure.s3.us-west-2.amazonaws.com/e8f11927-b70c-4524-9227-a3efac08e7aa/c5f54d44-72af-45de-8eb6-94fc8bbde992/image.png)

### **3-1. 기본 Bridge 네트워크의 내부 구성**

1. **docker0**
   - Docker 데몬이 기본적으로 생성하는 브릿지 네트워크
   - 호스트 머신과 컨테이너 간 통신을 위한 가상의 이더넷 인터페이스
   - 컨테이너에 가상 네트워크 인터페이스(VETH)를 생성하고, 이 인터페이스를 docker0와 연결
2. **가상 이더넷 인터페이스 (VETH)**
   - 각 컨테이너는 고유의 이더넷 인터페이스(예: eth0)를 가집니다
   - VETH는 컨테이너 내부의 eth0와 호스트의 docker0를 연결
3. **NAT(Network Address Translation)**
   - 컨테이너는 `172.x.x.x`와 같은 내부 IP를 사용하며, NAT를 통해 외부 네트워크와 통신
4. **서브넷팅**
   - 기본적으로 Docker는 `172.17.0.0/16` 대역을 사용
   - 여러 브릿지 네트워크를 생성하면 각각 다른 서브넷을 자동으로 할당

### **3-2. 네트워크 연결 흐름**

1. 컨테이너 내부
   - 컨테이너는 자체 IP와 eth0 네트워크 인터페이스를 가짐
   - 로컬 통신은 loopback 인터페이스(lo)를 사용함
2. 호스트 네트워크
   - VETH를 통해 docker0에 연결함
     - 이더넷도 가상화하여 보유함
   - docker0은 NAT를 통해 호스트 머신의 실제 네트워크 인터페이스(예: eth0)와 통신함

### **4. Docker 네트워크 서브넷팅**

- Docker는 네트워크 생성 시 기본적으로 서브넷 대역을 자동 할당하며 사용자 지정도 가능
- **예시**
  ```bash
  docker network create \
    --driver bridge \
    --subnet=192.168.10.0/24 \
    my-custom-network
  ```
- **서브넷팅 동작**
  - 서브넷 대역 안에서 컨테이너에 IP 주소가 순차적으로 할당
  - 기본 Bridge 네트워크의 서브넷 대역: `172.17.0.0/16`
  - Overlay 네트워크 등에서도 사용자 지정 서브넷 대역을 사용할 수 있음
  - **네트워크 설정을 변경하면 서브넷팅도 다시 이루어지기 때문에 IP 주소가 바뀜**

### **5. Docker 네트워크 관련 명령어**

### **5-1. 네트워크 생성**

- Bridge 네트워크 생성
  ```bash
  docker network create \
    --driver bridge \
    --subnet=192.168.1.0/24 \
    my-bridge-network
  ```

### **5-2. 네트워크 목록 확인**

- 현재 생성된 네트워크 확인
  ```bash
  docker network ls
  ```
- 네트워크 상세 확인
  ```bash
  docker network inspect my-bridge-network
  ```

### **5-3. 네트워크 연결**

- 컨테이너를 기존 네트워크에 연결
  ```bash
  docker network connect my-bridge-network my-container
  ```

### **5-4. 네트워크 삭제**

- 네트워크 삭제
  ```bash
  docker network rm my-bridge-network
  ```

## Docker Volume

Docker 볼륨은 컨테이너의 데이터를 영구적으로 저장하고 관리하는 기능이다. 기본적으로 컨테이너의 데이터는 컨테이너 삭제 시 함께 삭제되지만, 볼륨을 사용하면 데이터를 유지할 수 있다. 볼륨은 호스트 시스템의 디렉토리와 컨테이너 간 데이터를 공유하거나, 여러 컨테이너 간 데이터를 공유하는 데 사용된다

### **1. Docker Volume의 주요 특징**

1. **데이터 영속성**
   - 컨테이너가 삭제되더라도 볼륨에 저장된 데이터는 유지됨
2. **컨테이너 간 데이터 공유**
   - 여러 컨테이너가 동일한 볼륨에 접근하여 데이터를 공유할 수 있음
3. **호스트-컨테이너 데이터 공유**
   - 호스트 시스템과 컨테이너 간 파일을 공유할 수 있음
4. **보안**
   - 기본적으로 Docker 볼륨은 다른 사용자에게 접근이 제한되며, 컨테이너에 의해 관리됨

### **2. Docker Volume의 종류**

1. **익명 볼륨**
   - 컨테이너 실행 시 자동 생성되는 이름 없는 볼륨
   - 예: `docker run -v /data mysql`
2. **이름 있는 볼륨**
   - 사용자가 명시적으로 생성한 볼륨
   - 예: `docker volume create my-volume`
3. **바인드 마운트**
   - 호스트 시스템의 특정 디렉토리를 컨테이너와 공유
   - 예: `docker run -v /host/path:/container/path mysql`
   - **왜 볼륨을 마운트한다고 표현할까?**
     - "마운트(Mount)"라는 용어는 **운영체제와 파일 시스템에서 사용되는 개념**에서 유래되었다고 한다. Docker 볼륨에서 "마운트"를 사용한다는 표현은, 호스트 파일 시스템의 특정 영역(디렉토리 또는 파일)을 **컨테이너의 파일 시스템에 연결**하는 것을 의미함 이를 통해 호스트와 컨테이너 간 데이터를 공유하거나 데이터를 영구적으로 저장할 수 있다

### **3. Docker Volume의 사용**

### **3-1. 볼륨 생성**

```bash
docker volume create my-volume
```

### **3-2. 컨테이너 실행 시 볼륨 연결**

컨테이너와 볼륨을 연결하려면 `-v` 또는 `--mount` 옵션을 사용

```bash
docker run -d \
  --name mysql-container \
  -e MYSQL_ROOT_PASSWORD=my-secret-pw \
  -v my-volume:/var/lib/mysql \
  mysql:latest
```

- `v my-volume:/var/lib/mysql`
  - `my-volume`: 호스트의 Docker 볼륨 이름
  - `/var/lib/mysql`: 컨테이너 내 MySQL 데이터가 저장되는 디렉토리

### **4. MySQL 데이터베이스 예제**

### **4-1. Docker를 이용한 MySQL 데이터베이스 컨테이너 실행**

MySQL 데이터베이스는 `/var/lib/mysql` 디렉토리에 데이터를 저장함

컨테이너를 삭제하더라도 데이터를 유지하려면 Docker 볼륨을 사용해야 한다

1. **볼륨 생성**:

   ```bash
   docker volume create mysql-data
   ```

2. **MySQL 컨테이너 실행**

   ```bash
   docker run -d \
     --name mysql-container \
     -e MYSQL_ROOT_PASSWORD=rootpassword \
     -v mysql-data:/var/lib/mysql \
     mysql:latest
   ```

   - `e MYSQL_ROOT_PASSWORD=rootpassword`: MySQL 루트 계정 비밀번호 설정
   - `v mysql-data:/var/lib/mysql`: MySQL 데이터 저장소를 `mysql-data` 볼륨에 마운트

3. **MySQL 데이터베이스 확인**
   - 컨테이너 내부에 접속하여 데이터베이스 확인
     ```bash
     docker exec -it mysql-container mysql -uroot -prootpassword
     ```
   - 데이터베이스를 생성 및 수정하고 컨테이너를 삭제해도 데이터는 유지됨

### **4-2. 컨테이너 삭제 후 데이터 유지 확인**

1. **MySQL 컨테이너 삭제**

   ```bash
   docker rm -f mysql-container
   ```

2. **새로운 컨테이너 실행**

   - 동일한 볼륨을 연결하여 실행

   ```bash
   docker run -d \
     --name mysql-container \
     -e MYSQL_ROOT_PASSWORD=rootpassword \
     -v mysql-data:/var/lib/mysql \
     mysql:latest
   ```

3. **데이터 유지 확인**
   - 새 컨테이너에서 MySQL 접속 후 데이터베이스 확인
     ```bash
     docker exec -it mysql-container mysql -uroot -prootpassword
     SHOW DATABASES;
     ```

## 5. Docker 볼륨과 컨테이너 레이어와의 관계

- 컨테이너 레이어
  - Docker 이미지를 기반으로 생성, 컨테이너 실행 중 발생하는 모든 데이터 변경 사항은 기본적으로 컨테이너 레이어에 기록됨
  - 컨테이너 삭제시 같이 삭제됨
- Docker 볼륨
  - 컨테이너 외부에서 데이터를 영구적으로 저장하는 스토리지
  - 컨테이너 레이어와 마찬가지로 컨테이너 내부 경로와 연결되어 데이터 읽고 쓰기 가능

### 5-1. 동기화의 원리

**Docker 볼륨 사용시 해당 디렉토리는 기본적으로 컨테이너 레이어가 아닌 Docker 볼륨에 저장된다고 한다.**

Docker는 내부적으로 파일 시스템 마운트 기법을 사용하여 컨테이너 내부의 특정 디렉토리를 호스트 볼륨과 연결하기 때문에 가능하다

- **Docker 볼륨 마운트 시 동작 방식**
  1. Docker 볼륨이 특정 디렉토리에 마운트 될 경우 해당 디렉토리의 모든 읽기/쓰기 작업은 컨테이너 레이어가 아닌 Docker 볼륨으로 전달
  2. 이 과정에서 컨테이너 레이어는 해당 디렉토리에 대한 데이터 저장하지 않음
  3. 해당 경로는 Docker 볼륨을 사용하여 데이터를 관리, 컨테이너 레이어와 독립적으로 작동