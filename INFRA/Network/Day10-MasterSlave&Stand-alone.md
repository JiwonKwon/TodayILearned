# day10

AWS 관련 미팅을 하다가 여느때와 다름없이 묵언 수행중.. ^^ 

![스크린샷 2021-01-08 오후 2 26 44](https://user-images.githubusercontent.com/44457591/104022952-538c9100-5204-11eb-85a0-7a0169cdaa3e.jpeg)


몇가지는 외계어를 듣는듯한 기분이었다. 

어플리케이션 쪽도 잘 모르지만 인프라는 진짜 다른 영역의 세상같고 들을때마다 너무 어렵다.

그 래 서 ! 용어들을 정리해 놓기로 했다. 

![다운로드](https://user-images.githubusercontent.com/44457591/104022994-5f785300-5204-11eb-9a88-fef875fa9f5a.jpeg)


---

## StandAlone 이란?

어감만 들어도 혼자 뭔가를 할것같다.

말그대로 _혼자서 다하는 경우_를 StandAlone이라고 칭한다.

**예)** Fax는 혼자서도 동작이됨. Stand-alone Device라고 할 수 있다.

반면, Print 는 동작을 위해서 컴퓨터가 필요하다. Stand-alone이 아님!

이처럼 독립적으로 이용이 되냐/안되느냐에 따라서 Stand-alone이라는 용어를 붙일 수 있다.

**+)** Redis(원격캐시서버)는 Standalone, Sentinel, Cluster 모드로 각 업무영역에 사용하고 있다.

그래서 Redis Standalone으로 구성해주세요~ 하면 그냥 서버 한대만 구성해서 쓰겠다는 말.

_그러나_

![다운로드 (1)](https://user-images.githubusercontent.com/44457591/104023083-7d45b800-5204-11eb-91d9-41a4dc71f775.jpeg)


Stand-alone 방식으로 DB를 구성한다면, 혹시 그 DB에 부하가 심하다던지 장애가 나면 데이터를 복구못할 수 있고

fail-over 상황에 전혀 대비할 수 없게된다. 

\*fail-over : 컴퓨터,서버,시스템, 네트워크 등에서 이상이 생겼을 때 시스템 대체 작동 또는 장애 극복(조치)을 위하여 예비 시스템으로 자동 전환되는 기능이다.

그래서 있는게 Replicaton (복제) 방식 ~~~

![다운로드 (2)](https://user-images.githubusercontent.com/44457591/104023064-75861380-5204-11eb-8b7b-3a93c0e9b295.jpeg)

---

## Replication (Master-slave 방식) 란?

![다운로드](https://user-images.githubusercontent.com/44457591/104023131-89ca1080-5204-11eb-90f6-65aa1dde5918.png)

용량의 2-3배를 쓰더라도 Master의 데이터를 Slave에 복제하여 **안정성**을 유지한다.

Slave를 추가로 구성하면, Master가 죽었을 때 Master의 내용을 복제하여 가지고 있던

Slave노드가 Master로 승격되면서 중단없이 서비스를 제공할 수 있게 된다.

따라서 클러스터 구성시에 꼭 Master-Slave 구조로 _클러스터_를 구성해야 한다.

휴~~ 클러스터란 또 뭐란말임. 안와닿죵?!!!! (응 나만 ㅠ)

---

## _클러스터(Cluster)란?_

위에서의 의미는 서버 클러스터를 의미함! 

**_각기 다른 서버를 하나의 시스템처럼 동작하게 함_**으로써 클라이언트에게 고가용성을 제공하는 것을 의미함.

특징으로는 여러대의 서버에 데이터가 분산되어 저장되므로 트래픽이 분산되는 효과를 얻을 수 있다.

클러스터라는 용어는 본래 포도송이,,귀여워라.. 집단이나 군집을 의미함.

IT에서 이 용어는 다양하게 사용되는데 서버, DB, 디스크 클러스터 등이 있다. 

각 분야에서 사용되지만 큰 의미로 집합이라는 것은 일치한다. 

**+)**Redis도 상용서비스에는 데이터 유실을 방지하기 위해 주로 Master-slave 구조를 사용한다.

하지만 비동기방식으로 Master에서 Slave 복제 후 ack 를 받지 않아서 데이터 유실 가능성이 존재한다고 함.

[참고 : www.letmecompile.com/redis-cluster-sentinel-overview/](https://www.letmecompile.com/redis-cluster-sentinel-overview/)

Redis Cluster 튜토리얼은 아래 medium에 굉장히 자세히 나와있당 ㅎ.ㅎ

[shorturl.at/dhkQ1](http://shorturl.at/dhkQ1)

레디스 어쩌고를 많이 이야기하는 건 제가 요새 Redis에 관심이 많아서 그렇읍니다

.

.

.

총총총
