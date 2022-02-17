# # 3-3. by JSP

## jsp 라이브러리 추가

```build.gradle```에 해당 코드를 추가
``` java
implementation 'org.apache.tomcat.embed:tomcat-embed-jasper'
implementation 'javax.servlet:jstl'
```

## 1. 회원 등록 

```new-form.jsp```

``` jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>

<html>
<head>
    <title>Title</title>
</head>
<body>
<form action="/jsp/members/save.jsp" method="post"> 
    username: <input type="text" name="username" /> 
    age: <input type="text" name="age" /> 
    <button type="submit">전송</button>
</form>
</body>
</html>
```

``` <%@ page contentType="text/html;charset=UTF-8" language="java" %>``` : 나는 JSP 문서다! 라고 알려주는 것임. JSP 문서는 이렇게 시작해야 함. 

🤔 회원 등록 JSP를 보면 첫 줄을 제외하고는 완전히 HTML와 똑같다.   
JSP는 서버 내부에서 서블릿으로 변환되는데, 이전의 ```MemberFormServlet``` 과 거의 비슷한 모습으로 변환된다.


## 2. 회원 저장

```save.jsp```

```jsp
<%@ page contentType="text/html;charset=UTF-8" language="java" %>

<%
    // 회원 저장 서블릿 코드랑 동일하네 ?!
    MemberRepository memberRepository = MemberRepository.getInstance();
    
    // jsp는 서블릿과 달리 request, response에 대한 별도의 선언? 없이도 사용 가능!
    String username = request.getParameter("username");
    int age = Integer.parseInt(request.getParameter("age"));
    
    Member member = new Member(username, age);
    memberRepository.save(member);
%>

<html>
<head>
    <meta charset="UTF-8">
</head>
<body>
성공
<ul>
    <li>id=<%=member.getId()%></li>
    <li>username=<%=member.getUsername()%></li>
    <li>age=<%=member.getAge()%></li>
</ul>
<a href="/index.html">메인</a>
</body>
</html>
```

```<% ~~~ %>``` : 자바 코드를 **입력**할 수 있는 공간  
```<%= ~~~ %>``` : 자바 코드를 **출력**할 수 있는 공간


## 3. 회원 목록

```members.jsp```

👉 회원 리포지토리에서 전체 회원을 조회 후, 결과 List를 사용해서 중간에 <tr><td> HTML 태그를 반복해서 출력하고 있다.  

```jsp
<%
  MemberRepository memberRepository = MemberRepository.getInstance();
  List<Member> members = memberRepository.findAll();
%>

<html>
<head>
    <meta charset="UTF-8">
    <title>Title</title>
</head>
<body>
<a href="/index.html">메인</a>
<table>
    <thead>
    <th>id</th>
    <th>username</th>
    <th>age</th>
    </thead>
    <tbody>
    <%
        for (Member member : members) {
            out.write("    <tr>");
            out.write("        <td>" + member.getId() + "</td>");
            out.write("        <td>" + member.getUsername() + "</td>");
            out.write("        <td>" + member.getAge() + "</td>");
            out.write("    </tr>");
        }
    %>
    </tbody>
</table>
</body>
</html>

```
  
## 📌 서블릿과 JSP의 불편함
  
서블릿으로 개발할 때는 뷰 화면을 위한 HTML을 자바코드로 일일히 입력 해줘야 했기에 매우 복잡했고,  
JSP로 개발할 때는 뷰를 생성하는 HTML 작업은 동적으로 변경이 필요한 부분만 자바코드를 적용해주었기에 상대적으로 수월했다.  

하지만 JSP 파일을 보면 상위에는 비즈니스 로직(ex. 회원 저장, 회원 조회)이고, 나머지 하위 절반은 뷰 영역이다.  
이처럼 JSP가 너무 많은 역할을 하고 있다.... 
  
비즈니스 로직은 서블릿처럼, 뷰 은 JSP처럼하는 방법은 없을까? 
#### 👉 MVC 패턴 등장
  

