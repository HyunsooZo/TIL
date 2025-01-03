---
description: >-
  즐겨 듣는(?) 애플코딩의 영상을 시청 후 도커의 개념에 대해 정리해보았다. 단순 개념 외에 예제 코드 등을 작성해볼 예정이기에 인프라와
  다른 카테고리로 분류했다.
---

# 🥸 Docker : What's Docker?

## Docker ?

도커는 **리눅스 컨테이너** 기반의 가상화 기술이다. \
이를 활용하면 **OS, 라이브러리, 필요한 프로그램 등을 하나의 이미지로 묶어** 다른 환경에서도 \
**동일한 실행 환경**을 제공할 수 있다. \
도커를 활용하면 **코드와 함께 환경까지 밀키트처럼** 패키징하여 어디서나 쉽게 실행할 수 있다.

### Docker and MealKit

즐겨듣는 유튜버 **애플코딩**은 도커를 밀키트에 비유하여 설명하는데\
큰 틀에서의 도커의 동작과 역할을 재미있게 나타낸 것 같아 \
해당 포스팅에서도 밀키트에 비유하여 설명하겠다.

서울에서 운영하던 치킨집이 잘되어 부산에 분점을 내고 싶다고 가정하자. \
이때 단순히 레시피만 보내는 것은 부산의 주방 환경, 도구, 재료 등이 다르기 때문에 맛이 달라질 수 있다. \
이를 해결하기 위해 서울에서 쓰던 모든 재료, 도구, 레시피를 밀키트 형태로 포장하여 부산으로 보낸다면, \
부산에서도 서울과 동일한 맛을 낼 수 있다. \
도커는 개발 환경에서 이와 같은 역할을 한다고 볼 수 있다.

## How Docker & Conatiner work?

도커를 사용하면 **도커 파일**을 작성하여 필요한 OS, 라이브러리, 프로그램, 실행 코드를 정의할 수 있다.\
도커 파일에 설정된 환경이 **이미지**로 생성되고, 이 **이미지를 기반으로 컨테이너가 실행**된다. \
**컨테이너**는 **독립적인 가상 환경에서 코드를 실행**하며, 이를 통해 다른 환경에서도 동일한 결과를 보장할 수 있다.

<figure><img src="../../.gitbook/assets/스크린샷 2024-11-23 오후 4.44.37.png" alt=""><figcaption></figcaption></figure>

## Pros of Docker

### **Environment Standardization**

도커 이미지는 실행 환경(OS, 라이브러리, 의존성)을 코드로 명시하여 어디서나 동일한 실행 결과를 보장한다.

* **팀 협업에 유리**: 개발 환경의 차이로 발생하는 "내거에선 되는데?" 같은 문제를 방지한다.
* **운영 환경과의 일치**: 로컬 개발 환경과 운영 서버 환경의 차이를 제거하여 배포 오류를 줄인다.

### **Efficient Deployment**

도커 이미지를 활용하면 빌드-테스트-배포 과정이 간소화된다.

* **빠른 배포**: 이미지를 빌드하여 각 서버에서 동일하게 배포할 수 있어 시간이 단축된다.
* **롤백 용이성**: 문제가 생기면 이전 버전 이미지를 바로 재배포할 수 있다.

### **Support for MSA**&#x20;

마이크로서비스 아키텍처에서 각 서비스가 독립적으로 운영될 수 있도록 지원한다.

* **서비스 독립성 보장**: 각 컨테이너가 고립된 환경에서 실행되므로 서비스 간의 충돌이 없다.
* **확장성 향상**: 필요한 서비스만 수평 확장(스케일링) 가능하다.

### **Improved Maintainability**

환경 설정과 배포 과정을 코드로 관리(Infrastructure as Code)하여 유지보수가 쉽다.

* **변경 추적 가능**: 도커 파일을 통해 환경 변경 사항을 기록하고 버전 관리할 수 있다.
* **자동화 가능**: CI/CD와 연동하여 자동화된 테스트 및 배포 파이프라인을 구축할 수 있다.

### Clean Local Environment

로컬 시스템에 직접 설치하지 않고도 다양한 환경에서 프로그램을 실행할 수 있다.

* **테스트 다양화**: 여러 버전의 라이브러리나 프로그램을 독립적으로 실행하며 테스트할 수 있다.
* **리소스 관리 효율화**: 컨테이너를 삭제하면 흔적 없이 깔끔하게 환경을 제거할 수 있다.

### **Cost Efficiency**

기존 VM 대비 더 적은 리소스를 사용하여 같은 작업을 수행할 수 있다.

* **효율적 리소스 사용**: 하나의 서버에서 여러 컨테이너를 실행하여 하드웨어 사용률을 극대화한다.
* **인프라 관리 단순화**: Kubernetes 등과 함께 사용하면 대규모 시스템 관리도 용이하다

***

## Cons of Docker

### **Security Issues**&#x20;

컨테이너는 가상 머신보다 격리 수준이 낮아 보안 취약점이 발생할 수 있다.

* **공유 커널 취약점**: 컨테이너는 호스트 OS의 커널을 공유하므로 커널 보안 취약점이 컨테이너에 영향을 줄 수 있다.
* **이미지 보안**: 외부에서 가져온 이미지에 악성 코드가 포함될 가능성이 있다.
* **권한 관리**: 컨테이너 실행 시 루트 권한 사용으로 인한 보안 위험이 있다.

### **Costs**&#x20;

컨테이너 사용이 증가할수록 리소스 사용량과 관리 비용이 증가한다.

* **서버 리소스 오버헤드**: 컨테이너 오케스트레이션 도구(Kubernetes 등)를 사용할 경우 리소스와 비용 부담이 커질 수 있다.
* **복잡성 증가**: 컨테이너 관리와 모니터링을 위해 추가적인 툴과 인력이 필요하다.

### **Steep Learning Curve**&#x20;

초보자에게는 도커와 컨테이너 관련 기술 학습이 쉽지 않을 수 있다.

* **개념적 복잡성**: 컨테이너와 이미지, 볼륨, 네트워크 등을 처음 배우는 과정이 어렵다.
* **생태계 복잡성**: 도커와 함께 사용하는 툴(Kubernetes, Helm 등)까지 익혀야 하는 경우가 많다.

### **Data Persistence Issues**

컨테이너 자체는 상태를 저장하지 않기 때문에 데이터를 영속적으로 관리하기 어렵다.

* **볼륨 설정 필요**: 데이터를 유지하려면 별도로 볼륨을 설정해야 한다.
* **컨테이너 삭제 시 데이터 손실**: 컨테이너와 함께 데이터를 삭제하지 않도록 주의가 필요하다.

### **Network Overhead**

컨테이너 간 네트워크 통신은 네이티브 네트워크보다 오버헤드가 발생할 수 있다.

* **복잡한 네트워크 설정**: 다수의 컨테이너가 통신할 때 네트워크 설정이 복잡해질 수 있다.
* **성능 저하**: 네트워크 가상화 기술이 성능에 영향을 미칠 수 있다.

## Conclusion

도커는 개발자들에게 효율적이고 안정적인 개발 환경을 제공하는 강력한 도구이지만\
이를 효과적으로 활용하기 위해서는 기본 코딩 실력과 환경에 대한 이해가 뒷받침되어야 한다. \
도커에 대해 조금 더 깊게 알아보기 위해 해당 카테고리에서는 도커 관련 포스팅을 다루도록 하겠다.
