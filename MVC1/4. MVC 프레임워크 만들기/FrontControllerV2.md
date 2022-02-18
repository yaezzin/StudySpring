# # 4-3. ver.2

## V1의 중복

* V1에서 모든 컨트롤러에서 뷰로 이동하는 부분에서 해당 코드가 중복된 것을 볼 수 있음
* V2에서는 중복을 개선해보자!

```java
String viewPath = "/WEB-INF/views/new-form.jsp";
  RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
  dispatcher.forward(request, response);

```
## V2 Structure

```클라이언트의 HTTP 요청``` 👉 ```FrontController가 URL 매핑정보에서 컨트롤러 조회``` 👉 ```컨트롤러 호출``` 
👉 ```컨트롤러가 MyView 반환``` 👉 ```render 호출``` 👉 ```JSP forward``` 👉 ```HTTP 응답```


## View 분리

각 컨트롤러는 더이상 JSP를 forward하는 역할을 더 이상하지 않고 대신 MyView 객체만 반환!
프론트 컨트롤러가 MyView를 

## MyView

* MyView의 render 메서드가 컨트롤러에서 뷰로 이동할 수 있도록 공통처리

```java
public class MyView {
    private String viewPath;
    
    public MyView(String viewPath) {
        this.viewPath = viewPath;
    }
    
    public void render(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
        
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
   }
}
```

## ControllerV2 

* V2의 인터페이스는 void가 아닌 MyView를 반환하도록 함

```java
public interface ControllerV2 {
      MyView process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException;
}
```

## 인터페이스를 상속받은 각 컨트롤러들

* 각 컨트롤러는 더이상 dispatcher.forward를 직접 생성해서 호출하지 않아도 됨!
* 단지 MyView 객체를 생성할 뿐!!

#### MemberFormContorllerV2

```java
public class MemberFormControllerV2 implements ControllerV2 {
    @Override
    public MyView process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        /*
        V1에서의 컨트롤러의 코드 -> v2 에서는 중복이 사라지고 경로만 넣어주면 됨!
        String viewPath = "/WEB-INF/views/new-form.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);// 컨트롤러에서 뷰로 이동할 떄 쓰는 메서드
        dispatcher.forward(request, response);
        */
        
        return new MyView("/WEB-INF/views/new-form.jsp");
    }
}

```

#### MemberSaveContorllerV2

```java
public class MemberSaveControllerV2 implements ControllerV2 {
    private MemberRepository memberRepository = MemberRepository.getInstance();

    @Override
    public MyView process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));

        Member member = new Member(username, age);
        memberRepository.save(member);
        request.setAttribute("member", member);

        return new MyView("/WEB-INF/views/save-result.jsp");
    }
}
```

#### MemberListContorllerV2

``` java
public class MemberListControllerV2 implements ControllerV2 {
    private MemberRepository memberRepository = MemberRepository.getInstance();

    @Override
    public MyView process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        List<Member> members = memberRepository.findAll();

        request.setAttribute("members", members);

        return new MyView("/WEB-INF/views/members.jsp");
    }
}
```

## FrontControllerV2

* 생성된 컨트롤러의 process 메서드를 호출
* V1과 대부분 동일하나 V2에서는 MyView를 반환해야하기 때문에 생성된 컨트롤러의 process 메서드 호출후 뷰를 렌더링!

```java
MyView view = controller.process(request, response);
view.render(request, response);
```
