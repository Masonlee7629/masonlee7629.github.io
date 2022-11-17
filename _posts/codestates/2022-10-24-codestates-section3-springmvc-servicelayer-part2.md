---
title : "코드스테이츠 학습일지 - 2022-10-24 part 2"

categories:
  - CodeStates
excerpt: "Spring MVC - Service Layer - Mapper를 이용한 DTO 클래스와 엔티티 클래스 매핑"
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

### 매퍼(Mapper)를 이용한 DTO클래스와 엔티티(Entity) 클래스 매핑
#### 매퍼(Mapper)
> DTO 클래스와 엔티티(Entity) 클래스를 변환해주는 클래스
> - 매퍼를 이용해 계층 간의 역할 분리
>   - DTO 클래스는 API 계층에서만 데이터를 처리하는 역할을 하고, 엔티티 클래스는 서비스 계층에서만 데이터를 처리하는 역할을 함으로써
> 계층간의 분리가 이루어짐

#### 매퍼를 이용한 매핑 순서
1. Mapper 클래스 구현
2. Controller의 핸들러 메서드에 매퍼 클래스 적용 


#### Mapper 클래스 구현
> Mapper 클래스의 클래스레벨에 @Component 추가
> - Mapper 클래스를 Spring Bean으로 등록하고 등록된 Bean은 Controller 클래스에서 사용

```java
@Component
public class MemberMapper {

  // MemberPostDto 를 Member 엔티티로 변환
  public Member memberPostDtoToMember(MemberPostDto memberPostDto) {
    return new Member(
      0L,
      memberPostDto.getEmail(),
      memberPostDto.getName(),
      memberPostDto.getPhone()
    );
  }

  // MemberPatchDto 를 Member 엔티티로 변환
  public Member memberPatchDtoToMember(MemberPatchDto memberPatchDto) {
    return new Member(
      memberPatchDto.getMemberId(),
      // 일반적으로 이메일은 고유한 데이터로 변경되지 않아
      // null로 Patch가 불가능하도록 표기한 것
      null,
      memberPatchDto.getName(),
      memberPatchDto.getPhone()
    );
  }

  // Member 엔티티를 MemberResponseDto 로 변환
  // MemberResponseDto 는 ResponseBody의 역할을 해주는 DTO 클래스
  public MemberResponseDto memberToMemberResponseDto(Member member) {
    return new MemberResponseDto(
      member.getMemberId(),
      member.getEmail(),
      member.getName(),
      member.getPhone()
    );
  }
}
```

#### Controller의 핸들러 메서드에 매퍼 클래스 적용
> DI로 주입하여 Spring Bean에 등록된 Mapper 객체를 Controller에서 사용
> - Service 계층과 연동이 이루어지는 곳에서 Dto 클래스를 엔티티 클래스로 변환하여 전달
> - 클라이언트에게 전달할 ResponseBody는 API 계층의 영역으로 DTO 클래스로 전달
>   - DTO 클래스가 엔티티 클래스로 변환되었다면 다시 DTO 클래스로 재변환

```java
@RestController
@RequestMapping("/v1/members/")
@Validated
public class MemberController {
  private final MemberService memberService;
  private final MemberMapper mapper;

  public Controller(MemberService memberService, MemberMapper mapper) {
    this.memberService = memberService;
    this.mapper = mapper;
  }

  @PostMapping
  public ResponseEntity postMember(@Valid @RequestBody MemberPostDto memberDto) {
    // Mapper를 이용하여 MemberPostDto를 Member로 변환
    Member member = mapper.memberPostDtoToMember(memberDto);

    Member response = memberService.createMember(member);

    // Mapper를 이용하여 Member를 MemberResponseDto로 변환
    return new ResponseEntity<>(mapper.memberToMemberResponseDto(response), HttpStatus.CREATED);
  }

  @PatchMapping("/{member-id}")
  public ResponseEntity patchMember(@PathVariable("member-id") @Positive long memberId,
                                    @Valid @RequestBody MemberPatchDto memberPatchDto) {
    memberPatchDto.setMemberId(memberId);
  
    // Mapper를 이용하여 MemberPatchDto를 Member로 변환
    Member response = 
      memberService.updateMember(mapper.memberPatchDtoToMember(memberPatchDto));

    // Mapper를 이용하여 Member를 MemberResponseDto로 변환
    return new ResponseEntity<>(mapper.memberToMemberResponseDto(response), HttpStatus.OK);
  }

  @GetMapping("/{member-id}")
  public ResponseEntity getMember(@PathVariable("member-id") @Positive long memberId) {
    Member response = memberService.findMember(memberId);

    // Mapper를 이용하여 Member를 MemberResponseDto로 변환
    return new ResponseEntity<>(mapper.memberToMemberResponseDto(response), HttpStatus.OK);
  }

  @GetMapping
  public ResponseEntity getMembers() {
    List<Member> members = memberService.findMembers();

    // // Mapper를 이용하여 List<Member>를 MemberResponseDto로 변환
    List<MemberResponseDto> response = members.stream()
                        .map(member -> mapper.memberToMemberResponseDto(member))
                        .collect(Collectors.toList());

    return new ResponseEntity<>(response, HttpStatus.OK);
  }

  @DeleteMapping("/{member-id}")
  public ResponseEntity deleteMember(@PathVariable("member-id") @Positive long memberId) {
    System.out.println("# delete member");
    memberService.deleteMember(memberId);

    return new ResponseEntity(HttpStatus.NO_CONTENT);
  }
}
```

