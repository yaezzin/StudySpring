# # 6-2. 요청 매핑

## 요청 매핑

요청 매핑은 클라이언트의 요청이 왔을 때 ```어떤 컨트롤러가 호출```될 것인지를 의미함  
MappingController 클래스를 점진적으로 살펴보자

### MappingController 
``` java
@RestController
@Slf4j
public class MappingController {
    @RequestMapping(value = "/hello-basic")
    public String helloBasic() {
        log.info("helloBasic");
        return "ok";
    }
}
```

* @RequestMapping은 해당 url(/hello-basic)이 오면 메서드를 호출함
* @RequestMapping은 method 속성으로 HTTP 메서드를 지정하지 않으면 메서드와 무관하게 호출됨  
    
   
### HTTP 메서드 매핑

``` java
@RequestMapping(value = "/mapping-get-v1", method = RequestMethod.GET)
public String mappingGetV1() {
    log.info("mappingGetV1");
    return "ok";
}
```

* method = RequestMethod.XXX
* GET, HEAD, POST, PUT, PATCH, DELETE 가능
* 만약 여기에 POST 요청을 하면 스프링 MVC는 ```HTTP 405 상태코드(Method Not Allowed)```를 반환


### HTTP 메서드 매핑 축약

```java
@GetMapping(value = "/mapping-get-v2")
public String mappingGetV2() {
    log.info("mapping-get-v2");
    return "ok";
}
```
* method = RequestMethod.XXX를 애노테이션으로 편리하게 사용가능! + 더 직관적
* ```@GetMapping```, ```@PostMapping```, ```@PutMapping```, ```@DeleteMapping```, ```@PatchMapping```


### PathVariable(경로 변수)

``` java
@GetMapping("/mapping/{userId}")
public String mappingPath(@PathVariable("userId") String data) {
   log.info("mappingPath userId={}", data);
   return "ok";
}
```
#### ✨ @PathVariable ✨
* 최근 HTTP API는 리소스 경로에 식별자를 넣는 스타일을 선호
* 실행 : ```http://localhost:8080/mapping/userA```
* @PathVaribale의 이름과 파라미터 이름이 같으면 생략 가능 -> @PathVariable String userId


### PathVariable 사용 - 다중
```java
@GetMapping("/mapping/users/{userId}/orders/{orderId}")
public String mappingPath(@PathVariable("userId") String data,
                          @PathVariable Long orderId) {
    log.info("mapping Path userId ={}, orderId = {}", data, orderId);
    return "ok";
}
```
* PathVariable 여러개 사용가능!
* users안의 userA , userA가 주문한 orders의 100번 이런식!!
* 실행 : ```http://localhost:8080/mapping/users/userA/orders/100```

### 특정 파라미터 조건 매핑
```java
 /**
   * 파라미터로 추가 매핑
   * params="mode",
   * params="!mode"
   * params="mode=debug"
   * params="mode!=debug" (! = )
   * params = {"mode=debug","data=good"}
   */

@GetMapping(value = "/mapping-param", params = "mode=debug")
public String mappingParam() {
    log.info("mappingParam");
    return "ok";
}
```
* 잘 사용하진 않지만 조건을 추가할 수 있음
* ex. /mapping-param?mode=debug 이렇게 파라미터 정보를 주면 mode가 debug일 때만 호출됨!
* 실행 : ```http://localhost:8080/mapping-param?mode=debug```

### 특정 헤더 조건 매핑

```java
@PostMapping(value = "/mapping-consume", consumes = "application/json") //MediaType.APPLICATION_JSON_VALUE
public String mappingConsumes() {
    log.info("mappingConsumes");
    return "ok";
}
```

### 미디어 타입 조건 매핑 - HTTP 요청 Content-Type, consume


``` java
@PostMapping(value = "/mapping-consume", consumes = "application/json")
public String mappingConsumes() {
    log.info("mappingConsumes");
    return "ok";
}
```

* 헤더의 컨텐트 타입이 json일때만 호출 👉 consumes 사용
* consumes = MediaType.APPLICATION_JSON_VALUE 으로 더 직관적으로 표현 가능


### 미디어 타입 조건 매핑 - HTTP 요청 Accept, produce

``` java
@PostMapping(value = "/mapping-produce", produces = "text/html")
public String mappingProduces() {
    log.info("mappingProduces");
    return "ok";
}
```
* http 요청의 accept 헤더를 기반으로 매핑
* 맞지 않으면 HTTP 406 상태코드(Not Acceptable)을 반환

