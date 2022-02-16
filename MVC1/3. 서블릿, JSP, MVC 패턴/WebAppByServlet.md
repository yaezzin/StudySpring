# # 3-2. by서블릿

## 1. MemberFormServlet

💡 [전송] 버튼을 누르면 ```/servlet/members/save``` 경로로 POST 

```java
@WebServlet(name = "memberFormServlet", urlPatterns = "/servlet/members/new-form")
public class MemberFormServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
        
        // Content-Type : text/html
        response.setContentType("text/html");
        response.setCharacterEncoding("utf-8");
        
        PrintWriter w = response.getWriter();
        
        w.write("<!DOCTYPE html>\n" +
                "<html>\n" +
                "<head>\n" +
                "    <meta charset=\"UTF-8\">\n" +
                "<title>Title</title>\n" +
                "</head>\n" +
                "<body>\n" +
                "<form action=\"/servlet/members/save\" method=\"post\">\n" +
                "    username: <input type=\"text\" name=\"username\" />\n" +
                "    age:      <input type=\"text\" name=\"age\" />\n" +
                " <button type=\"submit\">전송</button>\n" + "</form>\n" +
                "</body>\n" +
                "</html>\n");
    }
}
```

## 2. MemberSaveServlet

### 💡 동작 과정
* getParameter()를 통해서 파라미터를 조회 후 Member 객체를 생성  
* Member 객체를 MemberRepository에 저장(save 메서드)  
* Member 객체를 사용해 HTML을 동적으로 만듦  

참고 : ```getParameter()```의 반환 값은 항상 스트링!

```java
@WebServlet(name = "memberSaveServlet", urlPatterns = "/servlet/members/save")
public class MemberSaveServlet extends HttpServlet {

    //싱글톤이므로 new MemberRepository() 할 수 x
    private MemberRepository memberRepository = MemberRepository.getInstance();

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) 
        throws ServletException, IOException {
        
        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));
        // request.getParameter()의 응답 타입은 항상 string이므로 int로 변환해주어야함

        Member member = new Member(username, age);
        memberRepository.save(member);

        // Member 객체를 사용해서 결과 화면용 HTML을 동적으로 만들어서 응답한다.
        response.setContentType("text/html");
        response.setCharacterEncoding("utf-8");
        
        PrintWriter w = response.getWriter();
        
        w.write("<html>\n" +
                "<head>\n" +
                " <meta charset=\"UTF-8\">\n" + "</head>\n" +
                "<body>\n" +
                "성공\n" +
                "<ul>\n" +
                "    <li>id="+member.getId()+"</li>\n" +
                "    <li>username="+member.getUsername()+"</li>\n" +
                " <li>age="+member.getAge()+"</li>\n" + "</ul>\n" +
                "<a href=\"/index.html\">메인</a>\n" + "</body>\n" +
                "</html>");
    }
}
```

## 3. MemberFormServlet

### 💡 동작 과정
* memberRepository.findAll() 을 통해 모든 회원을 조회
* 회원 목록 HTML을 for 루프를 통해서 회원 수 만큼 동적으로 생성하고 응답

```java
@WebServlet(name = "memberListServlet", urlPatterns = "/servlet/members")
public class MemberListServlet extends HttpServlet {
    private MemberRepository memberRepository = MemberRepository.getInstance();
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {

        response.setContentType("text/html");
        response.setCharacterEncoding("utf-8");

        //모든 회원 조회
        List<Member> members = memberRepository.findAll();
        
        // 회원 목록 HTML을 for 루프를 통해서 회원 수 만큼 동적으로 생성하고 응답
        PrintWriter w = response.getWriter();
        w.write("<html>");
        w.write("<head>");
        w.write("    <meta charset=\"UTF-8\">");
        w.write("    <title>Title</title>");
        w.write("</head>");
        w.write("<body>");
        w.write("<a href=\"/index.html\">메인</a>");
        w.write("<table>");
        w.write("    <thead>");
        w.write("    <th>id</th>");
        w.write("    <th>username</th>");
        w.write("    <th>age</th>");
        w.write("    </thead>");
        w.write("    <tbody>");

        for (Member member : members) {
            w.write("    <tr>");
            w.write("        <td>" + member.getId() + "</td>");
            w.write("        <td>" + member.getUsername() + "</td>");
            w.write("           <td>" + member.getAge() + "</td>");
            w.write("    </tr>");
        }
        
        w.write("    </tbody>");
        w.write("</table>");
        w.write("</body>");
        w.write("</html>");
    }
}
```


### 🤔 HTML이 '동적'이란게 뭘까?

정적인 웹페이지는 서버에 미리 저장된 파일이 전달되는 반면 동적인 웹페이지는 가공처리 후 **생성**되어 전달되는 것임!!   
아래 소스코드의 html 부분에서 getId(), getUsername()과 같은 자바 코드를 넣어서 상황, 요청에 따라 상이한 웹페이지를 전달할 수 있음


### 💥 서블릿 단점

서블릿 덕분에 동적으로 원하는 HTML을 만들 수 있어서 화면이 계속 달라지는 회원의 저장 결과라던가, 회원 목록 같은 동적인 HTML을 통해 해결할 수 있음.  
하지만 코드에서 보듯 자바 코드로 HTML을 만들어 내는 것은 매우 복잡하고 비효율적이다!  
차라리 HTML 문서에 **동적으로 변경해야 하는 부분만 자바 코드를 넣을 수 있다면** 더 편리할 것이다.  
그래서 ```템플릿 엔진```이 등장~!
