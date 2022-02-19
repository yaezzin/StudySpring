# # 4-1. 프론트 컨트롤러 패턴

## 1. 특징

* 프론트 컨트롤러 서블릿 1개로 클라이언트 요청을 받아서, 프론트 컨트롤러가 요청에 맞는 컨트롤러를 찾아서 호출
* 결국, 프론트 컨트롤러를 제외한 나머지 컨트롤러는 서블릿은 사용하지 않아도 됨


## 2. 요약해서 보기 😎

## 🤖 V1

<img width="426" alt="스크린샷 2022-02-19 오후 11 24 18" src="https://user-images.githubusercontent.com/97823928/154804880-01b30125-0042-4de6-8d07-c4d8ee25736e.png">

### 핵심 : 프론트 컨트롤러 도입!

#### ControllerV1

```java
  public interface ControllerV1 {
      void process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException;
  }
```

* FrontControllerServlet 1개로 클라이언트의 요청에 맞는 컨트롤러를 찾아서 호출하자!
* 서블릿과 비슷한 모양의 컨트롤러 인터페이스 ControllerV1를 도입
* 기존 로직을 유지하되 각 컨트롤러는 인터페이스를 상속받아서 구현 


## 🤖 v2

<img width="427" alt="스크린샷 2022-02-19 오후 11 44 23" src="https://user-images.githubusercontent.com/97823928/154805734-e04c635d-a857-49c8-ab90-2afb33828906.png">

### 핵심 : 뷰 분리!

#### ControllerV2
``` java
public interface ControllerV2 {
    MyView process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException;
}
```

* 모든 컨트롤러에서 컨트롤러에서 뷰로 이동하는 부분에 중복이 존재
* ControllerV2는 V1과 다르게 ```MyView```를 반환

#### MyView

```java
public class MyView {
    private String viewPath;
    public MyView(String viewPath) {
        this.viewPath = viewPath; 
    }
    
    public void render(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
}
```

* MyView에서 dispatcher.forward()를 생성해서 호출하므로 각 컨트롤러에서 할 필요 없음!!
* 각 컨트롤러에서는 MyView에 viewPath만 담아서 반환해주면 됨

## 🤖 V3

<img width="425" alt="스크린샷 2022-02-19 오후 11 45 40" src="https://user-images.githubusercontent.com/97823928/154805787-890df7d3-73ad-4506-a275-143f2f1eeaae.png">

### 핵심 : 서블릿 종속성 제거 + 뷰의 논리 이름 반환! 

#### ControllerV3
``` java
public interface ControllerV3 {
    ModelView process(Map<String, String> paramMap);
}
```
* V3 컨트롤러 인터페이스는 request, response 같은 서블릿 기술이 사용되지 않음

#### ModelView

``` java
@Getter @Setter
public class ModelView {
    private String viewName;
    private Map<String, Object> model = new HashMap<>();
}
```
* 뷰의 이름과 뷰 렌더링을 위한 모델 객체를 포함
* 모델은 단지 Map이므로, 컨트롤러에서 뷰에 필요한 데이터를 모델에 put해주면 됨

#### ViewResolver

``` java
private MyView viewResolver(String viewName) {
    return new MyView("/WEB-INF/views/" + viewName + ".jsp");
}
```
* 각 컨트롤러에서 반환하는 뷰의 논리 이름 (ex. new-form, save-result, members)를 실제 물리적 뷰 경로로 변경 후
* 물리 경로를 담은 MyView를 반환


## 🤖 V4

<img width="421" alt="스크린샷 2022-02-19 오후 11 58 06" src="https://user-images.githubusercontent.com/97823928/154806233-8247e18c-79ec-45b7-adf2-6b5f2ee332c0.png">

### 핵심 : 좀 더 편리할 순 없나?

#### ControllerV4

``` java
public interface ControllerV4 {
    String process(Map<String, String> paramMap, Map<String, Object> model);
}
```

* V3와 달리 컨트롤러가 ModelView를 반환하지 않고 String 타입의 viewName만 반환
* 모델은 파라미터로 전달되기 때문에 viewName만 반환할 수 있음


## V5

<img width="424" alt="스크린샷 2022-02-20 오전 12 26 59" src="https://user-images.githubusercontent.com/97823928/154807319-46940ad9-5740-4ff7-a1c0-46a7a5a2d6b0.png">

### 핵심 : 컨트롤러 여러 개 쓸래!

``` java
public interface MyHandlerAdapter {
   boolean supports(Object handler);
   ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException;
}
```
* 핸들러 어댑터 추가 : 핸들러 어댑터가 프론트 컨트롤러 대신 컨트롤러를 호출 👉 좀 더 확장성 있게 설계 가능
* support() : 핸들러를 처리할 수 있는 어댑터인지 확인 
* handle()  : 어댑터를 호출


