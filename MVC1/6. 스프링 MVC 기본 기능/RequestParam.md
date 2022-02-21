# 6-4. HTTP 요청 파라미터

## HTTP 요청 데이터 전달 방법

### 📌 GET - 쿼리 파라미터
* /url?username=hello&age=20
* 메시지 바디 없이, **URL의 쿼리 파라미터에 데이터를 포함해서 전달** 
* 예) 검색, 필터, 페이징등에서 많이 사용하는 방식

### 📌 POST - HTML Form
* content-type: application/x-www-form-urlencoded
* **메시지 바디에 쿼리 파리미터 형식으로 전달** 
* username=hello&age=20 
* 예) 회원 가입, 상품 주문, HTML Form 사용

### 📌 HTTP message body에 데이터를 직접 담아서 요청 
* HTTP API에서 주로 사용, JSON, XML, TEXT 데이터 형식은 주로 JSON 사용
* POST, PUT, PATCH


## HTTP 요청 파라미터 조회 

1. ```request.getParameter()``` : HttpServletRequest가 제공하는 방식으로 조회해보기!
* GET 쿼리 파리미터 전송 방식, POST HTML Form 전송 방식 둘다 request.getParameter()로 조회 가능
* ```http://localhost:8080/request-param-v1?username=hello&age=20```

```java
@Slf4j
@Controller
public class RequestParamController {

    @RequestMapping("/request-param-v1")
    public void requestParamV1(HttpServletRequest request, HttpServletResponse response) throws IOException {
        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age")); 
        //request.getParameter는 string으로 반환됨 👉 int 타입으로 바꿔주자!

        log.info("username={}, age={}", username, age); 
        response.getWriter().write("ok");
    }
}
```

#### @RequestParam
