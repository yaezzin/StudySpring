# # 4-7.add V4 to V5

๐ ์ด๋ฒ์๋ FrontControllerServlet5์ ControllerV4 ๊ธฐ๋ฅ๋ ์ถ๊ฐํด๋ณด์!

## FrontControllerServlet5

* ํธ๋ค๋ฌ ๋งคํ์ ์ปจํธ๋กค๋ฌ4๋ฅผ ์ฌ์ฉํ๋ ์ปจํธ๋กค๋ฌ๋ฅผ ์ถ๊ฐ
```java
private void initHandlerMappingMap() {
    handlerMappingMap.put("/front-controller/v5/v3/members/new-form", new MemberFormControllerV3());
    handlerMappingMap.put("/front-controller/v5/v3/members/save", new MemberListControllerV3());
    handlerMappingMap.put("/front-controller/v5/v3/members", new MemberListControllerV3());

     // v4 ์ถ๊ฐ
    handlerMappingMap.put("/front-controller/v5/v4/members/new-form", new MemberFormControllerV4());
    handlerMappingMap.put("/front-controller/v5/v4/members/save", new MemberListControllerV4());
    handlerMappingMap.put("/front-controller/v5/v4/members", new MemberListControllerV4());
}
```

* ํด๋น ์ปจํธ๋กค๋ฌ๋ฅผ  ์ฒ๋ฆฌํ  ์ ์๋ ์ด๋ํฐ ์ถ๊ฐ

```java
private void initHandlerAdapters() {
    handlerAdapters.add(new ControllerV3HandlerAdapter());
    handlerAdapters.add(new ControllerV4HandlerAdapter()); // v4 ์ถ๊ฐ
}
 ```
 
## ControllerV4HandlerAdapter

V4์ ๊ฒฝ์ฐ ๋ทฐ๋ค์์ ๋ฐํํ์๋๋ฐ, handle ๋ฉ์๋๋ ๋ชจ๋ธ๋ทฐ ๊ฐ์ฒด๋ฅผ ๋ฐํํด์ค์ผ ํจ
-> ์ด๋ํฐ๊ฐ ๋ทฐ ์ด๋ฆ์ ๋ชจ๋ธ๋ทฐ๋ก ๋ง๋ค์ด์ ํ์์ ๋ง์ถ์ด ์ค๋ค!!!

```java
@Override
public ModelView handle(HttpServletRequest request, HttpServletResponse response, Object handler) throws ServletException, IOException {
    // handler๋ฅผ ControllerV4๋ก ์บ์คํ  
    ControllerV4 controller = (ControllerV4) handler;

    Map<String, String> paramMap = createParamMap(request)
    Map<String, Object> model = new HashMap<>();

    //v4๋ ๋ทฐ๋ค์์ ๋ฐํํ๊ธฐ ๋๋ฌธ์ ๋ทฐ๋ค์์ ๊ฐ์ง๊ณ  ๋ชจ๋ธ๋ทฐ๋ฅผ ๋ง๋ค๊ณ  ๋ชจ๋ธ๊น์ง ๊ฐ์ ์ธํํด์ ๋ฃ์ ํ ๋ชจ๋ธ๋ทฐ ๋ฐํ
    String viewName = controller.process(paramMap, model);

    ModelView mv = new ModelView(viewName); //๋ทฐ ์ธํ
    mv.setModel(model); //๋ชจ๋ธ์ธํ
    return mv;
}    
```

## ์ ๋ฆฌ !

FrontController๋ ํธ๋ค๋ฌ ์ด๋ํฐ ์ธํฐํ์ด์ค์๋ง ์์กดํ๊ธฐ ๋๋ฌธ์ ๊ตฌํํด๋์ค๊ฐ ์ด๋ค ๊ฒ์ด ๋ค์ด์๋ ์ฝ๋์ ๊ตฌ์กฐ๋ฅผ ๋ฐ๊ฟ์ค ํ์๊ฐ ์์!  
๋จ์ํ ์ปจํธ๋กค๋ฌ์ ์ด๋ํฐ๋ง ์ถ๊ฐํด์ฃผ๋ฉด ๋จ  
๋ง์ฝ handlerMapping.put(~) ์ฝ๋๋ง ๋ฐ์์ ์ฃผ์ํ๋๋ก ๋ฐ๊พธ์ด์ค๋ค๋ฉด ```OCP(Open-Closed Principle)```๋ฅผ ์๋ฒฝํ ์งํค๋ ๊ตฌ์กฐ!!




