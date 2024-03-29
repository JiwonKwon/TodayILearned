# day21 - Docker 네트워크 구조 살펴보기

요새 내 주변에 공기처럼 존재하는 Docker.. 알긴아는데 제대로 아는것 같지 않아서 다시 기록처럼 정리해본다.

## Docker란?

리눅스 컨테이너를 만들고, 사용하게 할 수 있는 컨테이너화 기술.

## 그렇다면 컨테이너란?

### 컨테이너가 만들어지게된 배경

기존에 애플리케이션이 배포되는 환경을 서버에 직접 설치해서 구성할 경우, 서비스가 점차 확장되면서 기존에 사용하던 서버와 새로 구축하는 서버의 설정차이가 발생하면서 유지보수의 어려움이 생겼다.

이로인해 스크립트를 활용한 자동화방식, OS를 가상화하는 방식까지 다양한 형태로 서버를 관리해왔다.

기존의 가상화 기술은 *하이퍼바이저를 이용해 여러개의 운영체제를 하나의 호스트에서 생성해서 사용하는 방식이었다. 이러한 가상머신은 라이브러리, 커널등을 전부 포함하기에 배포하기 위한 이미지로 만들었을때 너무 커서 가상머신을 애플리케이션으로 배포하기 부담스러웠습니다.

* 하이퍼바이저 : 하드웨어에서 시스템의 운영 체제와 리소스를 분리해 VM에 할당하여 가상 머신을 생성하고 구동하는 소프트웨어. 호스트 컴퓨터에서 다수의 운영체제를 동시에 실행하기 위한 논리적 플랫폼.

애플리케이션만을 띄우고 싶은데, OS 까지 띄우는 것은 엄청난 낭비였음. 매번 OS 부팅도 해야하고.. 빌드도 해야하고.. 보안패치를 할때마다 굉장히 번거로움.

> "격리된 CPU, 메모리, 디스크, 네트워크를 가진 공간을 만들고 이 공간에서 프로세스를 실행해서 유저에게 서비스" 하려면 어떻게 해야 할까?
>

![container](https://user-images.githubusercontent.com/44457591/213696152-0aa5b86b-8987-41e2-94fc-873250229af9.png)

기존엔 하이퍼바이저 위에 OS를 각각 띄어서 사용했음. 이로인해 이미지용량이 굉장히 큼.

하지만 컨테이너에 필요한 커널은 호스트의 커널을 공유해서 사용하고 컨테이너 안에는 어플리케이션에 필요한 라이브러리 및 실행파일만 존재하기 때문에 이미지 용량이 대폭 줄어든다.

## 컨테이너란?

컨테이너는 데스크탑, 기존의 IT 또는 클라우드 등 어디서나 실행될 수 있도록 애플리케이션 코드가 해당 라이브러리 및 종속 항목과 함께 패키징되어 있는 소프트웨어 실행 유닛입니다. (출처 IBM)

컨테이너는 운영체제 수준에서 CPU, 메모리, 스토리지, 네트워크 리소스를 가상화하므로 개발자에게 다른 애플리케이션으로부터 논리적으로 격리된 OS 환경을 제공합니다. (출처 GCP)

**그니까 결국 컨테이너는 완전히 격리된 OS환경을 제공한다는 것이다. 컨테이너를 사용하면** **애플리케이션의 개발과 배포가 편해지며, 여러 애플리케이션의 독립성과 확장성이 높아집니다.**

컨테이너 단위로 적재를 하니까 다른 컨테이너에 영향을 주지 않고 OS를 새로 시작할 필요도 없다.

### **컨테이너끼리 어떻게 통신하지?**

컨테이너를 생성하면, 독자적인 파일시스템이 생성되고, 이때 네트워크 인터페이스도 생성이된다.

컨테이너에 mac주소와 ip가 할당되어 있음. 이러한 컨테이너들끼리 통신하기 위해서는

컨테이너 내에 라우팅 테이블 gateway 브릿지인 **docker0** 브릿지를 통해 외부와 통신.

## 그렇다면 Docker 네트워크를 알아보자

![Untitled](https://user-images.githubusercontent.com/44457591/213696128-39109d49-4b55-4e3c-93f1-4b622a470f93.png)


- 브릿지란? 네트워크 장비. 스위치 같은 하드웨어 장비

각 컨테이너는 이 브릿지인 docker0를 통해 통신.

내부통신을 할땐 docker0를 통해 통신한다.

- veth란? pair의 가상 네트워크 인터페이스 (두개의 namespace를 연결시킨다)
- 네임스페이스? 컨테이너 내의 독립된 리눅스 구조 (비슷하겐 cgroup도 있다)
- 기본적으로 컨테이너는 외부와 통신이 불가능하다. 우리는 컨테이너 ip를 확인하기 어렵다. 그래서 포트포워딩을 설정해서 외부에 컨테이너를 공개할 수 있다.

**실습) 리눅스에서 docker0 동작방식 확인**

```bash
# brctl : bridge utils 브릿지에 대한 소프트웨어적인 내용을 살펴볼때 쓸 수 있는 명령어

# 1. docker0 동작방식 확인
# 1) docker0 브릿지에 연결되어있는 컨테이너를 확인할 수 있다
$ brctl show docker0

# 2) ubuntu image 컨테이너를 세개를 띄워본다 (세번수행)
$ docker run -it -d ubuntu:16.04

# 3) docker0 브릿지에 연결되어있는 컨테이너를 확인할 수 있다
$ brctl show docker0
```

<img width="502" alt="스크린샷 2023-01-20 오후 8 16 59" src="https://user-images.githubusercontent.com/44457591/213695899-f34ddb28-83a2-40f9-89d2-0887974bb88d.png">


컨테이너 세개가 docker0브릿지에 veth 인터페이스로 연결되어있는 것을 확인할 수 있다

```bash
# eth0 컨테이너의 네트워크 구성 확인
$ docker ps

# 1) 자식프로세스로서 docker 컨테이너 하나에 접속 해본다
$ docker exec -it b38ee83b885c /bin/bash

# 2) 접속 후 iproute2를 다운받아본다
root@b38ee83b885c:/# apt update && apt install -y iproute2

# 3) ip 정보 확인을 통해 eth0으로 네트워크 구성되어있는 것을 확인할 수 있음
root@b38ee83b885c:/# ip addr show
```

<img width="669" alt="스크린샷 2023-01-20 오후 8 31 40" src="https://user-images.githubusercontent.com/44457591/213695871-de5a25d0-ad9e-483a-a3ac-c3a22b4a65c8.png">


→ 요건 그냥 컨테이너로 띄운 고유한 리눅스! eth0 기준으로 생성이되어서 ip address가 할당되어 있다.

```bash
# docker IP 등 정보 자세히 확인 명령어
$ docker inspect $(docker ps -q) | grep "IPAddress\|Id"

# docker bridge 정보 확인
$ docker inspect bridge
```

<img width="591" alt="스크린샷 2023-01-20 오후 8 35 46" src="https://user-images.githubusercontent.com/44457591/213695656-394b4f79-0971-4746-ab32-e834a0456ff1.png">


172.17.0.4 등등 컨테이너 마다 고유의 ip가 할당된 것을 확인할 수 있다.
