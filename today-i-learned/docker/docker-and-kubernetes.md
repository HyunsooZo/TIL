---
description: >-
  Kubernetes(K8s)는 컨테이너화된 애플리케이션을 배포하고 확장하며 관리하는 데 사용되는 컨테이너 오케스트레이션 도구이다.
  Docker와 같은 컨테이너 런타임을 기반으로 작동하며, 복잡한 환경에서도 애플리케이션의 안정성과 확장성을 보장하는 데 도움을 준다.
  Docker와 연관이 깊어 도커 카테고리 하위에 포스팅하였다.
---

# ☸️ Docker: and Kubernetes

## Docker & Kubernetes

Docker는 애플리케이션을 컨테이너로 패키징하고 실행하는 역할을 한다. \
반면, Kubernetes는 이러한 **컨테이너를 관리하고 조율**하는 데 사용된다. \
즉, Docker가 컨테이너 단위의 환경을 제공한다면, \
Kubernetes는 이 **컨테이너들이 여러 서버(Node)에서 원활하게 실행되도록 관리**한다.

## Which environment K8s needs?

앞서 설명 했듯, K8s는 컨테이너화된 애플리케이션을 안정적이고 확장 가능하게 운영하는 데 최적화된 도구이다. \
특히 **트래픽 변동이 크거나**, **무 중단 서비스를 요구**하거나, \
**마이크로서비스 아키텍처를 사용하는 시스템**에서 가장 큰 강점을 발휘한다. \
서비스의 특성과 요구 사항에 따라 K8s를 활용하면, 운영 효율성과 개발 생산성을 크게 향상시킬 수 있다.

### **Cloud-Native Applications**

* 마이크로서비스 구조를 가진 클라우드 애플리케이션.
* 자동화된 배포와 확장이 필요한 경우 유리함.

### **Frequent Updates**

* 지속적인 배포(CI/CD)가 필요한 서비스.
* Rolling Update 및 Canary Deployment로 무중단 업데이트 가능.

### **High Availability Services**

* 장애 복구와 무중단 서비스가 중요한 시스템.
* Pod 장애 시 자동 복구(Self-Healing) 지원.

### **Services with Dynamic Traffic**

* 트래픽 변동이 큰 애플리케이션.
* HPA(Horizontal Pod Autoscaler)로 유연한 확장 지원.

### **Multi-Cloud or Hybrid Environments**

* 여러 클라우드 또는 온프레미스를 혼합한 환경.
* Kubernetes의 이식성과 유연성으로 클라우드 종속성 회피.

### **Data-Intensive Applications**

* 빅데이터 처리 및 머신러닝 애플리케이션.
* StatefulSet 및 Persistent Volume으로 상태 관리 지원.

### **Dev/Test Environments**

* 독립적인 개발 및 테스트 환경이 필요한 경우.
* Namespace를 활용해 환경을 효율적으로 분리 가능.

## How Kubernetes Work?

<figure><img src="../../.gitbook/assets/스크린샷 2024-11-27 오후 8.07.31.png" alt=""><figcaption></figcaption></figure>

1. **사용자**가 `kubectl`이나 API를 통해 **Kubernetes API Server**에 Deployment 생성 요청을 한다.
2. **Kubernetes API Server**는 요청을 받아 Deployment 객체를 생성하고 저장한다.
3. **Kubernetes API Server**는 새로운 Pod를 생성하기 위해 **Scheduler**에게 스케줄링을 요청한다.
4. **Scheduler**는 클러스터의 상태를 파악하여 적절한 Node를 선택하고, 그 결과를 **Kubernetes API Server**에 전달한다.
5. **Kubernetes API Server**는 선택된 Node의 **Kubelet**에게 Pod 생성 명령을 보낸다.
6. **Kubelet**은 **Container Runtime**(예: Docker, containerd)을 통해 컨테이너를 생성하고 시작한다.
7. **Container Runtime**은 컨테이너의 실행 상태를 **Kubelet**에게 보고한다.
8. **Kubelet**은 Pod의 현재 상태를 **Kubernetes API Server**에 업데이트한다.
9. **Kubernetes API Server**는 **사용자**에게 Deployment 생성이 완료되었음을 응답한다.

