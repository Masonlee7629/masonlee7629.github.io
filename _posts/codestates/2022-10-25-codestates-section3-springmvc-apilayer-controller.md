---
title : "코드스테이츠 학습일지 - 2022-10-20 part 2"

categories:
  - CodeStates
excerpt: "Spring MVC - Controller"
tags:
  - [TIL]

toc: true
toc_sticky: true

date: 2022-10-25
last_modified_at: 2022-10-25
---

## Spring MVC - Controller
> Spring MVC에서 클라이언트 요청의 최종 목적지

### Controller 클래스 설계 및 구조 생성
> 애플리케이션 제작을 위한 사전 준비
> 1. 애플리케이션의 경계를 설정
> 2. 애플리케이션 기능 구현을 위한 요구 사항을 수집

#### 패키지 구조 종류
> Spring Boot 팀에서는 **기능 기반 패키지구조**의 사용을 권장<br>
> : 테스트와 리팩토링이 용이하고 향후 마이크로 서비스 시스템으로의 분리가 상대적으로 용이함

- 기능 기반 패키지 구조(package-by-feature)
> 애플리케이션의 패키지를 애플리케이션에서 구현해야 하는 기능을 기준으로 패키지를 구성하는 것<br>
> 하나의 기능을 완성하기 위한 계층별(API계층, 서비스 계층, 데이터 액세스 계층) 클래스들을 모아 패키지로 구성<br>
> ![image](https://user-images.githubusercontent.com/105192204/199191663-a09a7f79-a02b-45dc-89c6-2382a35ad1f8.png)

- 계층 기반 패키지 구조(package-by-layer)
> 애플리케이션의 패키지를 하나의 계층(Layer)으로 보고 클래스를 계층별로 묶어서 구성하는 것<br>
> Controller, dto패키지는 API 계층에 해당<br>
> model, service패키지는 비즈니스 계층에 해당<br>
> repository패키지는 데이터 액세스 계층에 해당<br>
> ![image](https://user-images.githubusercontent.com/105192204/199192661-d46ce7fa-8bb7-441a-941a-a6ef31840694.png)

#### Controller 설계
> REST API 기반의 애플리케이션에선 일반적으로 애플리케이션이 제공해야 될 기능을 리소스로 분류<br>
> 리소스에 해당하는 Controller 클래스를 작성

#### Controller 구조 생성
- 엔트리포인트 클래스 작성
> Spring Boot 기반의 애플리케이션을 실행하기 위해 가장 먼저 해야할 것<br>
> main() 메서드가 포함된 애플리케이션의 엔트리포인트(Entrypoint, 애플리케이션 시작점)를 작성<br>

```java
import org.springframework.boot.SpringApplication;
import org.springframework.boot.autoconfigure.SpringBootApplication;

@SpringBootApplication    // (1)
public class SpringBootApplication {
  public static void main(String[] args) {
    // (2)
    SpringApplication.run(SpringBootApplication.class, args);
  }
}
```

> 1. @SpringBootApplication<br>
> - 자동 구성을 활성화
> - 애플리케이션 패키지 내에서 @Component가 붙은 클래스를 검색한 후 Spring Bean으로 등록하는 기능을 활성화
> - @Configuration 이 붙은 클래스를 자동으로 찾아 추가적으로 Spring Bean을 등록하는 기능을 활성화
> <br>
> <br>
> 2. SpringApplication.run(SpringBootApplication.class, args);<br>
> - Spring 애플리케이션을 부트스트랩하고 실행하는 역할
> : 부트스트랩 : 애플리케이션이 실행되기 전에 여러 설정 작업을 수행하여 실행 가능한 애플리케이션으로 만드는 단계를 의미

- Controller 구조 작성

```java
import org.springframework.web.bind.annotation.RequestMapping;
import org.springframework.web.bind.annotation.RestController;

@RestController                   // (1)
@RequestMapping("/v1/members")    // (2)
public class MemberController {
}
```

> 1. @RestController
> - @RestController 애너테이션이 추가된 클래스는 REST API의 리소스를 처리하기 위한 API 엔드포인트로 동작함을 정의
> - @RestController 가 추가된 클래스는 애플리케이션 로딩 시 Spring Bean으로 등록
> <br>
> <br>
> 2. @RequestMaping
> - 클라이언트의 요청과 해당 요청을 처리하는 핸들러 메서드를 매핑하는 역할
> - Controller 클래스 레벨에 추가하면 클래스 전체에 사용되는 공통 URL(Base URL)로 설정

### 핸들러 메서드
> Controller 클래스 안에 구현된 클라이언트의 요청을 처리하는 메서드를 의미<br>
> 핸들러 메서드를 작성하기 전에 각 핸들러 메서드에 필요한 기능별 정보를 정의해야 함<br>

#### 핸들러 메서드 작성
> - @PostMapping<br>
> 클라이언트의 요청 데이터(Request Body)를 서버에 생성(등록)할 때 사용하는 애너테이션<br>
> 핸들러 메서드 레벨에 추가<br>
> 일반적으로 POST Method를 처리하는 핸들러 메서드는 데이터 생성 후 클라이언트에 생성한 데이터를 리턴하는 것이 관례
> <br>
> <br>
> - @GetMapping<br>
> 클라이언트가 서버에 리소스를 조회할 때 사용하는 애너테이션<br>
> 핸들러 메서드 레벨에 추가<br>
> @GetMapping("/{member-id}") 와 같이 애트리뷰트를 사용하여 전체 HTTP URI의 일부를 지정 가능<br>
> 클라이언트에서 @GetMapping("/{member-id}") 애너테이션을 사용한 핸들러 메서드에 요청을 보낼 경우<br>
> -> 최종 URI : "/v1/members/{member-id}"
> <br>
> <br>
> - @PatchMapping<br>
> 클라이언트의 요청 데이터로 서버의 데이터를 수정하는 애너테이션<br>
> 핸들러 메서드 레벨에 추가<br>
> @PatchMapping("/{member-id}") 와 같이 애트리뷰트를 사용하여 해당 URI에 등록된 데이터 수정
> <br>
> <br>
> - @DeleteMapping<br>
> 클라이언트가 서버의 리소스를 삭제할 때 사용하는 애너테이션<br>
> 핸들러 메서드 레벨에 추가<br>
> @DeleteMapping("/{member-id}") 와 같이 애트리뷰트를 사용하여 해당 URI에 등록된 데이터 삭제
> <br>
> <br>
> - @RequestParam<br>
> 핸들러 메서드의 파라미터 종류 중 하나<br>
> 주로 클라이언트에서 전송하는 요청 데이터를 쿼리 파라미터, 폼 데이터, x-www-form-urlencoded 형식으로 전송하면 이를 서버에서 전달 받을 때 사용하는 애너테이션
> <br>
> <br>
> - @PathVariable
> 핸들러 메서드의 파라미터 종류 중 하나<br>
> @PathVariable의 괄호 안에 입력한 문자열 값은 @GetMapping("/{member-id}") 처럼 중괄호({ }) 안의 문자열과 동일해야 함<br>
> 두 문자열이 다르면 MissingPathVariableException이 발생

#### 응답 데이터에 ResponseEntity 적용
> 리턴 값으로 ResponseEntity 객체를 리턴<br>
> 응답 데이터로 Map 객체를 리턴하면 Spring MVC 내부적으로 JSON 형식의 데이터를 생성<br>
> -> 생성자 파라미터로 응답 데이터와 HTTP 응답 상태를 전달하여 클라이언트의 요청을 서버가 어떻게 처리했는지 확인 가능

---


문의사항 또는 블로그 내 수정이 필요한 부분은<br>
메일로 연락해주시면 확인 및 수정하도록 하겠습니다.<br>
감사합니다.
{: .notice--info}

---