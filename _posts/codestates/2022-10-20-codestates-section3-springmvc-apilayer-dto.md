---
title : "코드스테이츠 학습일지 - 2022-10-20 part 3"

categories:
  - CodeStates
excerpt: "Spring MVC - DTO"
tags:
  - [TIL]

toc: true
toc_sticky: true

date: 2022-10-25
last_modified_at: 2022-10-25
---

## Spring MVC - DTO
> Data Transfer Object의 약자
> <br>
> <br>
> 마틴 파울러가 ‘Patterns of Enterprise Application Architecture’라는 책에서 처음 소개한 엔터프라이즈 애플리케이션 아키텍처 패턴

---

### DTO가 필요한 이유
- DTO클래스를 이용한 코드의 간결성
  - DTO클래스가 요청 데이터를 하나의 객체로 전달 받는 역할을 함
<br>
- 데이터 유효성 검증의 단순화
  - 유효성 검증 로직을 DTO클래스에 작성하여 핸들러 메서드의 간결함을 유지
<br>
- HTTP 요청의 수를 최소화
- 도메인 객체와의 분리

---

### DTO 클래스 적용을 위한 코드 리팩토링 절차
> 1)정보를 전달 받을 DTO 클래스 생성
  - Controller에서 전달 받는 각 데이터 항목을 DTO 클래스의 멤버 변수로 추가
  - 각 멤버 변수에 해당하는 getter 메서드가 필수
    - getter 메서드가 없으면 Request Body에 해당 멤버 변수의 값이 포함되지 않는 문제가 발생

```java
public class MemberPostDto {
  private String email;
  private String korName;
  
  public String getEmail() {
    return email;
  }

  public String getKorName() {
    return korName;
  }
}
```

> 2) 클라이언트에서 전달하는 요청 데이터를 @RequestParam 애너테이션으로 전달 받는 핸들러 메서드 탐색
  - Request Body가 필요한 핸들러는 HTTP POST, PATCH, PUT 같이 리소스의 추가나 변경이 필요할 때이며 HTTP GET은 리소스를 조회하는 용도이므로 Request Body가 필요 없음
  - @PostMappint, @PatchMapping 애너테이션이 붙은 핸들러 메서드를 찾는 것과 동일함
    - @RequestBody 애너테이션
      : JSON 형식의 Request Body를 MemberPostDto 클래스의 객체로 변환하는 역할
    - @ResponseBody 애너테이션
      : DTO 클래스의 객체를 Response Body로 변환하는 역할
      : 핸들러 메서드의 리턴 값이 ResponseEntity 클래스의 객체일 경우 내부적으로 HttpMessageConverter가 동작하여 응답 객체(Dto 클래스의 객체)를 JSON형식으로 변경하므로
        @ResponseBody 애너테이션을 사용하지 않음

> 3) @RequestParam의 코드를 DTO 클래스의 객체로 변경

> 4) Map 객체로 작성된 Request Body를 DTO 클래스의 객체로 변경

```java
// 2), 3), 4) 변경 전
@RestController
@RequestMapping("/v1/members")
public class MemberController {
  @PostMapping
  public ResponseEntity postMember(@RequestParam("email") String email,
                                   @RequestParam("name") String name,
                                   @RequestParam("phone") String phone) {
    Map<String, String> body = new HashMap<>();
    body.put("email", email);
    body.put("name", name);
    body.put("phone", phone);

    return new ResponseEntity<Map>(body, HttpStatus.CREATED);
  }
}

// 2), 3), 4) 변경 후
@RestController
@RequestMapping("/v1/members")
public class MemberController {
  @PostMapping
  public ResponseEntity postMember(@RequestBody MemberPostDto memberPostDto) {
    return new ResponseEntity<>(memberPostDto, HTTPStatus.CREATED);
  }
}
```

### DTO 클래스에 유효성 검증 적용 방법
> 1) DTO 클래스에 유효성 검증을 적용하기 위해서는 Spring Boot에서 지원하는 Starter가 필요
> <br>
> build.gradle파일의 dependencies 항목에 'org.springframework.boot:spring-boot-starter-validation’을 추가

```java
dependencies {
	implementation 'org.springframework.boot:spring-boot-starter-validation'
	...
}
```

> 2) 유효성 검증 요구 사항에 맞게 제약 사항을 작성
> <br>
> Ex) email : 값이 비어있지 않거나 공백이 아니어야 함

> 3) 요청으로 전달 받는 DTO 클래스의 각 멤버 변수에 유효성 검증을 위한 애너테이션을 추가
> - @NotBlank
>   - null 값이나 공백, 스테이스와 같은 값들을 모두 허용하지 않음
>   - 유효성 검증에 실패하면 에러 메시지가 콘솔에 출력
>   - message 애트리뷰트를 이용하여 지정된 에러 메시지 출력 가능
> - @Pattern
>   - 데이터가 정규 표현식에 매치되는 유효한 데이터인지 검증
>   - 유효성 검증에 실패하면 내장된 디폴트 에러 메시지가 콘솔에 출력
>   - message 애트리뷰트를 이용하여 지정된 에러 메시지 출력 가능
> - @Email
>   - 유효한 이메일 주소인지를 검증
>   - 정규 표현식을 이용해 세밀한 유효성 검사 조건의 지정 가능
>   - javax에서 지원하는 표준 Email 애너테이션(javax.validation.constraints.Email)을 사용

