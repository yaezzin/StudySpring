# # 4-7.add V4 to V5

🙌 이번에는 FrontControllerServlet5에 ControllerV4 기능도 추가해보자!

## FrontControllerServlet5

* 핸들러 매핑에 컨트롤러4를 사용하는 컨트롤러를 추가
```java
private void initHandlerMappingMap() {
    handlerMappingMap.put("/front-controller/v5/v3/members/new-form", new MemberFormControllerV3());
    handlerMappingMap.put("/front-controller/v5/v3/members/save", new MemberListControllerV3());
    handlerMappingMap.put("/front-controller/v5/v3/members", new MemberListControllerV3());

     // v4 추가
    handlerMappingMap.put("/front-controller/v5/v4/members/new-form", new MemberFormControllerV4());
    handlerMappingMap.put("/front-controller/v5/v4/members/save", new MemberListControllerV4());
    handlerMappingMap.put("/front-controller/v5/v4/members", new MemberListControllerV4());
}
```

* 해당 컨트롤러를  처리할 수 있는 어댑터 추가

```java
private void initHandlerAdapters() {
    handlerAdapters.add(new ControllerV3HandlerAdapter());
    handlerAdapters.add(new ControllerV4HandlerAdapter()); // v4 추가
}
 ```
 
## ControllerV4HandlerAdapter

V4의 경우 뷰네임을 반환했었는데, handle 메서드는 모델뷰 객체를 반환해줘야 함
-> 어댑터가 뷰 이름을 모델뷰로 만들어서 형식을 맞추어 준다!!!

```java
@Override
public ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException {
    // handler를 ControllerV4로 캐스팅  
    ControllerV4 controller = (ControllerV4) handler;

    Map<String, String> paramMap = createParamMap(request)
    Map<String, Object> model = new HashMap<>();

    //v4는 뷰네임을 반환하기 때문에 뷰네임을 가지고 모델뷰를 만들고 모델까지 값을 세팅해서 넣은 후 모델뷰 반환
    String viewName = controller.process(paramMap, model);

    ModelView mv = new ModelView(viewName); //뷰 세팅
    mv.setModel(model); //모델세팅
    return mv;
}    
```

## 정리 !

FrontController는 핸들러 어댑터 인터페이스에만 의존하기 때문에 구현클래스가 어떤 것이 들어와도 코드의 구조를 바꿔줄 필요가 없음!  
단순히 컨트롤러와 어댑터만 추가해주면 됨  
만약 handlerMapping.put(~) 코드만 밖에서 주입하도록 바꾸어준다면 ```OCP(Open-Closed Principle)```를 완벽히 지키는 구조!!




