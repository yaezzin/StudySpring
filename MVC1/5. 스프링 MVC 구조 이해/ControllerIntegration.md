# # 5-5. 컨트롤러 통합

## 📝 컨트롤러 클래스 통합
```@RequestMapping```은 잘 보면 클래스 단위가 아닌 메서드 단위에 적용이 되었는데, 컨트롤러 클래스를 하나로 통합해보자!
* 하나의 클래스 안에 저장, 조회, 목록의 메서드를 모두 넣자!
* 메서드 이름은 process 였지만 v1과 중복으로 인해 변경!

```java
@Controller
public class SpringMemberControllerV2 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @RequestMapping("/springmvc/v2/members/new-form")
    public ModelAndView newForm() {
        return new ModelAndView("new-form");
    }

    @RequestMapping("/springmvc/v2/members")
    public ModelAndView save() {
        List<Member> members = memberRepository.findAll();
        ModelAndView mv = new ModelAndView("members");
        mv.addObject("members", members);
        return mv;
    }

    @RequestMapping("/springmvc/v2/members/save")
    public ModelAndView members(HttpServletRequest request, HttpServletResponse response) {
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

## ✨ 중복 제거!
@RequestMapping 부분에서 매핑정보 중 ```/springmvc/v2/members``` 는 모두 중복되는 것을 볼 수 있다!  
👉 클래스 레벨에서 중복되는 매핑정보를 작성해주자!

```java
@Controller
@RequestMapping("/springmvc/v2/members")
public class SpringMemberControllerV2 {

    private MemberRepository memberRepository = MemberRepository.getInstance();

    @RequestMapping("new-form")
    public ModelAndView newForm() {
        return new ModelAndView("new-form");
    }

    @RequestMapping
    public ModelAndView save() {
        List<Member> members = memberRepository.findAll();
        ModelAndView mv = new ModelAndView("members");
        mv.addObject("members", members);
        return mv;
    }

    @RequestMapping("save")
    public ModelAndView members(HttpServletRequest request, HttpServletResponse response) {
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

