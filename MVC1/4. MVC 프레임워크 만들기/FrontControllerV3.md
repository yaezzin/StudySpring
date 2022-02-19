# # 4-4. FrontControllerV3

## 💦 V2의 문제

### 1. 서블릿 종속성

컨트롤러 입장에서 HttpServletRequest와 HttpServletResponse를 모두 파라미터로 받을 필요가 없음!

* MemberFormControllerV2를 보면 response는 쓰이지도 않는 중!

```java

public class MemberFormControllerV2 implements ControllerV2 {
    @Override
    public MyView process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        return new MyView("/WEB-INF/views/new-form.jsp");
    }
}

```
* request 파라미터는 자바의 Map으로 대신 넘기면 지금 구조에서는 컨트롤러가 서블릿 기술을 몰라도 동작 가능  
* request 객체를 모델로 사용하지 말고 별도의 모델 객체를 만들어서 반환하자!

### 2. 뷰 이름 중복
V2에서는 ```/WEB-INF/views/new-form.jsp 처럼``` 모든 물리 위치를 다 작성해야 했음  
하지만 V3에서는 new-form처럼 ```뷰의 논리 이름```만 반환하도록 단순화 하자! 👉 viewResolver가 수행  
👉 논리 이름만 반환하면 향후에 뷰의 폴더 위치가 함께 이동해도 프론트 컨트롤러만 바꾸면 됨!

## V3 Structure
```클라이언트의 HTTP 요청``` 👉 ```프론트 컨트롤러가 매핑 정보에 맞는 컨트롤러 호출``` 👉 ```컨트롤러가 ModelView 반환```  
👉 ```프론트 컨트롤러가 viewResolver 호출``` 👉 ```viewResolver가 MyView를 반환``` 👉 ```프론트컨트롤러가 render 호출``` 👉 ```HTML 응답```

( V2에서는 컨트롤러가 MyView를 반환었음 )

## ModelView
* 이전까지 컨트롤러에서 서블릿에 종속적인 HttpServletRequest를 사용했음
* Model도 request.setAttribute를 통해 데이터를 저장하고 뷰에 전달했음
* 이러한 종속성을 제거하기 위해 별도로 ModelView를 만들자!

``` java
@Getter @Setter
public class ModelView {
    private String viewName; //뷰의 이름
    
    // 모델은 Map으로 되어있으므로 컨트롤러에서 뷰에 필요한 데이터를 k,v 형태로 넣어주면 됨!
    private Map<String, Object> model = new HashMap<>();
    
    // 뷰를 렌더링하기 위한 모델 객체
    public ModelView(String viewName) {
        this.viewName = viewName;
    }
}
```

## ControllerV3

* 서블릿 기술을 전혀 사용하지 않는 컨트롤러!
* 응답 결과로 뷰 이름과 뷰에 전달한 모델 데이터를 포함하는 ModelView 객체 반환
``` java
public interface ControllerV3 {
    ModelView process(Map<String, String> paramMap);
}
```
## ControllerV3를 상속받은 컨트롤러들

#### MemberFormControllerV3
```java
public class MemberFormControllerV3 implements ControllerV3 {
      @Override
      public ModelView process(Map<String, String> paramMap) {
          return new ModelView("new-form");
      }
}
```

#### MemberSaveControllerV3

```java
public class MemberSaveControllerV3 implements ControllerV3 {
    private MemberRepository memberRepository = MemberRepository.getInstance();
    
    @Override
    public ModelView process(Map<String, String> paramMap) {
        String username = paramMap.get("username");
        int age = Integer.parseInt(paramMap.get("age"));
        
        Member member = new Member(username, age);
        memberRepository.save(member);
        
        ModelView mv = new ModelView("save-result");
        mv.getModel().put("member", member); //모델에 뷰에서 필요한 멤버 객체를 담고 반환!
        return mv;
    } 
}
```

#### MemberListControllerV3
```java
public class MemberListControllerV3 implements ControllerV3 {
    private MemberRepository memberRepository = MemberRepository.getInstance();
    
    @Override
    public ModelView process(Map<String, String> paramMap) {
        List<Member> members = memberRepository.findAll();
        
        ModelView mv = new ModelView("members");
        mv.getModel().put("members", members);
        return mv;
    } 
}
```

## FrontControllerV3

### V2와 차이점

#### service 메서드

```java
protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    String requestURI = request.getRequestURI();
    
    ControllerV3 controller = controllerMap.get(requestURI);
    
    if (controller == null){
        response.setStatus(HttpServletResponse.SC_NOT_FOUND);
        return; 
    }
    
    Map<String, String> paramMap = createParamMap(request);
    ModelView mv = controller.process(paramMap);
    
    String viewName = mv.getViewName();
    MyView view = viewResolver(viewName);
    
    view.render(mv.getModel(), request, response);
}
```

#### createParamMap 메서드

* request 객체에서 파라미터이름을 다 꺼내서 key는 paramName으로, value는 paramName의 값으로 Map으로 변환
* 해당 맵(paraMap)을 컨트롤러에 전달 : ```ModelView mv = controller.process(paramMap);```


```java
private Map<String, String> createParamMap(HttpServletRequest request) {
    Map<String, String> paramMap = new HashMap<>();
    request.getParameterNames().asIterator()
        .forEachRemaining(paramName 
            -> paramMap.put(paramName, request.getParameter(paramName)));
    return paramMap;
}
```

#### viewResolver 메서드
* 컨트롤러가 반환한 논리 뷰 이름을 실제 물리 뷰 경로로 변경

``` java
private MyView viewResolver(String viewName) {
    return new MyView("/WEB-INF/views/" + viewName + ".jsp");
}
```

## MyView (V3에서의 추가)

* ```render(model, request, response)``` : 모델 정보까지 받을 수 있는 render 메서드 추가
* ```modelToRequestAttribute```: 모델에 있는 값들을 다 꺼내서 request에 넣어줌! 👉 그 후 jsp forward

```java
public void render(Map<String, Object> model, HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
    modelToRequestAttribute(model, request);
    RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
    dispatcher.forward(request, response);
}
```

``` java
private void modelToRequestAttribute(Map<String, Object> model, HttpServletRequest request) {
    model.forEach((key, value) -> request.setAttribute(key, value));
    //모델에 있는 값을 다 꺼내서 request 에 다 넣어줌 그 후에 jsp forward
}
```










