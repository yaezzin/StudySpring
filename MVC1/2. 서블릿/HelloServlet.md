# # 2-1. Hello Servlet

서블릿은 톰캣 같은 웹 애플리케이션 서버(WAS)를 직접 설치하고, 그 위에 서블릿 코드를 클래스 파일로 빌드해서 올린 후 톰캣 서버를 실행하면 된다.  
하지만 **스프링 부트는 톰캣 서버를 내장**하고 있으므로, 톰캣 서버 설치 없이 편리하게 서블릿 코드를 실행할 수 있다.


### 1. 소스코드

```java
@WebServlet(name = "helloServlet", urlPatterns = "/hello")
public class HelloServlet extends HttpServlet {    
   @Override
   protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
       String username = request.getParameter("username");
 
       response.setContentType("text/plain");
       response.setCharacterEncoding("utf-8"); //문자 인코딩 정보를 알려줌
       response.getWriter().write("hello" + username); 
    }
}
```

### 2. 동작원리


```@ServletComponentScan``` : main 함수에 이 애노테이션을 사용하면 스프링부트가 서블릿을 찾아서 자동으로 등록해줌

```@WebServlet``` : 서블릿 애노테이션 → name(이름)과 urlPatterns(url매핑)을 파라미터로 주자!  

```getParameter()``` : request.getParameter()를 통해 쿼리 파라미터를 쉽게 조회 가능   

```setContentType("text/plain")``` : 단순 문자를 보낸다는 뜻  

```getWriter()``` : http 메세지 body에 데이터가 들어감   

1) HTTP 요청을 통해 매핑된 URL이 호출되면 서블릿 컨테이너는 service 메서드를 실행  
2) WAS가 request 객체를 만들어서 서블릿을 호출하여 request response를 넘겨줌  
3) response 객체 정보를 가지고 HTTP 응답 메세지를 만들어서 반환
4) http://localhost:8080/hello?username=world URL을 호출하면 "hello world"가 출력됨을 볼 수 있다!
