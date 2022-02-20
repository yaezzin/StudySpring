# # 5-1. 스프링 전체 구조


<img width="433" alt="스크린샷 2022-02-20 오후 4 08 31" src="https://user-images.githubusercontent.com/97823928/154832401-1dcfe1e7-d7d6-4947-b809-5d7ce045358f.png">

## DispatcherServlet

* 우리가 직접 만든 프론트 컨트롤러가 DispatcherServlet !
* DispacherServlet 도 부모 클래스에서 HttpServlet 을 상속 받아서 사용하고, 서블릿으로 동작
* 스프링 부트는 DispacherServlet 을 서블릿으로 자동으로 등록하면서 모든 경로( urlPatterns="/" )에 대해서 매핑


### 전체 흐름

* 서블릿이 호출되면 HttpServlet이 제공하는 service() 메서드가 호출
* DispatcherServlet의 부모인 FrameworkServlet에 service()가 오버라이드 해두었고
* Framework.service()를 시작으로 DispatcherServlet.doDispatch()가 호출됨

```java
// 요약해서 일부분만 작성했습니다!
protected void doDispatch(HttpServletRequest request, HttpServletResponse response) throws Exception {
    // 1. 핸들러 조회
    HandlerExecutionChain mappedHandler = getHandler(processedRequest);
    
    // 2.핸들러 어댑터 조회-핸들러를 처리할 수 있는 어댑터
    HandlerAdapter ha = getHandlerAdapter(mappedHandler.getHandler());
    
    // 3. 핸들러 어댑터 실행 -> 4. 핸들러 어댑터를 통해 핸들러 실행 -> 5. ModelAndView 반환 
    ModelAndView mv = ha.handle(processedRequest, response, mappedHandler.getHandler());
    
    // 4. 뷰 렌더링 (processDiatchResult 메서드 내에 render 메서드가 존재)
    processDispatchResult(processedRequest, response, mappedHandler, mv, dispatchException);
}
```

### 동작 순서

1. ```핸들러 조회``` : 핸들러 매핑을 통해 요청 URL에 매핑된 핸들러(컨트롤러)를 조회
2. ```핸들러 어댑터 조회``` : 핸들러를 실행할 수 있는 핸들러 어댑터를 조회
3. ```핸들러 어댑터 실행``` : 핸들러 어댑터를 실행
4. ```핸들러 실행``` : 핸들러 어댑터가 실제 핸들러를 실행
5. ```ModelAndView 반환``` : 핸들러 어댑터는 핸들러가 반환하는 정보를 ModelAndView로 변환해서 반환
6. ```viewResolver 호출``` : 뷰 리졸버를 찾고 실행한다. JSP의 경우: InternalResourceViewResolver 가 자동 등록되고, 사용
7. ```View반환``` :
* 뷰리졸버는 뷰의 논리이름을 물리이름으로 바꾸고,렌더링 역할을 담당하는 뷰 객체를 반환
* JSP의 경우 InternalResourceView(JstlView) 를 반환하는데, 내부에 forward() 로직이 있음
8. ```뷰렌더링``` :뷰를 통해서 뷰를 렌더링


