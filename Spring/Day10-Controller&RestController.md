# day10

## API 추가 후 WhiteLabel Error

###

달력프로젝트에 카카오 API 추가하고 접근하니 아래와같은 에러를 만남

```

Whitelabel Error PageThis application has no explicit mapping for /error, so you are seeing this as a fallback.Sun Aug 13 14:34:00 KST 2017There was an unexpected error (type=Not Found, status=404).No message available
```

톰캣이 인식해야 하는  index.html 파일이나, index.jsp이 아직 없어서 웹 설정관련만 하면 해결되는 문제이다.

@Slf4j
-@Controller
+@RestController
@RequestMapping("/api")
@RequiredArgsConstructor
public class KakaoLoginController {

이경우 문제는 컨트롤러단에 `@Controller` 였다. `@RestController`로 변경하니 잘 됐음 !

# @Controller 와 @RestController의 차이

전통적인 `Spring MVC의 컨트롤러인 @Controller`와 `Restuful 웹서비스의 컨트롤러인 @RestController`의 주요한 차이점은 HTTP Response Body가 생성되는 방식

## **1. @Controller(Spring MVC Controller)**

---

## **[ Controller - View ]**

전통적인 Spring MVC의 컨트롤러인 @Controller는 주로 View를 반환하기 위해 사용합니다. 아래와 같은 과정을 통해 Spring MVC Container는 Client의 요청으로부터 View를 반환합니다.

![img](https://user-images.githubusercontent.com/44457591/104095254-3bd30c80-52d9-11eb-867a-d1a07e8ddfab.png)


1. Client는 URI 형식으로 웹 서비스에 요청을 보낸다.
2. Mapping되는 Handler와 그 Type을 찾는 DispatcherServlet이 요청을 인터셉트한다.
3. Controller가 요청을 처리한 후에 응답을 DispatcherServlet으로 반환하고, DispatcherServlet은 View를 사용자에게 반환한다.

## **[ Controller - Data ]**

하지만 Spring MVC의 컨트롤러에서도 Data를 반환해야 하는 경우도 있습니다. Spring MVC의 컨트롤러에서는 데이터를 반환하기 위해 @ResponseBody 어노테이션을 활용해주어야 합니다. 이를 통해 Controller도 Json 형태로 데이터를 반환할 수 있습니다.

![img (1)](https://user-images.githubusercontent.com/44457591/104095262-42fa1a80-52d9-11eb-839b-2a5fd9d2ca7c.png)


1. Client는 URI 형식으로 웹 서비스에 요청을 보낸다.
2. Mapping되는 Handler와 그 Type을 찾는 DispatcherServlet이 요청을 인터셉트한다.
3. @ResponseBody를 사용하여 Client에게 Json 형태로 데이터를 반환한다.

## **[ @Controller 예제 코드 ]**

```java
package com.mang.blog.application.user.controller;

import com.mang.blog.application.user.model.UserVO;
import com.mang.blog.application.user.service.UserService;
import org.springframework.stereotype.Controller;
import org.springframework.ui.Model;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.GetMapping;
import org.springframework.web.bind.annotation.PostMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.ResponseBody;

import javax.annotation.Resource;

@Controller
@RequestMapping("/user")
public class UserController {

    @Resource(name = "userService")
    private UserService userService;

    @PostMapping(value = "/retrieveUserInfo")
    public @ResponseBody UserVO retrieveUserInfo(@RequestBody UserVO userVO){
        return userService.retrieveUserInfo(userVO);
    }

    @GetMapping(value = "/userInfoView")
    public String userInfoView(Model model, @RequestParam(value = "userName", required = true) String userName){
        UserVO userVO = userService.retrieveUserInfo(userName);
        model.addAttribute("userInfo", userVO);
        return "/user/userInfoView";
    }

}
```

## **2. @RestController(Spring Restful Controller)**

---

## **[ RestController ]**

@RestController는 Spring MVC Controller에 @ResponseBody가 추가된 것입니다. 당연하게도 RestController의 주용도는 Json 형태로 객체 데이터를 반환하는 것입니다. 스프링부트프로젝트에서 API 서버로 활용할 때 많이 쓰임.

![https://blog.kakaocdn.net/dn/7bceC/btqx8K6BbxE/LVs4KK74mUj9CZ70uHTsjK/img.png](https://blog.kakaocdn.net/dn/7bceC/btqx8K6BbxE/LVs4KK74mUj9CZ70uHTsjK/img.png)

1. Client는 URI 형식으로 웹 서비스에 요청을 보낸다.
2. Mapping되는 Handler와 그 Type을 찾는 DispatcherServlet이 요청을 인터셉트한다.
3. RestController는 해당 요청을 처리하고 데이터를 반환한다.

# **[ @RestController 예제 코드 ]**

```
package com.mang.blog.application.user.controller;

import com.mang.blog.application.user.model.UserVO;
import com.mang.blog.application.user.service.UserService;
import org.springframework.http.HttpStatus;
import org.springframework.http.ResponseEntity;
import org.springframework.web.bind.annotation.RequestBody;
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RequestMethod;
import org.springframework.web.bind.annotation.RestController;

import javax.annotation.Resource;

@RestController
@RequestMapping("/user")
public class UserController {

    @Resource(name = "userService")
    private UserService userService;

    @PostMapping(value = "/retrieveUserInfo1")
    public UserVO retrieveUserInfo1(@RequestBody UserVO userVO){
        return userService.retrieveUserInfo(userVO);
    }

    @PostMapping(value = "/retrieveUserInfo2")
    public ResponseEntity<UserVO> retrieveUserInfo2(@RequestParam(value = "userName", required = true) String userName){
        userVO = userService.retrieveUserInfo(userName);

        if(userVO == null){
            return new ResponseEntity<>(HttpStatus.NOT_FOUND);
        }

        return new ResponseEntity<>(userVO, HttpStatus.OK);
    }

    @PostMapping(value = "/retrieveUserInfo3")
    public ResponseEntity<UserVO> retrieveUserInfo3(@RequestParam(value = "userName", required = true) String userName){
        return Optional.ofNullable(userService.retrieveUserInfo(userName))
                .map(userVO -> new ResponseEntity<>(userVO, HttpStatus.OK))
                .orElse(new ResponseEntity<>(HttpStatus.NOT_FOUND));
    }
}
```

출처 : [https://mangkyu.tistory.com/49](https://mangkyu.tistory.com/49)