이러한 과정을 통해 Kubernetes는 사용자의 요청에 따라 \
컨테이너화된 애플리케이션을 자동으로 배포하고 관리한다.

## Kubernetes Setting

### Prepare deployment/service.yml file

```yaml
### deployment.yml

apiVersion: apps/v1          # 이 Deployment가 사용할 API 버전을 나타냄. 여기서는 apps/v1 사용.
kind: Deployment             # Kubernetes 리소스의 종류를 지정. 여기서는 Deployment를 생성.
metadata:                    # 리소스의 메타데이터를 정의.
  name: springboot-kotlin-app # Deployment의 이름. 클러스터 내에서 고유해야 함.
  labels:                    # 이 Deployment에 붙일 레이블. 리소스 식별에 사용.
    app: springboot-kotlin-app # Deployment와 관련된 리소스를 그룹화하는 데 사용될 레이블.
spec:                        # Deployment의 스펙(설정) 정의.
  replicas: 2                # 이 Deployment로 생성할 Pod의 수. (여기서는 2개)
  selector:                  # 이 Deployment가 관리할 Pod를 식별하기 위한 조건.
    matchLabels:             # 레이블을 기반으로 Pod 선택.
      app: springboot-kotlin-app # app=springboot-kotlin-app 레이블을 가진 Pod 관리.
  template:                  # 새로 생성할 Pod의 템플릿 정의.
    metadata:                # 생성될 Pod의 메타데이터 설정.
      labels:                # Pod에 추가될 레이블 정의.
        app: springboot-kotlin-app # 생성되는 Pod에 app=springboot-kotlin-app 레이블 추가.
    spec:                    # Pod 내부 컨테이너의 설정 정의.
      containers:            # Pod에 포함될 컨테이너 목록.
      - name: springboot-kotlin-app # 컨테이너 이름. 다른 컨테이너와 구분하기 위해 사용.
        image: springboot-kotlin-app:latest # 컨테이너에 사용할 Docker 이미지.
        ports:               # 컨테이너가 노출하는 포트 정보.
        - containerPort: 8080 # 컨테이너 내부에서 서비스가 사용하는 포트.

```

```yaml
### service.yml

apiVersion: v1            # 이 Service가 사용할 API 버전을 나타냄. 여기서는 v1 사용.
kind: Service             # Kubernetes 리소스의 종류를 지정. 여기서는 Service를 생성.
metadata:                 # 리소스의 메타데이터를 정의.
  name: springboot-kotlin-service # Service의 이름. 클러스터 내에서 고유해야 함.
spec:                     # Service의 스펙(설정) 정의.
  selector:               # 이 Service가 트래픽을 전달할 Pod를 식별하기 위한 조건.
    app: springboot-kotlin-app # app=springboot-kotlin-app 레이블을 가진 Pod로 트래픽 전달.
  ports:                  # Service가 노출할 포트 설정.
    - protocol: TCP       # 트래픽이 전달될 네트워크 프로토콜. 여기서는 TCP 사용.
      port: 80            # 클라이언트가 접속할 Service의 포트 번호.
      targetPort: 8080    # Service가 전달한 트래픽이 Pod의 어느 포트로 전달될지 지정.
  type: LoadBalancer      # 외부 트래픽을 로드밸런서를 통해 전달하는 Service 타입.
```

### **in**Install Kubernetes in Linux

리눅스에서는 Kubernetes 클러스터를 직접 설정하거나, 간단히 테스트하기 위해 도구를 활용할 수 있다.

**옵션 1: Minikube 사용**

* 로컬 환경에서 Kubernetes를 실행하려면 Minikube를 설치한다.
* Minikube는 Kubernetes 클러스터를 생성하고, Docker를 컨테이너 런타임으로 사용할 수 있다.

