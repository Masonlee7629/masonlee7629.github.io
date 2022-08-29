---
title : "[Git] 기초"

categories:
  - CodeStates

tags:
  - [CodeStates, Git, TIL]

toc: true
toc_sticky: true

date: 2022-08-29
last_modified_at: 2022-08-29
---

### <span style="color:#F05A28">**Git이란?**<span>
<br>
Git은 쉽게 말해 파일을 관리해주는 프로그램이다. 여기서 파일을 관리해준다는 것은 다음을 의미한다.

- 파일의 변경 사항을 추적하며, 사용자가 각 파일의 버전을 관리할 수 있도록 한다.
- 파일의 백업을 지원한다.
- 협업자들과 파일을 공유하고, 각자의 작업물을 취합할 수 있도록 한다.


### <span style="color:#F05A28">**Git과 Github**<span>
<br>

Git     : 로컬에서 버전을 관리해주는 프로그램<br>
Github  : Git이 설치되어 있는 클라우드 저장소<br>


일반적으로 Git 자체는 로컬에서 버전을 관리해주는 프로그램이지만, 백업, 협업을 위한 기능을 활요하려면 온라인 원격 저장소가 필요하다.<br>
이런 원격 저장소 기능을 제공해주는 서비스 중 하나가 Github이다.


### <span style="color:#F05A28">**Git의 기본 명령어**<span>
<br>

**git init**


```
git init
// 로컬 Git 저장소를 설정(초기화)한다.
// 해당 명령어를 입력 후에 추가적인 git 명령어를 사용할 수 있다.
```


**git clone**


```
git clone <Repository URL>
// 원격 저장소로부터 프로젝트를 복제한다.
// 저장소를 clone하면 'origin'이라는 리모트 저장소가 자동으로 등록된다.
```


**git remote**


```
git remote -v
// 현재 프로젝트에 등록된 리모트 저장소를 확인할 수 있다.
// -v 옵션을 사용하면 단축 이름과 URL을 볼 수 있다.
```


**git config**


```
git config
//Git 사용 환경 설정을 확인하고 변경할 수 있다.
```


**git status**


```
git status
// 현재 작업 중인 파일의 상태를 확인한다.
// Work space와 Staging area의 상태를 확인하기 위해 사용한다.
```


**git add**


```
git add <파일명>
// Work space의 변경 내용을 Staging area에 추가한다.
// Git은 커밋하기 전, 인덱스에 먼저 commit할 파일을 추가한다.
// git add --all 또는 git add . 을 사용하면 status의 변경사항을 모두 스테이징 영역에 올려준다.
```


**git commit**


```
git commit -m "Commit Message"
// 파일 및 폴더의 추가 또는 변경 사항을 저장소에 기록한다.
// Staging area에 등록되어 있는 파일 상태를 기록한다.
```


**git log**


```
git log
// 다양한 옵션을 조합하여 원하는 형태의 로그를 출력한다.
```


**git push**


```
git push <저장소명> <브랜치명>
// 로컬 저장소에서 남겨놓은 파일 변경 이력을 원격 저장소로 전송한다.
// -u 옵션을 사용하면 최초 한 번 저장소명과 브랜치명을 입력 후, 생략 가능하다.
```


**git pull**


```
git pull
// 원격저장소에서 변경된 내용을 가져와 병합한다.
```


**git reset**


```
git reset <Options>
// 특정 커밋까지 이력을 초기화한다. 바로 전, 또는 n번 전까지 작업했던 내용을 취소할 수 있다.
// 되돌린 버전 이후의 버전들은 히소토리에서 삭제된다.
```

**git revert**


```
git revert
// 특정 커밋을 취소하는 새로운 커밋을 만든다.
// 되돌린 버전 이후의 버전들의 이력이 남아있다.
```

<br>
<br>

---
<div class="messagebox">
  <span>
    문의사항 또는 블로그 내 수정이 필요한 부분은<br>
    메일로 연락해주시면 확인 및 수정하도록 하겠습니다.<br>
    감사합니다.
  </span>
</div>

---