@Slf4j
@Controller
public class RequestParamController {

    //요청파라미터를 조회하는 방법1 : HttpServletRequest가 제공하는 방식으로 조회해보기!
    @RequestMapping("/request-param-v1")
    public void requestParamV1(HttpServletRequest request, HttpServletResponse response) throws IOException {
        String username = request.getParameter("username");
        int age = Integer.parseInt(request.getParameter("age"));

        log.info("username={}, age={}", username, age); //콘솔에 출력
        response.getWriter().write("ok"); //웹페이지에 ok 출력
    }



}