#### MapStruct를 이용한 Mapper 자동 생성
> 매퍼(Mapper) 구현 클래스를 자동으로 생성해주는 코드 자동 생성기
> - 도메인 업무 기능이 증가함에 따라 매퍼 클래스를 생성하는 것은 비효율적
>   - MapStruct를 이용해 자동으로 구현함으로 생산성 향상

- MapStruct 의존 라이브러리 설정
> build.gradle 파일에 의존 라이브러리 추가 및 Gradle 프로젝트 Reload

```java
dependencies {
	...
	...
	implementation 'org.mapstruct:mapstruct:1.4.2.Final'
	annotationProcessor 'org.mapstruct:mapstruct-processor:1.4.2.Final'
}
```

- MapStruct 기반의 Mapper 인터페이스 정의
> @Mapper를 추가하여 해당 인터페이스가 MapStruct의 매퍼 인터페이스임을 정의
> - 애트리뷰트로 componentModel = "spring" 을 지정하면 Spring Bean으로 등록
> - 해당 인터페이스를 통해 MapStruct가 Mapper 구현 클래스를 자동 생성

```java
@Mapper(componentModel = "spring")
public interface MemberMapper {
  Member memberPostDtoToMember(MemberPostDto memberPostDto);
  Member memberPatchDtoToMember(MemberPatchDto memberPatchDto);
  MemberResponseDto memberToMemberResponseDto(Member member);
}
```

#### 자동 생성된 Mapper 인터페이스 구현 클래스 확인
> Mapper인터페이스의 구현 클래스는 Gradle의 build task를 실행하면 자동으로 생성
> - Project 탭 > 프로젝트명 > build 디렉토리 내 Mapper 인터페이스가 위치한 패키지 내에 생성

```java
@Component
public class MemberMapperImpl implements MemberMapper {
  public MemberMapperImpl() {
  }

  public Member memberPostDtoToMember(MemberPostDto memberPostDto) {
    if (memberPostDto == null) {
      return null;
    } else {
      Member member = new Member();
      member.setEmail(memberPostDto.getEmail());
      member.setName(memberPostDto.getName());
      member.setPhone(memberPostDto.getPhone());
      return member;
    }
  }

  public Member memberPatchDtoToMember(MemberPatchDto memberPatchDto) {
    if (memberPatchDto == null) {
      return null;
    } else {
      Member member = new Member();
      member.setMemberId(memberPatchDto.getMemberId());
      member.setName(memberPatchDto.getName());
      member.setPhone(memberPatchDto.getPhone());
      return member;
    }
  }

  public MemberResponseDto memberToMemberResponseDto(Member member) {
    if (member == null) {
      return null;
    } else {
      long memberId = 0L;
      String email = null;
      String name = null;
      String phone = null;
      memberId = member.getMemberId();
      email = member.getEmail();
      name = member.getName();
      phone = member.getPhone();
      MemberResponseDto memberResponseDto = 
					new MemberResponseDto(memberId, email, name, phone);
      return memberResponseDto;
    }
  }
}
```

#### Controller의 핸들러 메서드에서 MapStruct 적용
> MapStruct 인터페이스가 위치를 import문으로 전달

```java
//import com.codestates.member.mapper.MemberMapper;
import com.codestates.member.mapstruct.mapper.MemberMapper; // 패키지 변경

@RestController
@RequestMapping("/v1/members/")
@Validated
public class MemberController {
  ...
  ... // 변경 사항 없음으로 생략
}
```

### DTO 클래스와 엔티티 클래스의 역할 분리가 필요한 이유
- 계층별 관심사 분리
> - DTO 클래스<br>
> API 계층에서 Request Body를 전달 받고, Response Body를 전송하는 것이 목적
> - Entity 클래스<br>
> Service 계층에서 Data Access 계층과 연동하여 비즈니스 로직의 결과로 생성된 데이터를 다루는 것이 목적
> - 객체 지향 프로그래밍<br>
> 하나의 클래스나 메서드 내에서 여러 기능을 구현하는 것은 객체 지향 코드 관점에서 리팩토리 대상

- 코드 구성의 단순화
> Entity 클래스에는 데이터 액세스 기술인 JPA에서 사용하는 애너테이션이 적용
> <br>
> <br>
> DTO 클래스에서 유효성 검사 애너테이션을 사용하여 유지보수에 용이하도록 함

- REST API 스펙의 독립성 확보
> DTO 클래스를 사용하여 클라이언트에게 원하는 정보만 선별하여 제공 가능
> - Data Access 계층에서 전달 받은 데이터로 이루어진 Entity 클래스 그대로 전달하면 원치 않는 데이터까지 클라이언트에게 전송될 가능성이 존재
> - Ex) 회원의 로그인 패스워드

---


문의사항 또는 블로그 내 수정이 필요한 부분은<br>
메일로 연락해주시면 확인 및 수정하도록 하겠습니다.<br>
감사합니다.
{: .notice--info}

---