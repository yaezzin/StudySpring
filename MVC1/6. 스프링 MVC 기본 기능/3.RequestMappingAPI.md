# # 6-3. 요청 매핑 - API 예시

데이터는 전송하지 않고 매핑 메서드만 살펴보자!

### 🧑‍💻 회원관리 HTTP API 예시

* 목록 조회: **GET** ```/users```  
* 회원 등록: **POST** ```/users```  
* 회원 조회: **GET** ```/users/{userId}  ```  
* 회원 수정: **PATCH** ```/users/{userId}```   
* 회원 삭제: **DELETE** ```/users/{userId} ```  


```java
@RestController
public class MappingClassController {

    // GET /mapping/users
    @GetMapping("/mapping/users")
    public String user() {
        return "get users";
    }

    // POST /mapping/users
    @PostMapping("/mapping/users")
    public String addUser() {
        return "post user";
    }

    // GET /mapping/users/{userId}
    @GetMapping("/mapping/users/{userId}")
    public String findUser(@PathVariable String userId){
       return "get userId = " + userId;
    }
    
    // PATCH /mapping/users/{userId}
    @PatchMapping("/mapping/users/{userId}")
    public String updateUser(@PathVariable String userId){
        return "update" + userId;
    }

    // DELETE /mapping/users/{userId}
    @DeleteMapping ("/mapping/users/{userId}")
    public String deleteUser(@PathVariable String userId){
        return "delete" + userId;
    }
}
```

* 클래스 레벨에 ```@RequestMapping("/mapping/users")``` 써주면 중복 제거가능!!
