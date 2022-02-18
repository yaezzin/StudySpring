# # 3-5. MVC패턴 적용

## 1. 회원 등록

### MvcMemberFormServlet
* MvcMemberFormServlet이 컨트롤러의 역할을 함 👉 서비스를 호출하는 역할 (고객 요청이 오면 service 메서드가 호출)
* viewPath를 /WEB-INF/views/new-form.jsp 로 지정하고 ``` getRequestDispathcher()``` 메서드에 넣어주면 해당 경로로 이동하게 됨
* getRequestDispathcher()는 컨트롤러에서 뷰로 이동할 수 있게 해줌
* ```dispatcher.forward()``` : 다른 서블릿이나 JSP로 이동할 수 있는데, 이는 서버 내부에서 다시 호출되는 것 (클라이언트에 응답 후 다시 호출되는 것x)
* ```/WEB-INF```의 경로 안에 JSP가 있으면 외부에서 직접 호출할 수 없고 **컨트롤러를 통해서만** 호출 가능

``` java
@WebServlet(name = "mvcMemberFormServlet", urlPatterns = "/servlet-mvc/members/new-form")
public class MvcMemberFormServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response)
        throws ServletException, IOException {
        
        String viewPath = "/WEB-INF/views/new-form.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    } 
}

```

## 2. 회원 저장

### MvcMemberSaveServlet

* request가 제공하는 ```serAttribute()```를 통해 request 객체에 데이터를 보관하여 뷰에 전달 가능함

``` java
@WebServlet(name = "mvcMemberSaveServlet", urlPatterns = "/servlet-mvc/members/save")
public class MvcMemberSaveServlet extends HttpServlet {

    MemberRepository memberRepository = MemberRepository.getInstance();

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response)
        throws ServletException, IOException {
        
        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));

        Member member = new Member(username, age);

        memberRepository.save(member);

        //model에 데이터를 보관
        request.setAttribute("member", member);

        String viewPath = "/WEB-INF/views/save-result.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
}

```

### save-result.jsp 일부분

*  <%= request.getAttribute("member")%> 로 모델에 저장한 member 객체를 꺼낼 수 있으나 넘 복잡해!
* JSP는 ```${}``` 문법을 제공하는데, 이 문법을 사용하면 request의 attribute에 담긴 데이터를 편하게 조회 가능


```jsp
<ul>
      <li>id=${member.id}</li>
      <li>username=${member.username}</li>
      <li>age=${member.age}</li>
</ul>
```

## 3. 회원 목록

* 이쯤 되면.. 뭔가 코드가 계속해서 반복되는 느낌을 받아야함..!

``` java
@WebServlet(name = "mvcMemberSaveServlet", urlPatterns = "/servlet-mvc/members")
public class MvcMemberListServlet extends HttpServlet {
    private MemberRepository memberRepository = MemberRepository.getInstance();

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
        
        List<Member> members = memberRepository.findAll();

        request.setAttribute("members", members);

        String viewPath = "/WEB-INF/views/members.jsp";
        RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
        dispatcher.forward(request, response);
    }
}

```

### members.jsp 일부분

* 모델에 담아둔 members를 JSP가 제공하는 taglib기능을 사용해서 반복하면서 출력
* <c:forEach> 이 기능을 사용하려면 ```<%@ taglib prefix="c" uri="http://java.sun.com/jsp/jstl/core"%>```선언하기!


``` jsp
 <c:forEach var ="item" items="${members}">
        <tr>
            <td>${item.id}</td>
            <td>${item.username}</td>
            <td>${item.age}</td>>
        </tr>
    </c:forEach>
```

## 📝 redirect 와 forward 차이

redirect는 실제 클라이언트(웹브라우저)에 응답이 나갔다가, 클라이언트가 그 경로로 다시 요청하는 것이므로 클라이언트가 인지 가능하며 URL 경로도 실제로 변경!
하지만 forward는 서버 내부에서 일어나는 호출이기 때문에 (함수 한번 호출 하듯!) 클라이언트가 전혀 인지하지 못함


## 👩‍💻 MVC 패턴을 적용하니까

비즈니스 로직과 뷰 로직이 확실히 **분리** 되었음 (컨트롤러와 뷰의 분리)  
만약 내가 웹 화면에서 버튼의 위치를 바꾸고 싶으면 jsp 파일에만 가서 고쳐주면 되고, 비즈니스 로직은 건들일 필요가 없음!  


## 💦 하지만 여전히 단점은 존재!

### 너무 많은 중복

* View로 이동하는 코드가 항상 호출되어야 하며, 공통처리해도 되지만 (인터페이처럼?) 여전히 메서드 호출은 계속 해줘야함
``` java
RequestDispatcher dispatcher = request.getRequestDispatcher(viewPath);
dispatcher.forward(request, response);
```

* viewPath의 /WEB-INF/views/ 부분이 계속 중복되며, .jsp가 아닌 타임리프같은 다른 뷰로 변경되면 전체 코드를 바꿔줘야 함
``` java
String viewPath = "/WEB-INF/views/new-form.jsp";
```
### 사용하지 않는 코드의 존재

* 현재 response는 사용하지 전혀 사용하지 않고 있으며, HttpServletRequest, HttpServletResponse는 테스트 코드 작성하기도 어려움

``` java
HttpServletRequest request, HttpServletResponse response
```

### 공통처리가 어려움

기능이 복잡해질 수록 컨트롤러에서 공통으로 처리하는 부분이 증가할 것임..  
공통 기능을 메서드로 뽑아내도 결국 해당 메서드를 항상 호출 해줘야하는데 이 호출 자체도 중복!!

👉 ``` 프론트 컨트롤러(Front Controller)``` 를 통해 컨트롤러 호출 전에 먼저 공통 기능을 처리해주자!
