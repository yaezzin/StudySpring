# # 2-2. HttpServletRequest

### 1. 역할

HTTP 요청 메시지를 개발자가 직접 파싱해서 사용해도 되지만, 매우 불편할 것임!  
서블릿은 개발자 대신에 HTTP 요청 메시지를 파싱하고 그 결과를 HttpServletRequest 객체에 담아서 제공한다.
  
  
### 2. 기능

  2-1. 임시 저장소 기능
* 해당 HTTP 요청이 시작부터 끝날 때 까지 유지되는 임시 저장소 기능 
* 저장: ```request.setAttribute(name, value)```
* 조회: ```request.getAttribute(name)```

  2-2. 세션 관리 기능
* ```request.getSession(create: true)```  
  
### 3. 주의 ✨
HttpServletRequest, HttpServletResponse는 HTTP 요청 메시지, HTTP 응답 메시지를 편리하게 사용하도록 도와주는 **객체**라는 점  
HTTP 스펙이 제공하는 요청, 응답 메시지 자체를 이해해야 하는 것이 중요!

### 4. 기본 메서드

#### [ Request-Line ]   


``` getMethod()``` : GET 방식인지 POST 방식인지 확인할 수 있는 메서드  

```getProtocol()``` :  

```getScheme()``` : http (또는 https)를 반환  

```getRequestURL()``` : 전체 주소 정보를 반환 (ex. http://localhost:8080/request-header)  

```getRequestURI()``` :contextPath 이후 주소를 반환 (ex. /request-header를 반환)

```getQueryString()```:

```isSecure()``` : https 사용 유무를 반환

----
#### [ Header ]

1. ```getHeader("~")``` : 특정 헤더 하나를 조회할 때

2. 전체 조회 방법 

``` java
request.getHeaderNames().asIterator()
               .forEachRemaining(headerName ->
                       System.out.println(headerName + ": " + request.getHeader(headerName)));
```

---
#### [  ]


```getServerName()```

```getServerPort()```

```getLoacale()```

```java
 request.getLocales().asIterator().forEachRemaining(locale ->
                System.out.println("locale = " + locale));
```


```
if (request.getCookies() != null) {
            for (Cookie cookie : request.getCookies()) {
                System.out.println(cookie.getName() + ": " + cookie.getValue());
            }
        }
```

```getContentType()```

```getContentLength()```

```getCharacterEncoding```

-----
#### [ Etc ]


```getLocaleName()```

```getLocalAddr```

```getLocalPort()```


```getRemoteHost()```


```getRemoteAddr()```


```getRemotePort()```


