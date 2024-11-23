---
description: 아래 기본 명령어들에 덧붙여 추가적으로 docker-compose와 같은 도구를 함께 사용하면 복잡한 환경도 더 쉽게 관리할 수 있다!
---

# 🧙‍♀️ Docker: Commands

## Docker Commands

### Check Docker Version and Status

* **`docker version`**\
  도커의 클라이언트와 서버 버전을 확인한다.
* **`docker info`**\
  도커 설치 상태 및 시스템 정보(이미지 개수, 컨테이너 개수 등)를 확인한다.

### Manage Docker Images

* **`docker images`**\
  로컬에 저장된 도커 이미지를 리스트로 보여준다.
*   **`docker pull <이미지 이름>`**\
    Docker Hub 등 레지스트리에서 이미지를 다운로드한다.

    ```bash
    bash코드 복사docker pull nginx:latest
    ```
*   **`docker build -t <이미지 이름> .`**\
    현재 디렉토리의 `Dockerfile`을 기반으로 이미지를 생성한다.

    ```bash
    bash코드 복사docker build -t myapp:1.0 .
    ```

### Manage Docker Containers

* **`docker ps`**\
  실행 중인 컨테이너를 리스트로 보여준다.
* **`docker ps -a`**\
  중지된 컨테이너를 포함한 모든 컨테이너를 보여준다.
*   **`docker run -it <이미지 이름>`**\
    새로운 컨테이너를 생성하고 상호작용 모드로 실행한다.

    ```bash
    docker run -it ubuntu:latest /bin/bash
    ```
*   **`docker stop <컨테이너 ID>`**\
    실행 중인 컨테이너를 중지한다.

    ```bash
    docker stop 123abc
    ```
*   **`docker rm <컨테이너 ID>`**\
    중지된 컨테이너를 삭제한다.

    ```bash
    docker rm 123abc
    ```
*   **`docker logs <컨테이너 ID>`**\
    특정 컨테이너의 로그를 확인한다.

    ```bash
    docker logs 123abc
    ```

### Remove Docker Images

*   **`docker rmi <이미지 ID>`**\
    특정 이미지를 삭제한다.

    ```bash
    docker rmi nginx:latest
    ```
* **`docker rmi $(docker images -q)`**\
  로컬에 저장된 모든 이미지를 삭제한다.

### Access Running Containers

*   **`docker exec -it <컨테이너 ID> /bin/bash`**\
    실행 중인 컨테이너에 접근하여 명령어를 실행한다.

    ```bash
    docker exec -it 123abc /bin/bash
    ```

### Manage Docker Networks

* **`docker network ls`**\
  도커 네트워크 리스트를 확인한다.
*   **`docker network create <네트워크 이름>`**\
    새로운 사용자 정의 네트워크를 생성한다.

    ```bash
    docker network create my_network
    ```
*   **`docker network connect <네트워크 이름> <컨테이너 ID>`**\
    컨테이너를 특정 네트워크에 연결한다.

    ```bash
    docker network connect my_network 123abc
    ```

### Manage Docker Volumes

* **`docker volume ls`**\
  도커 볼륨 리스트를 확인한다.
*   **`docker volume create <볼륨 이름>`**\
    새로운 볼륨을 생성한다.

    ```bash
    docker volume create my_volume
    ```
*   **`docker run -v <볼륨 이름>:<컨테이너 내 경로> <이미지 이름>`**\
    컨테이너 실행 시 볼륨을 마운트한다.

    ```bash
    docker run -v my_volume:/data nginx:latest
    ```

### Optimize Docker Images

*   **`docker image prune`**\
    사용하지 않는 이미지를 삭제한다.

    ```bash
    docker image prune
    ```
*   **`docker system prune`**\
    사용하지 않는 컨테이너, 네트워크, 볼륨, 이미지를 모두 삭제한다.

    ```bash
    docker system prune -a
    ```

### Set Container Restart Policies

*   **`docker run --restart=<정책>`**\
    컨테이너가 실패했을 때 자동 재시작 정책을 설정한다.

    * `no`: 자동 재시작 없음 (기본값).
    * `on-failure`: 오류 발생 시 재시작.
    * `always`: 항상 재시작.

    ```bash
    docker run --restart=always nginx:latest
    ```

### Work with Docker Registries

*   **`docker tag <이미지 이름> <레지스트리 주소>`**\
    로컬 이미지를 특정 레지스트리에 태깅한다.

    ```bash
    docker tag myapp:1.0 myregistry.com/myapp:1.0
    ```
*   **`docker push <레지스트리 주소>`**\
    태깅된 이미지를 레지스트리에 업로드한다.

    ```bash
    docker push myregistry.com/myapp:1.0
    ```
* **`docker login`**\
  Docker Hub 또는 다른 레지스트리에 로그인한다.

### Docker Compose Commands

*   **`docker-compose up`**\
    `docker-compose.yml` 파일을 기준으로 여러 컨테이너를 동시에 실행한다.

    ```bash
    docker-compose up -d
    ```
*   **`docker-compose down`**\
    실행 중인 모든 서비스를 중지하고 네트워크와 볼륨을 정리한다.

    ```bash
    docker-compose down
    ```
