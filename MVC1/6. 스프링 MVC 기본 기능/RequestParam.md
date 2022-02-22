# 6-5. HTTP 요청 파라미터

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

GET 쿼리 파리미터 전송 방식이든, POST HTML Form 전송 방식이든 둘다 형식이 같으므로 구분없이 조회할 수 있음  
여러가지 방법을 알아보자 🙊

## HttpServletRequest
* ```request.getParameter()``` : HttpServletRequest가 제공하는 방식으로 조회해보기!
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

## ✨ @RequestParam

```java
@ResponseBody
@RequestMapping("/request-param-v2")
public String requestParamV2(@RequestParam("username") String memberName,
                             @RequestParam("age") int memberAge) {
    log.info("username={}, age={}", memberName, memberAge);
    return "ok";
}
```
* 파라미터 이름(ex. username, age)으로 바인딩
* @RequestParam("username") -> request.getParameter("username")과 동일하다 생각하자!


```java
@ResponseBody
@RequestMapping("/request-param-v3")
public String requestParamV3(@RequestParam String username,
                             @RequestParam int age) {
    log.info("username={}, age={}", username, age);
    return "ok";
}
```

* HTTP 파라미터 이름이 변수 이름과 같으면 ("~") 생략 가능

```java
@ResponseBody
@RequestMapping("/request-param-v4")
public String requestParamV4(String username, int age) {
    log.info("username={}, age={}", username, age);
    return "ok";
}
```
* String, int, Integer와 같은 단순 타입이면 @RequestParam도 생략 가능하다
* 하지만 이렇게 애노테이션을 완전히 생략하는 것보다 '요청 파라미터에서 데이터를 읽는다'라는 것을 직관적으로 보여주기 위해 @RequestParam을 쓰는 것이 바람직!

## 파라미터 필수 여부

```java
@ResponseBody
@RequestMapping("/request-param-required")
public String requestParam(@RequestParam(required = true) String username,
                           @RequestParam(required = false) Integer age) {
    log.info("username={}, age={}", username, age);
    return "ok";
}
```
* required = true가 디폴트값
* required = true : 무조건 이 파라미터가 있어야하고, 없으면 bad request(400) 반환
* java 에서 int(기본형)는 null을 넣을 수 없기 때문에 required=false더라도 500 에러가남
* int -> Integer로 바꾸어주어야함
* nill과 ""는 다르다! : required?username=  < 이렇게만 하면 빈 공백이 들어가서 ok 출력 됨


```java
@ResponseBody
@RequestMapping("/request-param-default")
public String requestParamDefault(@RequestParam(defaultValue = "guest") String username,
                                  @RequestParam(defaultValue = "-1") int age) {
    log.info("username={}, age={}", username, age);
    return "ok";
}
```
* defaultValue = "~" 는 파라미터에 값이 안들어오면 해당 값으로 설정해주겠다는 뜻!
* required = false로 설정해놓아도 값이 들어가기 때문에 Integer 대신 int로 설정해도 됨
* 하지만 이미 기본 값이 있기 때문에 required는 의미가 없긴 함
* required와 달리 defaultValue는 빈 공백의 경우에도 설정한 기본값이 적용됨

## 파라미터를 Map으로 조회
```java
@ResponseBody
@RequestMapping("/request-param-map")
public String requestParamMap(@RequestParam Map<String, Object> paramMap) {
    log.info("username={}, age={}", paramMap.get("username"), paramMap.get("age"));
    return "ok";
}
```
* 파라미터의 값이 2개 이상이라면 MultiValueMap을 사용 key = [value1, value2, ...]



