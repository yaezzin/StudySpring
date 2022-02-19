# # 4-5. Ver.4

## V3 문제점

아래 코드를 보자.  
컨트롤러에서 ModelView 객체를 생성해서, 데이터를 넣고 반환하고.. 조금 귀찮다! 💢

``` java
ModelView mv = new ModelView("save-result");
mv.getModel().put("member", member);
return mv;
 ```
 
 ## V4 Structure
 
 V3에서 ModelView를 반환하던 것을 호출할때는 paramMap과 model을 파라미터로 주고 viewName만 반환하자!
 
 * V3 : 컨트롤러 호출 -> 모델뷰 반환
 * V4 : paramMap, model 호출 -> 뷰네임 반환
 
 #### ✨ 핵심 : 기존 구조에서 모델을 파라미터로 넘기고, 뷰의 논리 이름을 반환하자!
 
 
## ControllerV4

* model 객체가 파라미터로 전달되기 때문에 ModelView가 필요 없다!
* 그냥 뷰네임만 반환해주면 된다! 👉 반환값 String
 
``` java
public interface ControllerV4 {
    // paramMap뿐 아니라 model도 파라미터로 받아줌!!
    String process(Map<String, String> paramMap, Map<String, Object> model);
}
 ```
 
 ## ControllerV4를 상속받은 컨트롤러들
 
 #### MemberFormControllerV4
 
 진짜 단순하게 뷰의 논리 이름만 반환!!
 
 ```java
 public class MemberFormControllerV4 implements ControllerV4 {
    @Override
    public String process(Map<String, String> paramMap, Map<String, Object> model) {
        return "new-form";
    }
}
 ```
  #### MemberSaveControllerV4
 
 * 모델이 파라미터로 전달되기 때문에 모델을 직접 생성할 필요 없음!!
 
 ```java
 public class MemberSaveControllerV4 implements ControllerV4 {
     private MemberRepository memberRepository = MemberRepository.getInstance();
     
     @Override
     public String process(Map<String, String> paramMap, Map<String, Object> model) {
         //1. 파라미터에서 값 꺼내고
         String username = paramMap.get("username");
         int age = Integer.parseInt(paramMap.get("age"));
          
         // 2. 비즈니스 로직 수행해서
         Member member = new Member(username, age);
         memberRepository.save(member);
         
         // 3. 모델에 데이터 put
         model.put("member", member);
         return "save-result";
     } 
}
```

#### MemberListControllerV4
 
```java
 
public class MemberListControllerV4 implements ControllerV4 {
    private MemberRepository memberRepository = MemberRepository.getInstance();
    @Override
    public String process(Map<String, String> paramMap, Map<String, Object> model) {
        List<Member> members = memberRepository.findAll();
        model.put("members", members);
        return "members";
    }
}
```

## FrontControllerServletV4

#### service 메서드 일부

```java
Map<String, String> paramMap = createParamMap(request); 
Map<String, Object> model = new HashMap<>(); //추가
String viewName = controller.process(paramMap, model);
MyView view = viewResolver(viewName);
view.render(model, request, response);
 ```
