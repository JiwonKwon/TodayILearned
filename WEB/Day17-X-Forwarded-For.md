# day17-X-Forwarded-For

> XFF 는 HTTP Header 중 하나로 HTTP Server 에 요청한 clinet 의 IP 를 식별하기 위한 사실상의 표준이다.

**웹 서버나 WAS 앞에 L4 같은 Load balancers 나 Proxy server([HAProxy](http://www.haproxy.org/)), caching server([Varnish](https://www.varnish-cache.org/)), HTTP 서버용 WAS Connector(웹로직 커넥터 - mod_wl, 톰캣 커넥터 - mod_jk 등) 등이 있을 경우,**

**이런 제품들은 웹서버/WAS 에 HTTP 나 전용 프로토콜(AJP)로 요청을 보낸후에 받은 결과를 가공하여 클라이언트에 재전송하게 된다.**

**이로 인해 처리한 웹 서버나 WAS에서 *request.getRemoteAddr();* 등으로 클라이언트 IP를 얻을 경우 L4 나 Proxy 의 IP 를 얻게 되는데 이는 원하는 결과가 아니다.**

X-Forwarded-For는 이 문제를 해결하기 위해 사용하는 http header 로 squid caching server 에서 처음 사용되었다.

![ipad](https://user-images.githubusercontent.com/44457591/109515154-1cdb4480-7aea-11eb-9df4-e735a006be80.png)
