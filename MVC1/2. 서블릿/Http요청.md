# # 2-3. Http 요청

## 1. Http 요청 메세지를 통한 데이터 전달 방법

#### 1. GET - 쿼리 파라미터
* /url?username=hello&age=20  
* 메시지 바디 없이, **URL의 쿼리 파라미터**에 데이터를 포함해서 전달  
*  예) 검색, 필터, 페이징등에서 많이 사용하는 방식

#### 2. POST - HTML Form
* content-type: application/x-www-form-urlencoded  
* **메시지 바디**에 **쿼리 파리미터** 형식으로 전달 username=hello&age=20   
* 예) 회원 가입, 상품 주문, HTML Form 사용

#### 3.HTTP message body에 데이터를 직접 담아서 요청 
* **HTTP API**에서 주로 사용
* 데이터 형식은 주로 JSON 사용 
* HTTP 메서드는 POST, PUT, PATCH를 


## 2. GET - 쿼리 파라미터

👩‍💻 가정 : ```http://localhost:8080/request-param?username=hello&age=20```

#### 📌 전체 파라미터 조회

```java
request.getParameterNames().asIterator()
                .forEachRemaining(paramName -> System.out.println(
                        paramName + "=" + request.getParameter(paramName)));
```

#### 📌 단일 파라미터 조회

```String username = request.getParameter("username");```

#### 📌 단일 파라미터에 복수의 값이 존재할 때

```java
String[] usernames = request.getParameterValues("username");
for (String name : usernames) {
    System.out.println("username: " + name);
}
```

## 3. POST HTML Form

```request.getParameter()```는 get-쿼리 파라미터의 데이터와 HTML Form 방식의 데이터 모두 구분 없이 조회할 수 있다!  
클라이언트(웹 브라우저) 입장에서는 두 방식에 차이가 있지만, 서버 입장에서는 둘의 형식이 동일하므로!!

즉,```request.getParameter() 는 GET URL 쿼리 파라미터 형식도 지원하고, POST HTML Form 형식도 둘 다 지원한다.```


#### 🔔 참고  
content-type은 **HTTP 메시지 바디**의 데이터 형식을 지정한다.  
GET URL 쿼리 파라미터 형식으로 클라이언트에서 서버로 데이터를 전달할 때는 HTTP 메시지 바디를 사용하지 않기 때문에 content-type이 없다.  
반면, POST HTML Form 형식으로 데이터를 전달하면 HTTP 메시지 바디에 해당 데이터를 포함해서 보내기 때문에   
바디에 포함된 데이터가 어떤 형식인지 **content-type을 꼭 지정**해야 한다.   
이렇게 폼으로 데이터를 전송하는 형식을 **application/x-www-form-urlencoded** 라 한다.  


## 4. API 메시지 바디

HTTP 메세지 바디에 ```데이터를 직접 실어서``` 서버에 전달하는 방식

#### 📌 텍스트 메세지를 HTTP 메세지 바디에 담아서 전송  

```java
@WebServlet(name = "requestBodyStringServlet", urlPatterns = "/request-body-string")
public class RequestBodyStringServlet extends HttpServlet {
    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
          ServletInputStream inputStream = request.getInputStream();
          String messageBody = StreamUtils.copyToString(inputStream,StandardCharsets.UTF_8);
          System.out.println("messageBody = " + messageBody);
          response.getWriter().write("ok");
    } 
}
```

```getInputStream()```: 메세지 바디의 내용을 **바이트코드**로 얻을 수 있음 but! string으로 바꿔줘야함  
```StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8)``` : 바이트 코드에서 문자로 변환할 떄는 어떤 인코딩인지 알려주어야 함


#### 📌 Json HTTP 메세지 바디에 담아서 전송 


```java
@WebServlet(name = "requestBodyJsonServlet", urlPatterns = "/request-body-json")
public class RequestBodyJsonServlet extends HttpServlet {

    private ObjectMapper objectMapper = new ObjectMapper();

    @Override
    protected void service(HttpServletRequest request, HttpServletResponse response) throws ServletException, IOException {
        ServletInputStream inputStream = request.getInputStream();
        String messageBody = StreamUtils.copyToString(inputStream, StandardCharsets.UTF_8); //여기 까지는 메세지 바디에 담는 것과 동일

        HelloData helloData = objectMapper.readValue(messageBody, HelloData.class); //객체로 변환!!
        System.out.println("helloData.username = " + helloData.getUserName());
        System.out.println("helloData.age = " + helloData.getAge());

    }
}

```

JSON 결과를 파싱해서 사용할 수 있는 자바 객체로 변환하려면 Jackson, Gson 같은 JSON 변환 라이브러리를 추가해서 사용해야 한다.    
스프링 부트로 Spring MVC를 선택하면 기본으로 Jackson 라이브러리 ```ObjectMapper```를 함께 제공한다.