```bash
# Minikube 설치
curl -LO https://storage.googleapis.com/minikube/releases/latest/minikube-linux-amd64
sudo install minikube-linux-amd64 /usr/local/bin/minikube

# Minikube 시작
minikube start --driver=docker
```

**옵션 2: kubeadm 사용**

* kubeadm은 Kubernetes 클러스터를 설치 및 구성하는 도구이다.
* Docker를 설치한 후, kubeadm으로 클러스터를 설정한다.

**설치 및 실행**

```bash
# Docker 설치
sudo apt update
sudo apt install -y docker.io

# kubeadm, kubectl, kubelet 설치
sudo apt install -y apt-transport-https curl
curl -s https://packages.cloud.google.com/apt/doc/apt-key.gpg | sudo apt-key add -
echo "deb https://apt.kubernetes.io/ kubernetes-xenial main" | sudo tee /etc/apt/sources.list.d/kubernetes.list
sudo apt update
sudo apt install -y kubelet kubeadm kubectl
sudo apt-mark hold kubelet kubeadm kubectl

# Kubernetes 클러스터 초기화
sudo kubeadm init

# 클러스터에 관리자로 접근하기 위해 설정
mkdir -p $HOME/.kube
sudo cp -i /etc/kubernetes/admin.conf $HOME/.kube/config
sudo chown $(id -u):$(id -g) $HOME/.kube/config
```

### **Deploy Docker Container on Kubernetes**

Docker를 컨테이너 런타임으로 사용하면, 작성한 Docker 이미지를 Kubernetes 클러스터에 배포할 수 있다.

### **Use Docker Image in Kubernetes**

로컬에서 빌드한 Docker 이미지를 Kubernetes에서 사용하려면, \
클러스터에서 접근 가능하도록 이미지를 등록해야 한다.

**옵션 1: Minikube**

Minikube를 사용하는 경우, 로컬 Docker 이미지를 바로 사용할 수 있다.

```bash
eval $(minikube docker-env)
docker build -t springboot-kotlin-app:latest .
```

**옵션 2: 클러스터에서 직접 로컬 이미지 사용**

Kubernetes 클러스터가 이미지를 인식하도록 `imagePullPolicy`를 `Never`로 설정한다.

```yaml
containers:
- name: springboot-kotlin-app
  image: springboot-kotlin-app:latest
  imagePullPolicy: Never
```

***

### **Apply Kubernetes Resource**

`kubectl` 명령어로 Deployment와 Service를 적용한다.

```bash
kubectl apply -f deployment.yaml
kubectl apply -f service.yaml
```

***

### **Check Resource Status**

```bash
kubectl get pods
kubectl get services
```

***

## Accessing the Application from Outside the Cluster

*   Minikube를 사용하는 경우:

    ```bash
    minikube service springboot-kotlin-service
    ```
* 일반 Kubernetes 클러스터에서: Service 타입이 `LoadBalancer`인 경우, \
  클라우드 제공업체가 아닌 로컬 클러스터에서는 `NodePort`를 통해 접근해야 한다.

```bash
kubectl get services
```

`NodePort`의 `PORT`를 확인한 뒤, `<노드 IP>:<NodePort>`로 접속한다.

***

## **Conclusion**

리눅스에서는 Kubernetes 클러스터를 별도로 설치해야 하며, \
Docker와 Kubernetes를 함께 사용하려면 Minikube나 kubeadm 같은 도구를 활용하면 편리하다.\
Docker와 Kubernetes의 조합은 로컬 개발 환경부터 대규모 프로덕션 환경까지 \
유연하게 사용할 수 있는 강력한 배포 도구이다. \
사실 회사에 인프라팀에서 하는 업무이지만 도커를 알아보며 \
연관이 깊은 쿠버네티스에 대해 개념이라도 익혀두면 좋을것 같아 포스팅 해보았다.
