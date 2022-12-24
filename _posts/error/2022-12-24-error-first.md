---
title : "Response와 데이터베이스의 데이터타입 불일치"

categories:
  - Error & Solution

tags:
  - [Error & Solution]

toc: false
toc_sticky: false

date: 2022-12-24
last_modified_at: 2022-12-24
---

### Error
<br>
데이터베이스의 QUESTIONS 테이블에는 MEMBER_MEMBER_ID 컬럼이 존재하고, 해당 컬럼에는 memberId 값이 정상적으로 데이터가 저장된다.
<br>
<br>
![error_h2console](https://user-images.githubusercontent.com/105192204/209388372-c5448f40-fa91-4c87-b012-cbee9878a81d.png)
<br>
<br>
그러나 Postman 으로 Post 요청 시 저장된 memberId 값이 0으로 모두 0으로 표기되는 오류가 발생했다.
<br>
<br>
![error_postman](https://user-images.githubusercontent.com/105192204/209387884-faca5bea-f185-4e1e-809c-046cc8c98bd7.png)
<br>
<br>
ResponseBody 를 Client 로 전송하는 과정에서 오류가 발생하는 것으로 짐작된다.<br>
따라서 ResponseDto 의 impl 클래스를 확인해보니 memberId 값이 0으로 고정된 것을 볼 수 있다.
<br>
<br>
![error_impl](https://user-images.githubusercontent.com/105192204/209390709-7f51b5ca-759d-4317-8c2b-7fde8000fa90.png)


### Solution
문제는 QUESTIONS 테이블 안에 memberId 라는 필드명이 없기 때문에 매핑이 이루어지지 않은 것으로 파악되었다.<br>
따라서 아래 이미지처럼 ResponseBody 에 해당하는 QuestionDto.Response 에 @Mapping 애너테이션을 사용했다.<br>
@Mapping 애너테이션은 구현체 생성 시 source 가 되는 클래스와 target 이 되는 클래스의 속성명을 비교하고<br>
자동으로 매핑 코드를 작성한다.
<br>
<br>
![solution_mapper](https://user-images.githubusercontent.com/105192204/209393166-3d992f6b-6da4-4ab6-b93a-cc07d0034211.png)
<br>
<br>
개인적으론 위 방법을 진행해도 오류가 해결되지 않아 추가적인 확인이 필요했다.<br>
확인 결과, Member 는 Long memberId, ResponseDto 는 long memberId 로 타입이 달랐다.<br>
타입을 Long 타입으로 동일하게 맞춰주니 ResponseBody 에 memberId 가 정상적으로 포함되어 오류가 해결되었다.
<br>
<br>
> 포스팅하려고 오류를 되짚어서 확인하는 와중에 타입을 일치시키지 않고, 매핑 애너테이션만 사용하여도 오류 없이
> ResponseBody 에 memberId가 정상적으로 포함되는 것을 확인했다.<br>
> 정확한 이유를 파악하진 못했고, 현재로선 단순히 매핑 오류로 이해할 수 밖에 없을 것 같다.
> <br>
> <br>
> PS. 조언을 들었는데, 이전에 빌드된 파일이 오류를 일으켜 매핑을 해도 오류가 발생한 것으로 짐작된다고 하셨다.
> 오류가 발생한 빌드파일은 타입을 맞추는 형식으로 오류를 해결했으나 새로 빌드가 되며 오류가 해결되었기 때문에
> 타입을 맞추지 않아도 오류가 해결된 것이다.
> <br>
> <br>
> 코딩 세계는 역시 봐도 봐도 새롭구만 핳

---
<div class="messagebox">
  <span>
    문의사항 또는 블로그 내 수정이 필요한 부분은<br>
    메일로 연락해주시면 확인 및 수정하도록 하겠습니다.<br>
    감사합니다.
  </span>
</div>

---