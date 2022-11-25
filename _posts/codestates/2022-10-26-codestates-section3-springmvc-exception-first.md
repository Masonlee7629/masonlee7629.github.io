---
title : "코드스테이츠 학습일지 - 2022-10-25"

categories:
  - CodeStates
excerpt: "Spring MVC - Spring MVC에서의 예외 처리"
tags:
  - [TIL]

toc: true
toc_sticky: true

date: 2022-10-26
last_modified_at: 2022-11-26
---

## Spring MVC에서의 예외 처리
### @ExceptionHandler를 이용한 예외 처리
> Spring에서의 예외는 애플리케이션에 문제가 발생할 경우, 이 문제를 알려서 처리하는 것 뿐만 아니라 유효성 검증에 실패했을 떄와 같이 
> 이 실패를 하나의 예외로 간주하여 이 예외를 던져서(throw) 예외 처리를 유도함

- Controller 클래스에 handleException() 메서드를 추가

```java
@RestController
@RequestMapping("/v1/members")
public class Controller {
  // 유효성 검증 과정에 실패할 떄의 예외 처리
  
  @ExceptionHandler
  public ResponseEntity handleException(MethodArgumentNotValidException e) {
      final List<FieldError> fieldErrors = e.getBindingResult().getFieldErrors();

      return new ResponseEntity><>(fieldErrors, HttpStatus.BAD_REQUEST);
  }
}
```

- 유효하지 않은 이메일 주소를 포함한 POST 요청 전송에 대한 응답 메시지

```java
// 현재는 Response Body 전체 정보를 전달
[
    {
        "codes": [
            "Email.memberPostDto.email",
            "Email.email",
            "Email.java.lang.String",
            "Email"
        ],
        "arguments": [
            {
                "codes": [
                    "memberPostDto.email",
                    "email"
                ],
                "arguments": null,
                "defaultMessage": "email",
                "code": "email"
            },
            [],
            {
                "arguments": null,
                "defaultMessage": ".*",
                "codes": [
                    ".*"
                ]
            }
        ],
        "defaultMessage": "올바른 형식의 이메일 주소여야 합니다",
        "objectName": "memberPostDto",
        "field": "email",
        "rejectedValue": "hgd@",
        "bindingFailure": false,
        "code": "Email"
    }
]
```

> 요청 전송 시 Request Body의 JSON 프로퍼티 중 문제가 된 프로퍼티와 에러메시지만 전달 가능
> - 에러 정보를 기반으로 한 Error Response 클래스를 만들어 필요한 정보만 담은 후 클라이언트에 전달

- ErrorResponse 클래스 적용
> 한 개 이상의 유효성 검증에 실패한 필드의 에러 정보를 담기 위해 List 객체를 이용<br>
> 하나의 필드 에러 정보는 FieldError 라는 별도의 static class를 ErrorResponse 클래스의 멤버 클래스로 정의

```java
@Getter
@AllArgsConstructor
public class ErrorResponse {
  private List<FieldError> fieldErrors;

  @Getter
  @AllArgsConstructor
  public static class FieldError {
    private String field;
    private Object rejectedValue;
    private String reason;
  }
}
```

- ErrorResponse 를 사용하도록 Controllerdml handleException() 메서드 수정

```java
public class Controller {
  @ExceptionHandler
  public ResponseEntity handleException(MethodArgumentNotValidException e) {
    final List<FieldError> fieldErrors = e.getBindingResult().getFieldErrors();

    List<ErrorResponse.FieldError> errors =
            fieldErrors.stream()
                    .map(error -> new ErrorResponse.FieldError(
                      error.getField(),
                      error.getRejectedValue(),
                      error.getDefaultMessage()))
                    .collect(Collectors.toList());
    
    return new ResponseEntity<>(new ErrorResponse(errors), HttpStatus.BAD_REQUEST);
  }
}
```

