회사에서 새로운 Jira 이슈를 할당받았는데. 

[##_Image|kage@bqdRel/btqOvRPE28N/x0eCFqcglLrUccCsnFkH1k/img.png|alignCenter|data-filename="스크린샷 2020-11-30 오후 10.01.18.png" data-origin-width="1086" data-origin-height="66" data-ke-mobilestyle="widthContent"|||_##]

what ? 

웹개발자라면 알아야할 것 같구 한번쯤 들어는(들어만) 본것같은데 뭔소린지 잘 모르겠어서 잠시 멍때렸다. 

[##_Image|kage@Z2egm/btqOFhUb5xu/AB1xwmLP2PHD2Fnk6wkVKK/img.jpg|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|||_##]

하지만 차근차근 공부하면 된다 ^.^ 3년차까지 모르는 건 죄가 아니당,, 아늬! 5년차까지로 할랭 

---

# SOP(Same-origin Policy)

CORS를 이해하기전, SOP를 알아보자.

SOP (Same Origin Policy) 란 **똑같은 출처 에서만 Rescoure를 가져올 수 있다.** 는 정책이다. 

여기서 **출처가 같다 = 두 URL의 호스트, 프로토콜, 포트가 같다** 를 의미함.

예를 들어서,   https://google.com 은 https://google.com/dir/page.html 의 이미지나 리소스를 가져올 수 있다. 

하지만 http://google.com/ (_프로토콜이 다름_) 나 https://google.com:88/dir/page.html (_포트가 다름, 근데 IE는 포트번호를 무시하는 브라우저라 가능할 수도있음. IE ..._ ) 해당 도메인들의 이미지나 리소스를 가져올 수 없다. 

URL을 구성하는 각 명칭 및 개념은 아래의 이미지를 참고하자.

[##_Image|kage@bockXx/btqOH9nXBDT/kUMk1GjVB7Xzq4kKw7ojSK/img.png|alignCenter|data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|그냥 주소인줄 알았는데 다 명칭이 있는것.. 모든건 의미가 있는것.. url 주소 나부랭이도 의미가 있었따.||_##]

사실 https 는 암묵적으로 443 http는 80을 의미하므로 포트번호는 생략이 가능하다고 본다. 

[참고 : RFC 2616](https://tools.ietf.org/html/rfc2616#section-3.2.2)

+)개발자 도구에서 location.origin 을 쳐보면 우리는 해당 웹의 출처를 알 수 있다. 

---

# CORS(Cross-origin resource sharing)

SOP로 인해서 같은 출처의 리소스만 쓰게할 순 없었다.

그래서 생겨난 것이 CORS 라고 이해하자.

(원래 CORS개념이 SOP 이전에 나왔다고는 하는데 그냥 그렇게 이해하는게 내가 편하다.)

### 비록 SOP란게 있찌만,,, CORS 정책 표준만 맞추면  다른 출처 (Cross-origin)쓰게 해줄게!!!!

### (인자한척하는 표정으루)

[##_Image|kage@IqnIF/btqOKYl7xyD/EB6h0vik6KIazSAInUVeP0/img.jpg|alignCenter|data-origin-width="0" data-origin-height="0" width="304" height="NaN" data-ke-mobilestyle="widthContent"|||_##]

## _그래서 정해진 CORS 표준_

---

### _1\. 심플한 요청 Simple Request_

> 이름은 심플한 요청이지만. 조건이 꽤 까다로워서 웹 어플리케이션 구현에선 보기 힘든 경우라고 한다.

> -   요청의 메소드는 GET, HEAD, POST 중 하나여야 한다.
> -   Accept, Accept-Language, Content-Language, Content-Type, DPR, Downlink, Save-Data, Viewport-Width, Width를 제외한 헤더를 사용하면 안된다.
> -   만약 Content-Type를 사용하는 경우에는 application/x-www-form-urlencoded, multipart/form-data, text/plain만 허용된다.

조건에 들어간 Header들은 기본적 헤더들이기 때문에, 복잡한 실제 웹에는 저 헤더들만 사용하기 어려우며,

또한 요즘 대부분 API 는 application/json 의 형태로 세번째 조건도 맞추기 어렵다.

암튼간에 심플 리퀘스트는 서버에서 본 요청부터 때려넣고 서버가 이에 대한 응답의 헤더에**Access-Control-Allow-Origin**과 같은 값을 보내주면 그때 브라우저가 CORS 정책 위반 여부를 검사하는 방식이다.

---

### _2\. 예비 요청 Preflight Request_

> 일반적으로 웹 어플리케이션에서 가장 자주 마주치는 방식이다. 이 시나리오에서 브라우저는 요청을 한번에 보내지 않고 예비 요청과 본 요청으로 나누어서 서버로 전송한다.  
>   
> 이 예비 요청에는 HTTP 메소드 중OPTIONS메소드가 사용된다. 예비 요청의 역할은 본 요청을 보내기 전에 브라우저 스스로 이 요청을 보내는 것이 안전한지 확인하는 것이다.  

> -   요청의 메소드는 GET, HEAD, POST 요청이 _아니어야 한다._
> -   POST 요청의 경우 Content-Tpye이 application/x-www-form-urlencoded, multipart/form-data, text/plain 이 _아니어야 한다. _
> -   **커스텀 헤더가 포함된 경우 위의 내용을 포함하지 않더라도 사전 요청을 보낼 수 있음. **

[##_Image|kage@c5cBVm/btqON8BybAd/zdyHCi8uMVoFePRkVCMqZ1/img.png|alignCenter|width="613" height="NaN" data-origin-width="0" data-origin-height="0" data-ke-mobilestyle="widthContent"|아이패드 맥북 미친거아냐..이래서 애플생태계를 못벗어나ㅠ 영어못해도 응대 잘해주라고 애플놈들아||_##]

간단하게 개발자 도구에서 테스트 해볼 수 있다.

예시1) tistory 블로그 내에서 jibab.tisotry 리소스를 호출 할 경우

[##_Image|kage@eDTtVI/btqOFiS3mUu/k0jmKpkCxeOITaYZxEru21/img.png|alignCenter|data-filename="스크린샷 2020-11-30 오후 11.02.38.png" data-origin-width="1178" data-origin-height="1294" width="583" height="NaN" data-ke-mobilestyle="widthContent"|||_##]

200OK 응답이 옴

_OPTIONS로 분명 예비요청을 했을텐데 그걸 브라우저에서 확인 하는법을 모르겠다._

_아시는분 알려주세요 제발...._

예시2) github 내에서 jibab.tistory 리소스를 호출 할 경우

(_**CORS 위반**_)

[##_Image|kage@qsVbJ/btqOFjEsbun/CG6L1hqNBpfj3FnJLqgaMk/img.png|alignCenter|data-origin-width="836" data-origin-height="640" data-filename="스크린샷 2020-11-30 오후 11.02.45.png" width="553" height="NaN" data-ke-mobilestyle="widthContent"|||_##]

Access-Control-Allow-Origin: [https://jibab.tistory.com](https://jibab.tistory.com) 이 다음과 같이 설정되어 있었을 것이다.

---

### _3\. 인증을 이용한 요청 Credentialed Request_

> 인증된 요청을 사용하는 방법이다. 이 시나리오는 CORS의 기본적인 방식이라기 보다는 다른 출처 간 통신에서 좀 더 보안을 강화하고 싶을 때 사용하는 방법이다.  
>   
> 쿠키정보나 인증과 관련된 헤더를 함부로 요청에 담지 않는다. 이를 담을 수 있게 해주는 옵션이 바로 **Credentials 옵션.**

credentials: include옵션을 사용하면 동일 출처 여부와 상관없이 무조건 요청에 인증 정보가 포함되도록 설정할 수 있음.

fetch('[https://jibab.tistory.com/rss'](https://jibab.tistory.com/rss'), { Credential: 'include', });

이때, 서버에서는 CORS 위반 응답이 오지만 두가지가 달라진다.

1.  Access-Control-Allow-Origin에는\*를 사용할 수 없으며, 명시적인 URL이어야한다.
2.  응답 헤더에는 반드시Access-Control-Allow-Credentials: true가 존재해야한다.

---

### CORS를 해결할 수 있는 방법

### _Access-Control-Allow-Origin 세팅하기_

인듯, 이 헤더는 Nginx 나 Apache와 같은 서버 엔진의 설정에서 추가할 수도 있지만, 아무래도 복잡한 세팅을 하기 불편하기 때문에.

소스코드 내에서 응답 미들웨어 등을 사용하여 세팅하는 것이 좋다고 한다.

Spring, Express, Django 와 같은 이름있는 백엔드 프레임웤에는 모두 CORS 관련 설정을 위한 세팅이나 미들웨어 라이브러리를 제공 한다구 함.
