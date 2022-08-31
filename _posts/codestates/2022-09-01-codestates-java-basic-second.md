---
title : "[Java] 기초 -2"

categories:
  - CodeStates
excerpt: "Java의 조건문과 반복문"
tags:
  - [CodeStates, Java, TIL]

toc: true
toc_sticky: true

date: 2022-09-01
last_modified_at: 2022-09-01
---

### <span style="color:#F05A28">**Java의 제어문**<span>
<br>

Java의 조건문과 반복문을 통틀어 제어문이라고 한다.


조건문을 사용하면 특정 조건에 부합하는 경우에 특정 코드를 실행 또는 실행시키지 않을 수 있다.<br>
반복문을 사용하면 특정 코드를 반복적으로 실행시킬 수 있다.


- 제어문
  - 조건문 : if문, switch문
  - 반복문 : for문, while문, do while문

---

### <span style="color:#F05A28">**Java의 조건문**<span>
<br>

if문
: - if문은 소괄호 안에 boolean값으로 평가될 수 있는 조건식을 넣고, 실행 블록인 중괄호에는 
조건식이 참일 때 실행할 코드를 입력한다.

```java
if(조건식){
  // 조건식이 참일 때 실행될 블록
}
```


<br>

if~else문
: - if~else문은 if문의 조건식이 true일 경우 해당 블록이 실행되고 조건식이 false이면 다음 조건문인 
else if문의 조건식을 확인한다.  else if문의 모든 조건식이 false일 때 else블록을 실행한다.

```java
if(조건식1){
  // 조건식1이 참일 때 실행되는 블록
}
else if(조건식2){
  //  조건식1이 거짓이고 조건식 2가 참일 때 실행되는 블록
}
else {
  // 조건식1, 2 모두 거짓일 때 실행되는 블록으로 생략 가능
}
```

<br>

switch문
: - switch문은 변수가 값에 따라 실행문이 선택되므로 처리할 경우의 수가 많은 경우에 if문보다 
switch문으로 작성하는 것이 좋다.
: - 조건식의 결과는 정수 또는 문자열이어야 하며 case문의 값은 정수, 상수만 가능하고 중복이 없어야 한다.
: - break문은 각 case문의 영역을 구분하는 역할을 하며 경우에 따라 고의적으로 생략하기도 한다.

```java
switch(조건식){
  case value1 :
    // 조건식의 결과가 value1과 같을 때 실행되는 블록
    Syetem.out.println("Hello");
    break;
  case value2 :
    // 조건식의 결과가 value2와 같을 때 실행되는 블록\
    Syetem.out.println("Java");
    break;
  default :
    // 조건식의 결과와 일치하는 case문이 없을 때 실행되는 블록
    Syetem.out.println("Hello Java");
}
```

<br>

---

### <span style="color:#F05A28">**Java의 반복문**<span>
<br>

for문
: - 조건식이 참인 동안 주어진 횟수만큼 실행문을 반복적으로 실행한다.

```java
public class Main {
  public static void main(String[] args){    
    int tmp = 0;
    for(int i = 0; i < 10; i++){  // 초기화; 조건식; 증감식
      tmp += i;
    }
    System.out.println(tmp);      // 1~9의 합인 45를 출력한다.
  }
}
```

<br>

while문
: - while문은 조건식이 true일 경우 계속해서 반복한다.
: - 조건식에 true를 사용하면 무한 루프를 돌기 때문에 while문의 탈출 코드가 필요하다.

```java
int num = 0, sum = 0; // 초기화
while(num <= 11){     // 조건식
  sum += num;         // 조건식이 참인 경우 계속 실행되는 블록
  num ++;             // 증감식
}
System.out.println(sum);
```

<br>

do-while문
: - do-while문은 실행문을 처음 한 번은 무조건 실행한 후 조건식에 따라 while문이 실행된다.

```java
do {
                  // 처음 한 번은 무조건 실행되는 블록
} while(조건식);  // 끝에 ';'를 잊지 않도록 주의한다. 
```

<br>

break문
: - break문은 자신이 포함된 가장 가까운 반복문을 벗어난다.

```java
public class Main{
  public static void main(String[] args){
    int sum = 0, i = 0;

    while(true){
      if(sum > 100)
        break;  // break문이 실행되면 break문 아래 코드를 실행하지 않고 while문을 탈출한다.
      ++i;
      sum += i;
    }
    System.out.println("sum = " + sum);
  }
}
```

<br>

continue문
: - continue문은 반복문 내에서만 사용되며 반복문 실행 중 continue문을 만나면 다음 차례의 반복을 수행한다.
: - 반복 중 특정 조건을 만족하는 경우를 제외할 때 유용하다.

```java
public class Main{
  public static void main(String[] args){
    for(int i = 0; i <= 10; i++){
      if(i % 2 == 0)
        continue; // 조건식이 참일 때 실행되며 블록 끝으로 이동하여 다음 반복을 실행한다.
      Syetem.out.println(i);
    }
  }
}
```


<br>

---

시간의 부족함을 격하게 느끼는 중입니다...
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