#### @ExceptionHandler의 단점
1. 각각의 Controller 클래스에서 @ExceptionHandler 애너테이션을 사용하셔 Request Body에 대한 유효성 검증 실패에 대한 에러 처리를 해야하므로
각 Controller 클래스마다 코드 중복이 발생
2. Controller에서 처리해야하는 예외가 유효성 검증 실패에 대한 예외만 있는 것이 아니므로 Controller 클래스 내에서 @ExceptionHandler를 추가한
에러 처리 핸들러 메서드가 증가함

> 위 단점을 @RestControllerAdvice 애너테이션을 추가한 클래스를 이용하여 예외 처리를 공통화

### @RestControllerAdvice를 이용한 예외 처리
> - 예외 처리의 공통화 가능
> <br>
> <br>
> 특정 클래스에 @RestControllerAdvice 애너테이션을 추가하면 다수의 Controller 클래스에서 @ExceptionHandler, @InitBinder, @ModelAttribute 가 추가된 메서드를 공유하여 사용
> <br>
> <br>
> 기존 Controller 클래스에서 사용한 @ExceptionHandler 가 추가된 메서드는 특정 클래스에서 공통으로 처리되므로 제거

#### @RestControllerAdvice 적용 순서
1. ExceptionAdvice 클래스 정의
  - Controller 클래스에서 발생하는 예외들을 공통으로 처리할 클래스 정의
2. Exception 핸들러 메서드 구현
3. ErrorResponse 수정
4. Exception 핸들러 메서드 수정

#### @RestControllerAdvice 적용 방식

- ExectionAdvice 클래스 정의

```java
@RestControllerAdvice
public class GlobalExceptionAdvice {

}
```

- Exception 핸들러 메서드 구현
> handleConstraintViolationException() 메서드는 ErrorResponse 클래스가 ConstraintViolationException 에 대한 Error Response를 생성할 수 없기에 ErrorResponse 클래스의 추가 수정 필요

```java
@RestControllerAdvice
public class GlobalExceptionAdvice {
  @ExceptionHandler
  public ResponseEntity handleMethodArgumentNotValidException(MethodArgumentNotValidException e) {
    final List<FieldError> fieldErrors = e.getBindingResult().getFieldErrors();

    List<ErrorResponse.FieltError> errors = 
          fieldErrors.stream()
                  .map(error -> new ErrorResponse.FieldError(
                    error.getField(),
                    error.getRejectedValue(),
                    error.getDefaultMessage()))
                  .collect(Collectors.toList());
    
    return new ResponseEntity<>(new ErrorResponse(errors), HttpStatus.BAD_REQUEST);
  }

  @ExceptionHandler
  public ResponseEntity handleConstraintViolationException(ConstraintViolationException e) {
    return new ResponseEntity<>(HttpStatus.BAD_REQUEST);
  }
}
```

- ErrorResponse 수정

(1) DTO 멤버 변수 필드의 유효성 검증 실패로 발생한 에러 정보를 담는 멤버 변수
<br>
<br>
(2) URI 변수 값의 유효성 검증에 실패로 발생한 에러 정보를 담는 멤버 변수
<br>
<br>
(3) ErrorResponse 클래스의 생성자
- private 접근 제한자를 지정
  - new ErrorResponse 방식으로 객체 생성 불가능
  - (4), (5)와 같이 of() 메서드로 ErrorResponse 의 객체 생성 가능
    - ErrorResponse 의 객체를 생성함과 동시에 역할을 명확히 함

(4) MethodArgumentNtoValidException 에 대한 ErrorResponse 객체를 생성
- MethodArgumentNotValidException 에서 에러 정보를 얻기 위해 필요한 것은 BindingResult
- of() 메서드를 호출하는 쪽에서 BindingResult 객체를 파라미터로 전달
  - BindingResult 객체로 에러 정보를 추출, 가공하는 것은 FieldError 클래스에게 위임