> 4) Controller 클래스의 핸들러 메서드에 사용되는 DTO 클래스에 @Valid 애너테이션을 추가

```java
import javax.validation.constraints.Email;
import javax.validation.constraints.NotBlank;
import javax.validation.constraints.Pattern;

public class MemberPostDto {
  @NotBlank
  @Email
  private String email;

  @NotBlank(message = "이름은 공백이 아니어야 합니다.")
  private String korName;

  @Pattern(regexp = "^010-\\d{3,4}-\\d{4}$",
           message = "휴대폰 번호는 010으로 시작하는 11자리 숫자와 '-'로 구성되어야 합니다.")
  private String phoneNumber;

  ...
  ... // 각 멤버 변수의 getter, setter 메서드 생략
}
```

### 쿼리 파라미터 및 @Pathvariable 에 대한 유효성 검증
> Controller 클래스의 핸들러 메서드 내 URI path에서 사용되는 @Pathvariable 및 이하 변수 또한 유효성 검증 대상
> <br>
> <br>
> 클래스 레벨에 @Validated 애너테이션이 추가
> <br>
> <br>
> @Pathvariable 애너테이션에 검증 애너테이션을 추가

```java
@RestController
@RequestMapping("/v1/members")
@Validated
public class MemberController {
	...
	...

  @PatchMapping("/{member-id}")
  // @Min 애너테이션 : 1 이상의 숫자일 경우에만 유효성 검증에 통과
  public ResponseEntity patchMember(@PathVariable("member-id") @Min(1) long memberId,
                                    @Valid @RequestBody MemberPatchDto memberPatchDto) {
    memberPatchDto.setMemberId(memberId);

    return new ResponseEntity<>(memberPatchDto, HttpStatus.OK);
  }
}
```

### Custom Validator를 사용한 유효성 검증
> Jakarta Bean Validation에 내장된(Built-in) 애너테이션 중에 여러분들의 목적에 맞는 애너테이션이 존재하지 않을 때 목적에 맞는 애너테이션을 직접 만들어 유효성 검증에 적용 가능
>   - Jakarta Bean Validation<br>
>     유효성 검증을 위한 표준 스펙에서 지원하는 내장 애너테이션<br>
>     Jakarta Bean Validation 스펙을 구현한 구현체는 Hibernate Validator

#### Custom Validator를 구현하기 위한 절차
1. Custom Validator를 사용하기 위한 Custom Annotation 정의
2. 정의한 Custom Annotation에 바인딩 되는 Custom Validator 구현
3. 유효성 검증이 필요한 DTO 클래스의 멤버 변수에 Custom Annotation 추가

```java
// Custom Annotation 정의
import javax.validation.Constraint;
import javax.validation.Payload;
import java.lang.annotation.ElementType;
import java.lang.annotation.Retention;
import java.lang.annotation.RetentionPolicy;
import java.lang.annotation.Target;

@Target(ElementType.FIELD)
@Retention(RetentionPolicy.RUNTIME)
// NotSpace 애너테이션이 멤버 변수에 추가되었을 때 동작할 Custom Validator를 추가
@Constraint(validatedBy = {NotSpaceValidator.class})
public @interface NotSpace {
  // 유효성 검증 실패 시 표시되는 디폴트 메시지 지정
  String message() default "공백이 아니어야 합니다";
  Class<?>[] groups() default {};
  Class<? extends Payload>[] payload() default {};
}
```
```java
// Custom Validator 구현
import org.springframework.util.StringUtils;
import javax.validation.ConstraintValidator;
import javax.validation.ConstraintValidatorContext;

// CustomValidator를 구현하기 위해서는 ContraintValidator 인터페이스를 구현해야 함
// ConstraintValidator<NotSpace, String>에서
// NotSpace는 CustomValidator와 매핑된 Custom Annotation을 의미
// String은 Custom Annotation으로 검증할 멤버 변수의 타입을 의미
public class NotSpaceValidator implements ConstraintValidator<NotSpace, String> {

    @Override
    public void initialize(NotSpace constraintAnnotation) {
        ConstraintValidator.super.initialize(constraintAnnotation);
    }

    @Override
    public boolean isValid(String value, ConstraintValidatorContext context) {
        return value == null || StringUtils.hasText(value);
    }
}
```
```java
// 유효성 검증을 위해 DTO 클래스으 ㅣ멤버 변수에 Custom Annotation 추가
import javax.validation.constraints.Pattern;

public class MemberPatchDto {
  private long memberId;

  // message 애트리뷰트를 사용하여 유효성 검증 실패 시 표시되는 메시지 지정
  @NotSpace(message = "회원 이름은 공백이 아니어야 합니다")
  private String name;

  @Pattern(regexp = "^010-\\d{3,4}-\\d{4}$",
          message = "휴대폰 번호는 010으로 시작하는 11자리 숫자와 '-'로 구성되어야 합니다")
  private String phone;

  ...
  ... // 각 멤버 변수의 getter, setter 메서드 생략
}
```

---


문의사항 또는 블로그 내 수정이 필요한 부분은<br>
메일로 연락해주시면 확인 및 수정하도록 하겠습니다.<br>
감사합니다.
{: .notice--info}

---