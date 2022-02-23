# # 6-4. HTTP 요청 - 기본, 헤더 조회

컨트롤러가 지원하는 파라미터를 살펴보자!

* ```HttpServletRequest```
* ```HttpServletResponse```
* ```HttpMethod ```
* ```Locale```
* ```@RequestHeader MutiValueMap<String, String> headerMap``` : 모든 HTTP 헤더를 MultiValueMap 형식으로 반환   
* ```@RequestHeader("~") ```: 특정 HTTP 헤더 조회 가능
  * required : 필수 값 여부
  * defaultValue : 기본 값 속성
* ```CookieValue(value = "~", required = T/F)```
  * required : 필수값 여부
  * defaultValue : 기본 값 속성 


```java
@RestController
@Slf4j
public class RequestHeaderController {
    @RequestMapping("/headers")
    public String headers(HttpServletRequest request,
                          HttpServletResponse response,
                          HttpMethod httpMethod,
                          Locale locale,
                          @RequestHeader MultiValueMap<String,String> headerMap,
                          @RequestHeader("host") String host,
                          @CookieValue(value = "myCookie", required = false) String cookie){

        log.info("request={}", request);
        log.info("response={}", response);
        log.info("httpMethod={}", httpMethod);
        log.info("locale={}", locale);
        log.info("headerMap={}", headerMap);
        log.info("header host={}", host);
        log.info("myCookie={}", cookie);

        return "ok";
    }
}
```

## MultiValueMap

* 하나의 키에 여러 값을 받을 수 있음
* ex ) **keyA=value1&keyA=value2**

```java
MultiValueMap<String, String> map = new LinkedMultiValueMap();
map.add("keyA", "value1");
map.add("keyA", "value2");

List<String> values = map.get("keyA")
```
