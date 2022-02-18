# # 4-2. Ver.1

프론트 컨트롤러를 단계적으로 도입하기 위해 구조를 맞추고 점진적으로 리펙터링하자!

## V1 Structure

```클라이언트의 HTTP 요청``` 👉 ```FrontController가 URL 매핑 정보에서 컨트롤러 조회``` 👉 ```컨트롤러 호출``` 👉 ```컨트롤러에서 JSP forward```

## ControllerV1

* ControllerV1이라는 ```인터페이스```를 만들자!
* 서블릿과 비슷한 모양의 컨트롤러 인터페이스 도입
* 각 컨트롤러는 이 인터페이스를 상속받아서 내부를 구현해주어야 함
* 프론트 컨트롤러는 이 인터페이스를 호출하면 다형성 구현이 가능


``` java
public interface ControllerV1 {
    void process(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException;
}
```

## 인터페이스를 상속받아 내부를 구현한 각 컨트롤러들

각 컨트롤러는 프론트컨트롤러 도입 전후에 달라진게 없다!  
프론트 컨트롤러가 컨트롤러를 호출하면 컨트롤러 안에서 JSP를 forward!!

#### 📌 MemberFormControllerV1
``` java
public class MemberFormControllerV1 implements ControllerV1 {

    @Override
    public void process(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
        
        String viewPath = "/WEB-INF/views/new-form.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);// 컨트롤러에서 뷰로 이동할 떄 쓰는 메서드
        dispatcher.forward(request, response);
    }
}

```
#### 📌 MemberSaveControllerV1

``` java
public class MemberSaveControllerV1 implements ControllerV1 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @Override
    public void process(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
        
        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));

        Member member = new Member(username, age);

        memberRepository.save(member);

        request.setAttribute("member", member);

        String viewPath = "/WEB-INF/views/save-result.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
}
```

#### 📌 MemberListControllerV1

``` java
public class MemberListControllerV1 implements ControllerV1 {

    MemberRepository memberRepository = MemberRepository.getInstance();

    @Override
    public void process(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
        
        List<Member> members = memberRepository.findAll();

        request.setAttribute("members", members);

        String viewPath = "/WEB-INF/views/members.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
}
```

## ✨ FrontController

* ``` /front-controller/v1/* ```  : v1 하위의 어떤 url이 들어와도 이 프론트 컨트롤러 서블릿이 호출되도록 매핑!
* ``` controller.process(request, response)``` : 인터페이스의 process 메서드를 사용하자! 


``` java
@WebServlet(name = "frontControllerServletV1", urlPatterns = "/front-controller/v1/*")
public class FrontControllerServletV1 extends HttpServlet {

    private Map<String, ControllerV1> controllerMap = new HashMap<>();

    public FrontControllerServletV1() {
        // 2. 이러한 매핑 정보가 오면 해당되는 컨트롤러 객체를 생성하자!
        controllerMap.put("/front-controller/v1/members/new-form", new MemberFormControllerV1());
        controllerMap.put("/front-controller/v1/members/save", new MemberSaveControllerV1());
        controllerMap.put("/front-controller/v1/members", new MemberListControllerV1());
    }

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        System.out.println("FrontControllerServletV1.service");

        // 1. localhost8080뒤의 부분을 얻어옴 -> /front-controller/v1/members/
        String requestURI = request.getRequestURI();

        //2. 해당하는 uri를 MAP에서 꺼내서 해당하는 컨트롤러 객체를 생성
        ControllerV1 controller = controllerMap.get(requestURI);

        // 3. 컨트롤러가 없으면 404 반환
        if (controller == null) {
            response.setStatus(HttpServletResponse.SC_NOT_FOUND);
            return;
        }

        // 4. 생성된 컨트롤러의 process 메서드를 호출
        controller.process(request, response); // COntrollerV1
    }
}
```
