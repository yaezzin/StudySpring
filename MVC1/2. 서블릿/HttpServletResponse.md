# # 2-4.HttpServletResponse 

## 1. 역할


### HTTP 응답 메시지 생성 
* HTTP 응답코드 지정 :  
 
``` response.setStatus(HttpServletResponse.SC_OK) ``` 👉 **200**: 매직넘버로 쓰지말고 의미를 파악할 수 있게 하자!

* 헤더 생성 : 
``` java
response.setHeader("Content-Type", "text/plain;charset=utf-8");
response.setHeader("Cache-Control", "no-cache, no-store, must-revalidate");
response.setHeader("Pragma", "no-cache");
response.setHeader("my-header","hello");
```

* 바디 생성
``` java
PrintWriter writer = response.getWriter();
writer.println("~~");
```


### 편의 기능 제공
* Content-Type

``` java
//response.setHeader("Content-Type", "text/plain;charset=utf-8");와 동일

response.setContentType("text/plain");
response.setCharacterEncoding("utf-8");
```

* 쿠키
``` java
//Set-Cookie: myCookie=good; Max-Age=600;
//response.setHeader("Set-Cookie", "myCookie=good; Max-Age=600");

Cookie cookie = new Cookie("myCookie", "good");
cookie.setMaxAge(600); //600초
response.addCookie(cookie); //쿠키 객체를 만들어서 addCookie 메서드를 통해 응답객체에 넣어줌
```

* Redirect
``` java
 //response.setStatus(HttpServletResponse.SC_FOUND); 👉 Status Code : 302 (redirect)
 //response.setHeader("Location", "/basic/hello-form.html"); 
 response.sendRedirect("/basic/hello-form.html");  //한줄로 표현 가능
```

## 2. Http 응답 데이터

### 1. Html   
👉 HTTP 응답으로 HTML을 반환할 때는 **content-type을 text/html**로 지정해야 한다.

``` java
@WebServlet(name = "responseHtmlServlet", urlPatterns = "/response-html")
public class ResponseHtmlServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //HTTP 응답으로 HTML을 반환할때는 컨텐트 타입이 text/html 으로 지정해야 웹브라우저가 html을 정상적으로 렌더링
        response.setContentType("text/plain");
        response.setCharacterEncoding("utf-8");

        //서블릿으로 html을 렌더링할 때는 직접 작성해줘야 함
        PrintWriter writer = response.getWriter();
        writer.println("<html>");
        writer.println("<body>");
        writer.println("    <div>안녕?</div>");
        writer.println("</body>");
        writer.println("</html>");
    }
}

```

### 2. Json
👉 HTTP 응답으로 JSON을 반환할 때는 **content-type을 application/json**로 지정해야 한다.   
Jackson 라이브러리가 제공하는 ```objectMapper.writeValueAsString()```를 사용하면 객체를 JSON 문자로 변경할 수 있다.


```java
@WebServlet(name = "responseJsonServlet", urlPatterns = "/response-json")
public class ResponseJsonServlet extends HttpServlet {
    ObjectMapper objectMapper = new ObjectMapper();

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        //Content-Type : application/json
        response.setContentType("application/json");
        response.setCharacterEncoding("utf-8");

        // {"username":"kim", "age":20}
        HelloData helloData = new HelloData();
        helloData.setUserName("kim");
        helloData.setAge(20);

        String result = objectMapper.writeValueAsString(helloData); //객체를 Json 문자로 바꾸주는 메서드
        response.getWriter().write(result);
    }
}

```
