---
title : "코드스테이츠 학습일지 - 2022-10-26"

categories:
  - CodeStates
excerpt: "Spring MVC - 비즈니스 로직에 대한 예외 처리"
tags:
  - [TIL]

toc: true
toc_sticky: true

date: 2022-10-26
last_modified_at: 2022-10-26
---

## 비즈니스적인 예외 던지기(throw) 및 예외 처리
### 체크 예외(Checked Exception)와 언체크(Unchecked Exception) 예외
> 애플리케이션에서 발생하는 예외(Exception)는 크게 체크 예외(Check Exception)와 언체크 예외(Unchecked Exception)로 구분
> - 체크 예외<br>
> 예외를 잡아서(catch) 체크한 후에 해당 예외를 복구 또는 회피 등의 어떤 구체적인 처리를 해야 하는 예외<br>
> Ex) ClassNotFoundException
> <br>
> <br>
> - 언체크 예외<br>
> 예외를 잡아서(catch) 해당 예외에 대한 어떤 처리를 할 필요가 없는 예외<br>
> Ex) NullPointerException, ArrayIndexOutOfBoundsException

### 개발자가 의도적으로 예외를 던질 수(throw) 있는 상황
> - 백엔드 서버와 외부 시스템과의 연동에서 발생하는 에러 처리<br>
> - 시스템 내부에서 조회하려는 리소스가 없는 경우

### 의도적인 예외 던지기/받기(throw/catch
> 서비스 계층의 메서드는 API계층인 Controller의 핸들러 메서드가 이용<br>
> -> 서비스 계층에서 던져진 예외는 Controller의 핸들러 메서드에서 잡아 처리 가능
> <br>
> <br>
> Controller에서 발생하는 예외를 Exception Advice에서 처리하도록 공통화<br>
> -> 서비스 계층에서 던진 예외 역시 Exception Advice에서 처리 가능

### 사용자 정의 예외(Custom Exception)
> 예외 발생 시 RuntimeException과 같은 추상적인 예외가 이닌 예외를 구체적으로 표현할 수 있는 Custom Exception을 만들어 예외 던지기 가능<br>
> : -> 서비스 계층에서 RuntimeException을 던지고 Exception Advice에서 그대로 잡는 것은 예외의 의도가 명확하지 않고 구체적으로 어떤 예외가 발생했는지에 대한 예외 정보를 얻기 어려움

#### 사용자 정의 예외 순서
> 1. 서비스 계층에서 던질 Custom Exception에 사용할 ExceptionCode를 enum으로 정의
> : -> ExceptionCode를 enum으로 정의하면 비즈니스 로직에서 발생하는 다양한 유형의 예외를 enum에 추가하여 사용 가능
> 2. 서비스 계층에서 사용할 Custom Exception 정의
> : -> 추상적인 RuntimeException 예외를 구체화할 경우 RuntimeException을 상속하며 ExceptionCode를 멤버 변수로 지정하여 생성자를 통해 구체적인 예외 정보를 제공
> : -> 예외 메시지는 상위 클래스인 RuntimeException의 생성자(super)로 전달
> : -> CustomException은 서비스 계층에서 개발자가 의도적으로 예외를 던져야 하는 여러 상황에서 ExceptionCode 정보만 바꿔가며  던질 수 있음
> 3. 서비스 계층에 CustomException 적용
> 4. Exception Advice에서 CustomException 처리

### 핵심 포인트
> - 체크예외는 예외를 잡아서 체크한 후에 해당 예외를 복구 또는 회피 등의 구체적인 처리를 해야하는 예외
> - 언체크 예외는 예외를 잡아서 해당 예외에 대한 어떤 처리를 할 필요가 없는 예의
> - RuntimeException을 상속한 예외는 모두 언체크 예외
> - RuntimeException을 상속해서 개발자가 직접 사용자 정의 예외 생성 가능
> - 사용자 정의 예외를 정의하여 서비스 계층의 비즈니스 로직에서 발생하는 여러 예외를 던질 수 있고 Exception Advice에서 처리 가능
> - @ResponseStatus 애너테이션은 고정된 예외를 처리할 경우에 사용
> - HttpStatus가 동적으로 변경되는 경우 ResponseEntity를 사용

---


문의사항 또는 블로그 내 수정이 필요한 부분은<br>
메일로 연락해주시면 확인 및 수정하도록 하겠습니다.<br>
감사합니다.
{: .notice--info}

---