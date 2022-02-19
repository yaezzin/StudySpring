# #4-6. Ver.5

## V4 문제점

하나의 프로젝트 안에서 다양한 컨트롤러의 종류를 사용하고 싶은 상황이라고 가정해보자!  

🧑‍💻 : 나는 ControllerV4로 개발하고 싶어!   
🗣 : ControllerV3..가 좋은데..

### 하지만!

```private Map<String, ControllerV4> controllerMap = new HashMap<>();``` 

FrontController에서 이러한 코드는 한가지 방식의 컨트롤러 인터페이스만 사용가능하며 호환이 불가능  

#### ✨ ``` 어댑터 패턴``` 을 사용하자

## V5 Structure

이제는 FrontController가 곧 바로 컨트롤러(= 핸들러)를 호출할 수 없다!!  
반드시 ```핸들러 어댑터```를 거쳐야 함 (우리가 110V를 사용하고 싶을 때 110V로 변환하는 어댑터를 끼워서 쓰니까.. )  
핸들러 어댑터 덕분에 **다양한 종류의 컨트롤러**를 호출할 수 있음!!

<img width="438" alt="스크린샷 2022-02-19 오후 4 43 35" src="https://user-images.githubusercontent.com/97823928/154791928-87a62431-e2ee-4804-a9d6-615f4f18ee28.png">

## MyHandlerAdapter

``` java
public interface MyHandlerAdapter {
    boolean supports(Object handler);

    ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler)
        throws ServletException, IOException;
}
```

📌 ```support()```
 * FrontController가 핸들러 매핑 정보에서 컨트롤러를 찾아옴  
 * 핸들러 어댑터 목록을 조회해서 컨트롤러를 처리할 수 있는 어댑터를 가져옴  
 * 이때 어댑터가 해당 컨트롤러를 처리할 수 있는 지 T/F로 반환  

📌 ```handle()```
 * 핸들러를 호출해주고, ModelView 를 반환



## FrontControllerServletV5

### 

### 전체 코드
```java
@WebServlet(name = "frontControllerServletV5", urlPatterns = "/front-controller/v5/*")
public class FrontControllerServletV5 extends HttpServlet {

    private final Map<String, Object> handlerMappingMap = new HashMap<>();
    private final List<MyHandlerAdapter> handlerAdapters = new ArrayList<>();

    public FrontControllerServletV5() {
        initHandlerMappingMap();
        initHandlerAdapters();
    }
    
    // 핸들러 매핑 정보
    private void initHandlerMappingMap() {
        handlerMappingMap.put("/front-controller/v5/v3/members/new-form", new MemberFormControllerV3());
        handlerMappingMap.put("/front-controller/v5/v3/members/save", new MemberListControllerV3());
        handlerMappingMap.put("/front-controller/v5/v3/members", new MemberListControllerV3());
    }
    
    private void initHandlerAdapters() {
        handlerAdapters.add(new ControllerV3HandlerAapter());
    }

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        
        // 1. 핸들러 찾아와! : 매핑정보를 확인하고 핸들러 가져옴
        Object handler = getHandler(request);

        if (handler == null) {
            response.setStatus(HttpServletResponse.SC_NOT_FOUND);
            return;
        }

        // 2. 핸들러 어댑터 찾아와!
        MyHandlerAdapter adapter = getHandlerAdapter(handler);

        // 3. 핸들러 어댑터가 핸들러 호출 -> handle 메서드 안에서 컨트롤러 호출해서 모델뷰 반환
        ModelView mv = adapter.handle(request, response, handler);

        // 4. 뷰 리졸버 호출을 위해 viewName 얻어 오기
        String viewName = mv.getViewName();
        
        // 5. MyView 반한
        MyView view = viewResolver(viewName);
        
        //6. 렌더링
        view.render(mv.getModel(), request, response);
    }

    private Object getHandler(HttpServletRequest request) {
        String requestURI = request.getRequestURI();
        return handlerMappingMap.get(requestURI);
    }

    private MyHandlerAdapter getHandlerAdapter(Object handler) {
        for (MyHandlerAdapter adapter : handlerAdapters) {
            if (adapter.supports(handler)) {
                //어댑터가 핸들러를 지원하면
                return adapter;
            }
        }
        throw new IllegalArgumentException("hander adapter를 찾을 수 없습니다.");
    }

    private MyView viewResolver(String viewName) {
        return new MyView("/WEB-INF/views/" + viewName + ".jsp");
    }
}
``` 
