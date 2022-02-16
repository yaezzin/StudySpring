# # 3-1. 회원 관리 웹 애플리케이션 요구사항

**🧑‍💼 회원 정보** : ```이름(username)```, ```나이(age)```  
**🛠 요구 사항** : ```회원 저장```, ```회원 목록 조회```  
**✨ 주요 메서드** : ```save()```, ```findById()```, ```findAll()```

```java
public Member save(Member member) {
    member.setId(++sequence);
    store.put(member.getId(), member);
    return member;
}
```

```java
public Member findById(Long id) {
    return store.get(id);
}
```
```java
public List<Member> findAll() {
    return new ArrayList<>(store.values());
}
```
