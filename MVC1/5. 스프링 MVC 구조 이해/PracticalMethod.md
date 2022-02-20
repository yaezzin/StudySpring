# # 5-6. 실용적인 방법

MVC 프레임워크를 만들 때 V3가 ModelView를 반환했던 것을 V4는 viewName(String)만 반환하도록 개선했었다! 
이번에도 실용적으로 개선해보도록 하자!


## 1. ModelView 👉 ViewName

📌 반환값을 String으로 바꾸고, "new-form", "save-result" 같은 viewName만 반환하자

```java
// newForm 메서드만 살펴보자!
@RequestMapping("/new-form")
public String newForm() {
    return "new-form";
}
```

## 2. @RequestParam

📌 request, response, model(Map)을 파라미터로 받았던 것을 ```@RequestParam``` 애노테이션을 이용해서 간소화해보자

```java
@RequestMapping("/save")
public String save(@RequestParam("username") String username,
                   @RequestParam("age") int age,
                   Model model) {

     Member member = new Member(username, age);
     memberRepository.save(member);

     model.addAttribute("members", member);
    return "save-result";
}
```

* 스프링은 HTTP 요청 파라미터를 @RequestParam으로 받을 수 있다!
* @requestParam("username")은 ```request.getAttribute("username")```과 비슷하다고 생각하자
* @requestParam은 GET, POST 방식 모두 지원         

#### @GetParameter 애노테이션을 통해 파라미터를 받았기 때문에 다음 코드를 삭제 가능!

```java
String username = request.getParameter("username");
int age = Integer.parseInt(request.getParameter("age"));
```

## 3. @GetMapping @PostMapping

📌 @RequestMapping은 URL 매핑 뿐아니라 HTTP 메서드(ex. GET, POST)도 함께 구분 가능하다!
* 이전에는 GET과 POST를 구분하지 않고 해당 URL이면 모든 요청을 다 받았었는데 이번에는 구분을 해보자 🙊

``` java
@RequestMapping(value = "/new-form", method = RequestMethod.GET)
```

💥 하지만 스프링은 더 간편한 **@GetMapping, @PostMapping** 애노테이션을 제공!

``` java
@GetMapping
public String members(Model model) {
    List<Member> members = memberRepository.findAll();
    model.addAttribute("members", members);
    return "members";
}
```
```java
@PostMapping("/save")
public String save(@RequestParam("username") String username,
                   @RequestParam("age") int age,
                   Model model) {
       //생략
}
```
