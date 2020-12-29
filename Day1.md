# day1

# 오늘 발생한 일

<img width="887" alt="스크린샷 2020-12-29 오전 11 48 50" src="https://user-images.githubusercontent.com/44457591/103271535-d724d180-49fd-11eb-996b-c05d7ce33738.png">

iFrame 에서의 CORS(Cross Origin Resource Sharing) 정책으로 인한 차단으로 팝업(자식)에서 버튼클릭시 부모창 이동이 안됐음.

요새 보안이 쎄져서 안되는게 많아진당.. 😇

해당 에러는 부모와 자식간, Orifin 즉 Domain이 다른 상황에서 자신이 아닌 상대방의 DOM에 접근하려 할 때 발생함. 

- 부모 DOM에서 자식 DOM이 가지고 있는 함수 또는 프로퍼티를 참조하려고 하는 경우.
- 자식 DOM에서 부모 DOM이 가지고 있는 함수 또는 프로퍼티를 참조하려고 하는 경우.

![Untitled](https://user-images.githubusercontent.com/44457591/103271492-bceaf380-49fd-11eb-826f-92113929fe89.png)

이경우는 프로토콜이 다른 `http` 에서 `https` 로 접근하려 하여 발생. 

# 해결

프로토콜을 변경하여 해결함.

다른 방법으로는 

window.postMessage() 빌트인 함수를 이용하는 방법 (개발단에서의 우회)

부모 데이터를 자식한테 보내주는 방법이 있다고 함. 

추가로 서버에서 CORS 를 특정 사이트 혹은 모든 사이트에 대해 허용해주고자 한다면

허용할 Origin을 Access-Control-Allow-Origin 응답 헤더에 넣어주면 된다. 그러면 다른 Origin일지라도 json 데이터와 같은 자원들을 응답고 읽을 수 있게 된다.

- 모든 사이트를 허용하는 경우: "Origin을 `Access-Control-Allow-Origin": *`
- 특정한 사이트만 허용하는 경우: "Origin을 Access-Control-Allow-Origin": https://www.coding-groot.tistory.com/

근데 이 경우는 http라고 막았던것보다는 iframe 에서 막힌 브라우저 문제같다.
