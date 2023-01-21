# day22 - 네트워크 핵심이론 기초

[https://www.inflearn.com/course/네트워크-핵심이론-기초](https://www.inflearn.com/course/%EB%84%A4%ED%8A%B8%EC%9B%8C%ED%81%AC-%ED%95%B5%EC%8B%AC%EC%9D%B4%EB%A1%A0-%EA%B8%B0%EC%B4%88)

## 네트워크와 네트워킹 그리고 개념

Network : 관계

Networking : 상호작용

상호작용이란 결국 Layerd 구조로 이뤄짐.

예를들어 영어(언어) ← 말 ← 공기중 이런식으로 관념적인 개념이 계층별로 이루어져야 상호작용이 가능함.

규칙 = Protocol 이라고 생각하자.

OSI 7 Layer는 개념, 철학적 이므로 TCP/IP나 구체적인 실체에 대해 공부하고 난 뒤에 저절로 알게될 수 있음

## User mode와 Kernel mode

User mode와 Kernel mode 는 인간계와 신계처럼 다른 개념.

User mode에서 Kernel mode로 소통하기 위해서는 File 이 필요한데 이를 TCP/IP에서는 ‘Socket’이라함. 이 인터페이스를 이용해서 소통한다.

<img width="630" alt="1" src="https://user-images.githubusercontent.com/44457591/213870738-ec626c23-e87b-4d03-9f67-2a9283a3000e.png">

User mode 는 L5 계층 이상이라고 생각하면 된다.

# Internet 기반 네트워크 입문

## OSI 7 layer와 식별자

유선네트워크 = Ethernet

**L2의 식별자**

MAC 주소 = LAM 카드 = NIC (Network Interface Card)

**L3의 식별자**

IP주소

**L4의 식별자**

Port 번호

(경우에 따라 port번호는 여러군데에 다의적으로 쓰임.

L2에서는 인터페이스 식별자(스위치),

L3에서는 네트워크 서비스 식별자,

L7에서는 Process 식별자)

<img width="779" alt="2" src="https://user-images.githubusercontent.com/44457591/213870779-bcc7cf3e-2c30-4b9e-9b5e-8edc7018d028.png">

## Host는 이렇게 외우자

Host : Network 에 연결된 Computer

Switch : Host중 Nerwork 자체를 이루는 것 ex) Router, Tab Switch, IPS

**Endpoint** : 그 네트워크 인프라를 이용하는 이용주체는 단말기. ex) Client, Server, Peer

## Switch가 하는일

Network = 고속도로망

Switch = 교차로

교차로에서 경로를 찾는 행위 = Switching 혹은 Interface 선택

자동차는 Packet 이라고 비유.

IP주소를 근거로 스위칭한다면 L3 스위치

L3 스위치는 Router 라고 할 수 있으며, 이정표는 Routing Table (목적지 주소 같은 것들이 쓰여있음)

따라서,

MAC주소를 근거로 스위칭한다면 L2 스위치

포트번호를 근거로 스위칭한다면 L4 스위치

HTTP 프로토콜 규칙에 근거해서 스위칭한다면 L7 스위치

Matric 값 = 비용

비용이 낮은 쪽으로 이동해야한다. (참고상 가볍게 알아둔다)

<img width="619" alt="3" src="https://user-images.githubusercontent.com/44457591/213870786-35dbf2ba-78b3-4b7e-a385-0c64c335ca2f.png">

# L2 수준에서 외울 것들

## NIC, L2 Frame, LAN 카드 그리고 MAC 주소

NIC = Network interface Card = LAN 카드

유/무선 NIC 가 있지만 굳이 구별하지 않고 NIC 이라 한다.

NIC은 H/W이며 MAC 주소를 갖는다.

<img width="585" alt="4" src="https://user-images.githubusercontent.com/44457591/213870804-0ce9da8b-e732-4776-97c2-8e5bfeae0502.png">

LAN이란 네트워크의 규모이다. (Local Network Area)

L2에서의 단위 : Packet은 L2 인터넷에서의 단위와 Frame 단위로 말할 수 있음

Frame : 유통단위 (1514Frame 정도.. 10 KB 조금 안된다)

보통 랜카드 전송속도는 1Gbps (b는 bit를 의미)

## L2 Access switch

엔드포인트(like PC)와 직접 연결되는 스위치

## LAN과 WAN의 경계 그리고 Broadcast

Broadcasting → 효율을떨어뜨린다. 꼭 필요할때만 써야하는 제한적인 통신방법

Broadcast 주소라는 매우 특별한 주소가 있음. (MAC, IP 모두 존재한다.)

2진수에서 모두 1111 1111 로 되어있거나, 16진수에서 FF FF FF FF FF FF 이렇게 되어있는 주소를 Broadcast 주소라고 한다.

<img width="704" alt="5" src="https://user-images.githubusercontent.com/44457591/213870815-a633a067-cdff-489a-a706-762c6e184348.png">

전체 네트웍에서 Broadcasting을 하면 다른 장비들은 잠깐 통신을 멈춘다.

그래서 브로드캐스팅 잘못하면 네트웍이 완전 느려진다. 그래서 브로드캐스트를 최소화 해야한다!

<img width="716" alt="6" src="https://user-images.githubusercontent.com/44457591/213870832-8d7220b1-8722-477a-afc9-9cabf576811b.png">

S/W : Logical 논리적 (실체가 없다.) = Virtual

H/W : Physical 물리적

가상화 기술  : 하드웨어를 소프트웨어적으로 구현하는 것

소프트웨어 논리적 계층을 WAN 이라고 생각하면 좀 더 이해가 쉽다.

하드웨어 물리적 계층을 LAN이라고 생각하면 좀 더 이해가 쉽다. (정답은 아님 요령정도로)

# L3 수준에서 외울 것들

## IPv4주소와 기본 주소

<img width="637" alt="7" src="https://user-images.githubusercontent.com/44457591/213870838-5cda17bf-fc65-44cf-8ceb-3d80c43e2a8d.png">

IP = 24bit의 NetworkID + 8bit의 Host ID

## L3 IP Packet으로 외워라

Packet이라는 말은 L3 IP Packet으로 외워라.

최대 크기는 MTU (=1500bytes)

Packet은 퍼즐조각, 혹은 송장붙은 택배박스라고 생각하면 쉽다.

Packet = Header(송장) + Payload

Wireshark를 통해서 Packet을 분석할 수 있음.

##