(5) ConstraintViolationException 에 대한 ErrorResponse 객체를 생성
- ConstraintViolationException 에서 에러 정보를 얻기 위해 필요한 것은 Set<ConstraintViolation<?>> 객체
- of() 메서드를 호출하는 쪽에서 Set<ConstraintViolation<?>> 객체를 파라미터로 전달
  - Set<ConstraintViolation<?>> 객체로 에러 정보를 추출, 가공하는 것은 ConstraintViolationError 클래스에게 위임

(6) 필드(DTO 클래스의 멤버 변수)의 유효성 검증에서 발생하는 에러 정보를 생성
<br>
<br>
(7) URI 변수 값에 대한 에러 정보를 생성

```java
@Getter
public class ErrorResponse {
  private List<FieldError> fieldErrors;   // (1)
  private List<ConstraintViolationError> violationErrors;   // (2)

  // (3)
  private ErrorResponse(final List<FieldError> fieldErrors,
                        final List<ConstraintViolationError> violationErrors) {
    this.fieldErrors = fieldErros;
    this.violationErrors = violationErrors;
  }

  // (4) BindinResult에 대한 ErrorResponse 객체 생성
  public static ErrorResponse of(BindingResult bindingResult) {
    return new ErrorResponse(FieldError.of(bindingResult), null);
  }

  // (5) Set<ConstraintViolation<?>> 객체에 대한 ErrorResponse 객체 생성
  public static ErrorResponse of(Set<ConstraintViolation<?>> violation) {
    return new ErrorResponse(null, ConstraintViolationError.of(violation));
  }

  // (6) Field Error 가공
  @Getter
  public static class FieldError {
    private String field;
    private Object rejectedValue;
    private String reason;

    private FieldError(String field, Object rejectedValue, String reason) {
      this.field = field;
      this.rejectedValue = rejectedValue;
      this.reason = reason;
    }

    private static List<FieldError> of(BindingResult bindingResult) {
      final List<org.springframework.validation.FieldError> fieldErrors = 
              bindingResult.getFieldErrors();

      return fieldErrors.stream()
          .map(error -> new FieldError(
            error.getField(),
            error.getRejectedValue() == ? "" : error.getRejectedValue().toString(),
            error.getDefaultMessage()))
          .collect(Collectors.toList());
    }
  }

  // (7) ConstraintViolation Error 가공
  @Getter
  public static class ConstraintViolationError {
    private String propertyPath;
    private Object rejectedValue;
    private String reason;

    private ConstraintViolationError(String propertyPath,
                                     Object rejectedValue,
                                     String reason) {
      this.propertyPath = propertyPath;
      this.rejectedValue = rejectedValue;
      this.reason = reason;
    }

    public static List<ConstraintViolationError> of(
            Set<ConstraintViolation<?>> constraintViolations) {
      return constraintViolations.stream()
              .map(constraintViolation -> new ConstraintViolationError(
                constraintViolation.getPropertyPath().toString(),
                constraintViolation.getInvalidValue().toString(),
                constraintViolation.getMessage()))
              .collect(Collectors.toList());
    }
  }
}
```

- Exception 핸들러 메서드 수정

```java
@RestControllerAdvice
public class GlobalExceptionAdvice {
  @ExceptionHandler
  @ResponseStTUS(HttpStatus.BAD_REQUEST)
  public ErrorResponse handleMethodArgumentNotValidException(
    MethodArgumentNotValidException e) {
     final ErrorResponse response = ErrorResponse.of(e.getBindingResult());

     return response;
  }

  @ExceptionHandler
  @ResponseStatus(HttpStatus.BAD_REQEUST)
  public ErrorResponse handleConstraintViolationException(
    ConstraintViolationException e) {
      final ErrorResponse response = ErrorResponse.of(e.getConstraintViolations());

      return response;
  }
}
```

---


문의사항 또는 블로그 내 수정이 필요한 부분은<br>
메일로 연락해주시면 확인 및 수정하도록 하겠습니다.<br>
감사합니다.
{: .notice--info}

---