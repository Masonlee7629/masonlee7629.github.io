---
title : "코드스테이츠 학습일지 - 2022-10-24 part 1"

categories:
  - CodeStates
excerpt: "Spring MVC - Service Layer - DI를 통한 서비스 계층과 API 계층 연동"
tags:
  - [TIL]

toc: true
toc_sticky: true

date: 2022-10-26
last_modified_at: 2022-10-26
---

## Spring MVC 서비스 계층
> API 계층에서 전달 받은 클라이언트의 요청 데이터를 기반으로 실질적인 비즈니스 요구사항을 처리하는 계층
> <br>
> <br>
> Spring의 DI를 이용하여 API 계층과 서비스 계층을 연동하고 API 계층에서 전달받은 DTO 객체를 서비스 계층의 도메인 엔티티 객체로 변환하여 전달

### DI를 통한 서비스 계층과 API 계층 연동
> API 계층에서 구현한 Controller 클래스가 서비스 계층의 Service 클래스와 메서드 호출을 통해 상호 작용함을 의미
>  - Service 클래스
>    - 도메인 업무 영역을 구현하는 비즈니스 로직을 처리하는 클래스
>    - Contoller가 전달 받은 요청을 실제로 처리하는 역할
>    - Controller의 핸들러 메서드와 1대1로 매치
> <br>
> <br>
>  - 도메인 엔티티(Entity) 클래스<br>
>    - API 계층에서 전달 받은 요청 데이터를 기반으로, 서비스 계층에서 비즈니스 로직을 처리하기 위해 필요한 데이터를 전달 받고 비즈니스 로직을 처리한 후 결과 값을 다시 API 계층으로 리턴해주는 역할
>    - 서비스 계층에서 데이터 액세스 계층과 연동하며 비즈니스 로직을 처리하기 위해 필요한 데이터를 담는 역할

#### 구현되지 않은 Service 클래스 예시
> 도메인 엔티티 클래스를 구현한 후 Service 클래스를 구현

```java
import java.util.List;

public class MemberService {
    public Member createMember(Member member) {
        return null;
    }

    public Member updateMember(Member member) {
        return null;
    }

    public Member findMember(long memberId) {
        return null;
    }

    public List<Member> findMembers() {
        return null;
    }

    public void deleteMember(long memberId) {

    }
}
```

#### 도메인 엔티티 클래스 구현 예시
> API 계층의 DTO 클래스에서 사용한 멤버 변수가 모두 포함
> - @Getter, @Setter
>   - Lombok 라이브러리에서 제공하는 애너테이션
>   - 각 멤버 변수에 해당하는 getter/setter 메서드를 자동으로 생성해주는 유틸리티성 라이브러리
> <br>
> <br>
> - @AllArgsConstructor
>   - 엔티티 클래스에 추가된 모든 멤버 변수를 파라미터로 갖는 생성자를 자동으로 생성하는 애너테이션
> <br>
> <br>
> - @NoArgsConstructor
>   - 파라미터가 없는 기본 생성자를 자동으로 생성하는 애너테이션

```java
@Getter
@Setter
@NoArgsConstructor
@AllArgsConstructor
public class Member {
  private long memberId;
  private String email;
  private String korName;
  private String phoneNumber;
}
```

#### 구현된 Service 클래스 예시
> 실제 구현 시 데이터 액세스 계층과 연동하여 DB에 데이터를 저장 및 조회 등이 가능해야 함

```java
public class MemberService {
  public Member createMember(Member member) {
    Member createdMember = member;
    return createdMember;
  }

  public Member updateMember(Member member) {
    Member updatedMember = member;
    return updatedMember;
  }

  public Member findMember(long memberId) {
    Member member = new Member(memberId, "mason@gmail.com", "메이슨", "010-1111-1111");
    return member;
  }

  public List<Member> findMembers() {
    List<Member> members = List.of(
      new Member(1, "mason@gmail.com", "메이슨", "010-1111-1111"),
      new Member(2, "serena@gmail.com", "세레나", "010-2222-2222")
    );
    return members;
  }

  public void deleteMember(long memberId) {
  }
}
```

#### DI를 적용한 Service 계층과 API 계층 연동
> DI를 적용하면 클래스 간의 결합을 느슨한 결합(loose Coupling)으로 쉽게 만들 수 있음
> - 느슨한 결합<br>
> 클래스의 자료 구조, 메서드를 추상화할 수 있는 인터페이스 클래스를 사용해 의존성을 최소화
> <br>
> <br>
> - 강한 결합<br>
> 클래스와 객체가 서로 의존적인 상태로 객체가 변경될 시 클래스가 전체적으로 수정되어야 할 수 있음

```java
// Controller 클래스에 DI 적용
@RestController
@RequestMapping("/v1/members")
@Validated
public class MemberController {
  private final MemberService memberService;

	// DI를 적용하여 MemberController 생성자 파라미터로 MemberService의 객체를 주입
  public MemberController(MemberService memberService) {
    this.memberService = memberService;
  }
	
	...
	... // MemberContoller 핸들러 메서드 생략
}
```
```java
// Service 클래스에 @Service 애너테이션 추가
// DI를 통해 객체를 주입 받기 위해서는
// 주입을 받는 클래스와 주입 대상 클래스 모두 Spring Bean으로 등록되어야 함
@Service
public class MemberService {
  // 생성자가 하나 이상일 경우
  // DI를 적용하기 위한 생성자에 반드시 @Autowired 애너테이션 적용
  ...
	... // MemberService 핸들러 메서드 생략
}
```


---


문의사항 또는 블로그 내 수정이 필요한 부분은<br>
메일로 연락해주시면 확인 및 수정하도록 하겠습니다.<br>
감사합니다.
{: .notice--info}

---