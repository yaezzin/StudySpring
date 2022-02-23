# # 6-6. HTTP 요청 파라미터 2

## @ModelAttribute

실제 개발 시 요청 파라미터를 받음 -> 필요한 객체 생성 -> 객체에 값을 넣어줌 이러한 과정을 거친다

```java
@RequestParam String username;
@RequestParam int age;
  
HelloData helloData = new HelloData();
helloData.setUsername(username);
helloData.setAge(age);
```
### @ModelrAttribute는 이 과정을 자동화 해준다!

```java
@ResponseBody
@RequestMapping("/model-attribute-v1")
public String ModelAttributeV1(@ModelAttribute HelloData helloData) {
        // @ModelAttribute를 사용하면 아래의 코드 생략가능
        // HelloData helloData = new HelloData();
        // helloData.setUsername(username);
        // helloData.setAge(age);

        log.info("username={}, age={}", helloData.getUsername(), helloData.getAge());
        return "ok";
    }
```

물론 요청 파라미터를 바인딩 받을 객체를 만들어 놔야 함! 

```
@Data
public class HelloData {
      private String username;
      private int age;
}
```

* @ModelAttribute가 있으면 먼저 HelloData 객체를 생성하고, 요청파라미터 이름으로 HelloData의 프로퍼티를 찾고 setter를 호출해 파라미터의 값을 바인딩함
* ex) 파라미터 이름이 username이면 setUsername() 메서드를 호출해서 값을 입력

```java
@ResponseBody
@RequestMapping("/model-attribute-v2")
public String ModelAttributeV2(HelloData helloData) {
    // 내가 직접 만든 클래스는 @ModelAttribute를 생략해도 자동으로 붙는다고 생각하자!
    log.info("username={}, age={}", helloData.getUsername(), helloData.getAge());
    return "ok";
}
```
* @ModelAttribute도 생략 가능하나 @RequestParam도 생략할 수 있어서 혼란이 발생할 수 있음...
* 그렇기 때문에 스프링은 다음과 같은 규칙을 적용!
  * String, int, Integer : @RequestParam
  * 나머지 : @ModelAttribute 

## 프로퍼티 ?
객체에 getUsername() , setUsername() 메서드가 있으면, 이 객체는 username 이라는 프로퍼티를 가지고 있음    
"프로퍼티(property)는 일부 객체 지향 프로그래밍 언어에서 필드(데이터 멤버)와 메소드 간 기능의 중간인 클래스 멤버의 특수한 유형이다."   
(출처: 위키 백과 ㅎㅎ)
