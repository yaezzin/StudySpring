# # 5-4. 스프링 MVC 시작

## @Controller

* 이 애노테이션이 존재하면 스프링이 컨트롤러를 자동으로 스프링빈으로 등록
* @Controller 안에는 ```@Component``` 애노테이션이 존재하므로 컴포넌트의 대상이 됨!

## @RequestMapping

* @RequestMapping은 요청 정보를 매핑하고, 해당 URL이 호출되면 이 애노테이션 붙은 메서드가 호출됨
* 클라이언트의 요청이 오면 기본적으로 핸들러 목록의 조회하고 어댑터를 조회하여 찾아줘야 함

```@Controller가 존재하면```
* ```RequestMappingHandlerMapping``` 이 "내가 처리할 수 있어!"하고 핸들러 목록을 조회하여 꺼내주고,
* ```RequestMappingHanlderAdapter``` 가 해당 컨트롤러를 처리할 수 있는 어댑터를 꺼내줌

## SpringMemberSaveControllerV1

#### 🤔 이전과 달라진 점

* ModelView가 아닌 ```ModelAndView```를 반환
* ```mv.addObject("members", member)``` : 스프링이 제공하는 ModelAndView를 통해 모델 데이터를 추가할 때는 addObject 사용!!

```java
@Controller
public class SpringMemberSaveControllerV1 {
    private MemberRepository memberRepository = MemberRepository.getInstance();

    @RequestMapping("/springmvc/v1/members/save")
    public ModelAndView process(HttpServletRequest request, HttpServletResponse response) {
        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));

        Member member = new Member(username, age);
        memberRepository.save(member);

        ModelAndView mv = new ModelAndView("save-result");
        mv.addObject("members", member);
        return mv;
    }

}
```
