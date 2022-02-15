# # 2-2. HttpServletRequest

## 1. 역할

HTTP 요청 메시지를 개발자가 직접 파싱해서 사용해도 되지만, 매우 불편할 것임!  
서블릿은 개발자 대신에 HTTP 요청 메시지를 파싱하고 그 결과를 HttpServletRequest 객체에 담아서 제공한다.
  
  
## 2. 기능

2-1. 임시 저장소 기능
* 해당 HTTP 요청이 시작부터 끝날 때 까지 유지되는 임시 저장소 기능 
* 저장: ```request.setAttribute(name, value)```
* 조회: ```request.getAttribute(name)```

2-2. 세션 관리 기능
* ```request.getSession(create: true)```  
  
## 3. 주의 ✨
HttpServletRequest, HttpServletResponse는 HTTP 요청 메시지, HTTP 응답 메시지를 편리하게 사용하도록 도와주는 **객체**라는 점  
HTTP 스펙이 제공하는 요청, 응답 메시지 자체를 이해해야 하는 것이 중요!

## 4. 기본 메서드

### [ Request ]   


``` getMethod()``` : GET 방식인지 POST 방식인지 확인할 수 있는 메서드  

```getProtocol()``` : 클라이언트가 요청한 프로토콜을 반환

```getScheme()``` : http (또는 https)를 반환  

```getRequestURL()``` : 전체 주소 정보를 반환 (ex. http://localhost:8080/request-header)  

```getRequestURI()``` :contextPath 이후 주소를 반환 (ex. /request-header를 반환)

```getQueryString()```: (ex. year=2021&month=10)

```isSecure()``` : https같은 보안 채널의 사용 여부를 반환

----
### [ Header ]

1. 특정 헤더 하나를 조회 : ```getHeader("~")```

2. 전체 헤더 조회 : 

``` java
request.getHeaderNames().asIterator().forEachRemaining(headerName ->
        System.out.println(headerName + ": " + request.getHeader(headerName)));
```

---
### [ Header Utils ]


```getServerName()``` : 도메인을 반환

```getServerPort()``` : 포트 번호를 반환 (ex. 8080)

```getLoacale()``` : 지역정보를 반환 (한국의 경우 ko)  

```java
 request.getLocales().asIterator().forEachRemaining(locale ->
                System.out.println("locale = " + locale));
```

```getCookies()``` : 쿠키 정보 반환  

```java
if (request.getCookies() != null) {
            for (Cookie cookie : request.getCookies()) {
                System.out.println(cookie.getName() + ": " + cookie.getValue());
            }
        }
```

```getContentType()``` : 클라이언트가 요청 정보를 전송할 때 사용한 컨텐트 타입을 반환

```getContentLength()``` : 클라이언트가 전송한 요청 정보의 길이를 반환

```getCharacterEncoding()``` : 클라이언트가 요청 정보를 전송할 떄 사용한 문자셋의 인코딩을 반환

-----
### [ Locale ]

서버에 대한 정보 ⚙

```getLocaleName()``` : 로컬호스트 반환

```getLocalAddr``` : 서버의 도메인 주소 반환

```getLocalPort()``` : 서버의 포트번호 반환

-----

### [ Client ]

요청에 대한 정보 📩

```getRemoteHost()``` : 클라이언트의 host값 반환

```getRemoteAddr()``` : 클라이언트의 IP주소를 반환

```getRemotePort()``` : 클라이언트의 포트 반환


