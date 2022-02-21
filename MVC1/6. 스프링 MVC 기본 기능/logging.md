# # 6-1. 로깅

## 로그 선언

1. ```private Logger log = LoggerFactory.getLogger(getClass());```
2. ```private static final Logger log = LoggerFactory.getLogger(~.class);```
3. ```@Slf4j``` : 제일 간단한 방법

## 로그 레벨 설정

```application.properties``` 에 다음과 같이 설정해주자

```java
#전체 로그 레벨 설정(기본 info) 
logging.level.root=info

#hello.springmvc 패키지와 그 하위 로그 레벨 설정 
logging.level.hello.springmvc=debug
```

### 📌 로그 레벨
```TRACE``` > ```DEBUG``` > ```INFO``` > ```WARN``` > ```ERROR```

* 개발 서버는 debug, 운영 서버는 info 출력하는 것이 좋다!


## 소스 코드
```java
@RestController
@Slf4j
public class LogTestController {
    @GetMapping("/log-test")
    public String logTest() {
    
        String name = "Spring";
        System.out.println("name = " + name);

        //로그를 찍을 때 레벨을 정할 수 있음
        log.trace("trace log = {}", name);
        log.debug("debug log = {}", name);
        log.info("info log = {}", name);
        log.warn("warn log = {}", name);
        log.error("error log = {}", name);

        return "ok";
    }
}
```

### 출력 결과
```
name = Spring  
2022-02-20 16:48:05.337  INFO 1812 --- [nio-8080-exec-1] hello.springmvc.basic.LogTestController  : info log = Spring
...
```
#### 👉 시간, 로그 레벨, 프로세스 ID, 쓰레드명, 클래스명, 로그 메세지 

### 장점

* 쓰레드 정보, 클래스 이름 같은 부가 정보를 함께 볼 수 있고, 출력 모양을 조정할 수  있음
* 로그 레벨에 따라 개발 서버에서는 모든 로그를 출력하고, 운영서버에서는 출력하지 않는 등 로그를 상황에 맞게 조절할 수 있음
* 시스템 아웃 콘솔에만 출력하는 것이 아니라, 파일이나 네트워크 등, 로그를 별도의 위치에 남길 수 있음
* 특히 파일로 남길 때는 일별, 특정 용량에 따라 로그를 분할하는 것도 가능
* 성능도 일반 System.out보다 좋음 (내부 버퍼링, 멀티 쓰레드 등등) 그래서 실무에서는 꼭 로그를 사용!

## System.out.prinln()을 쓰면 안되는 이유 ? 🤔

//여기 다시 듣기!

## @RestController
#### @Controller와의 차이

```@Controller```는 반환값이 스트링이면 뷰 이름으로 인식되므로 **뷰를 찾고 뷰를 렌더링 함**    
반면 ```@RestController```는 **HTTP 메시지 바디에 바로 스트링이 입력 됨**





