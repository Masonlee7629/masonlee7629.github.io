---
title : "[Java] 컬렉션"

categories:
  - CodeStates
excerpt: "Java의 열거형, 제네릭, 예외 처리, 컬렉션 프레임워크"
tags:
  - [CodeStates, Java, TIL]

toc: true
toc_sticky: true

date: 2022-09-14
last_modified_at: 2022-09-14
---

## <span style="color:#F05A28">**Java의 열거형**<span>
### 열거형(Enum)이란?
- 여러 상수들을 보다 편리하게 선언할 수 있도록 만들어진 자바의 문법요소
- 상수명의 중복을 피하고 타입에 대한 안정성을 보장
- 간결하고 가독성이 좋은 코드를 작성할 수 있으며 switch문에서 작동이 가능


### 열거형의 사용
- 대소문자 모두 사용 가능하지만 관례적으로 대문자를 사용
- 값을 따로 지정하지 않아도 자동적으로 0부터 시작하는 정수값이 할당

```java
// 열거형의 사용 방법
enum 열거형이름 {상수명1, 상수명2, 상수명3, ...}

// 열거형 사용 예시
enum Numbering {
  FIRST,
  SECOND,
  THIRD
}

public class Main {
  public static void main(String[] args) {
    Numbering num = Numbering.THIRD;

    switch(num) {
      case FIRST:
        System.out.println("First Number");
        break;
      case SECOND:
        System.out.println("Second Number");
        break;
      case THIRD:
        System.out.println("Third Number");
        break;
    }
  }
}

// 출력 결과
// Third Number
```

### 열거형에서 사용 가능한 메서드
- 해당 메서드는 모든 열거형의 조상인 java.lang.Enum에 정의

|리턴타입|메소드(매개변수)|설명|
|:---:|:---:|:---:|
|String|name()|열거 객체가 가진 문자열을 리턴, 리턴되는 문자열은 열거타입을 정의할 때 사용한 상수명과 동일|
|int|ordinal()|열거 객체의 순번(0부터 시작)을 리턴|
|int|compareTo(비교값)|주어진 매개값과 비교하여 순번 차이를 리턴|
|열거타입|valueOf(String name)|주어진 문자열의 열거 객체를 리턴|
|열거배열|values()|모든 열거 객체들을 배열로 리턴|

## <span style="color:#F05A28">**Java의 제네릭**<span>
### 제네릭(Generic)이란?
- 타입을 추후에 지정할 수 있도록 일반화해두는 것
- 작성한 클래스 또는 메서드의 코드가 특정 데이터 타입에 얽매이지 않게 해둔 것

### 제네릭 클래스
- 제네릭이 사용된 클래스
- 클래스 변수(static)에는 타입 매개변수의 사용 불가능

```java
class 클래스명<타입 매개변수> {}

// 타입 매개변수는 임의의 문자로 지정 가능
// T = Type, K = Key, V = Value, E = Element, N = Number, R = Result 를 주로 사용
```

### 제네릭 클래스의 사용
- 제네릭 클래스를 인스턴스화할 때는 의도하는 타입을 지정해야 함
- 타입 매개변수에 치환될 타입으로 기본 타입의 지정 불가능하며 Integer, Double과 같은 래퍼 클래스를 활용
- 제네릭 클래스 사용 시 다형성 적용 가능

```java
Box<String> box1 = new Box<String>("Java");
Box<Integer> box2 = new Box<Integer>(5);
Box<Double> box3 = new Box<Double>(8.15);

// new Box<...> 는 구체적인 타입의 생략이 가능, 찹조변수의 타입으로 유추
```

### 제한된 제네릭 클래스
- 제네릭 클래스를 인스턴스화할 때 타입으로 특정 하위 클래스 또는 인터페이스를 구현한 클래스만 지정하도록 제한하는 것
- extends 키워드를 사용
- 상속과 인터페이스를 동시에 지정하려면 &를 사용하며 클래스를 인터페이스 보다 앞에 위치시켜야 함

```java
class Toy {...}
class Doll extends Toy {...}
class Snack {...}

// 제네릭 클래스 정의
class Box<T extends Toy> {
  private T item;

  public T getItem() {
    return item;
  }

  public T setItem(T item) {
    this.item = item;
  }
}

public static void main(String[] args) {
  // 인스턴스화
  Box<Toy> toyBox = new Box<>();
  Box<Snack> snackBox = new Box<>(); // 에러
}
```

### 제네릭 메서드
- 클래스 내부의 특정 메서드만 제네릭으로 선언한 것
- 제네릭 매서드의 타입 매개변수는 제네릭 클래스의 타입 매개변수와 별개로 다른 타입 매개변수로 간주함
- 제네릭 클래스는 클래스가 인스턴스화될 때이며 제네릭 메서드는 메서드가 호출될 때 타입 지정
- static 메서드에도 선언하여 사용 가능
- 제네릭 메서드를 정의하는 시점에 실제 타입을 알 수 없으므로 length()와 같은 String 클래스의 메서드는 사용 불가
- Object 클래스의 메서드는 사용 가능

```java
class Box<T> {  //제네릭 클래스의 타입 매개변수
  ...
  public <T> void sum(T item) {  //  제네릭 매서드의 타입 매개변수
    System.out.println(item.length());    // 사용 불가
    System.out.println(item.equals("Mason Lee"));    // 사용 가능
  }
}
```

### 와일드카드
- 제네릭에서 어떠한 타입으로든 대체될 수 있는 타입 파라미터를 의미하며 '?'기호로 와일드카드를 사용
- 일반적으로 extends와 super 키워드를 조합하여 사용

```java
<? extends T>     // 와일드카드에 상한 제한을 두는 것
                  // T와 T를 상속받는 하위 클래스 타입만 타입 파라미터로 받을 수 있도록 지정

<? super T>       // 와일드카드에 하한 제한을 두는 것
                  // T와 T의 상위 클래스만 타입 파라미터로 받을 수 있도록 지정

<?>               // <? extends Object>와 동일하며 모든 클래스 타입을 타입 파라미터로 받을 수 있음
```

## <span style="color:#F05A28">**Java의 예외 처리**<span>
### 예외 처리란?
- 프로그램의 비정상적인 종료를 방지하고 정상적인 실행 상태를 유지하기 위한 것
- 자바에서는 발생 시점에 따라 에러를 컴파일 에러(Compile Time Error)와 런타임 에러(Run Time Error)로 구분

### 컴파일 에러
- 컴파일할 때 발생하는 에러
- 주로 세미콜론 생략, 오탈자, 등 문법적 문제인 신택스(syntax) 오류로부터 발생하므로 신택스 에러(Syntax Error)라고 부르기도 함
- 자바 컴파일러가 오류를 감지하여 사용자에게 알려주기 때문에 상대적으로 쉽게 발견하고 수정 가능

### 런타임 에러
- 런타임 시에 발생하는 에러
- 개발자가 컴퓨터가 수행할 수 없는 작업을 요청할 때 주로 발생
- 자바 가상 머신(JVM)에 의해 감지

### 에러와 예외
- 자바에서 코드 실행(run-time) 시 잠재적으로 발생할 수 있는 오류를 에러와 예외로 구분
- 에러는 복구하기 어려운 수준의 심각한 오류를 의미하며 메모리 부족, 스택오버플로우 등이 있음
- 예외는 잘못된 사용 또는 코딩으로 인한 상대적으로 미약한 수준의 오류로 코드 수정 등을 통해 수습이 가능한 오류
- 예외의 최고 상위 클래스는 Exception 클래스이며 이는 일반 예외 클래스와 실행 예외 클래스로 구분

- 일반 예외 클래스(Exception)
: - 런타임 시 발생하는 RuntimeException 클래스와 그 하위 클래스를 제외한 모든 Exception 클래스와 그 하위 클래스를 지칭
: - 컴파일러가 코드 실행 전에 예외 처리 코드 여부를 검사한다고하여 checked 예외라고도 부름
: - 주로 잘못된 클래스명이나 데이터 형식 등 사용자 편의 실수도 발생

- 실행 예외 클래스(Runtime Exception)
: - RuntimeException 클래스와 그 하위 클래스를 지칭
: - 컴파일러가 예외 처리 코드 여부를 검사하지 않는다는 의미에서 unchecked 예외라고 부름
: - 개발자의 실수에 의해 발생하는 경우가 많으며 자바 문법 요소와 관련

### try - catch문
- 자바에서 예외 처리는 try - catch 문을 통해 구현이 가능
- '예외 발생 - 첫 번째 catch문 - 두 번째 catch문 - finally문'의 과정으로 진행

```java
try {
  // 예외가 발생할 가능성이 있는 코드 삽입
}
catch (ExceptionType1 except1) {
  // ExceptionType1 유옇의 예외 발생 시 실행할 코드
}
catch (ExceptionType2 except2) {
  // ExceptionType2 유옇의 예외 발생 시 실행할 코드
}
finally {
  // 예외 발생 여부와 상관없이 항상 실행
  // finally 블럭은 옵셔널
}
```

### 예외 전가
- 예외를 호출한 곳으로 다시 예외를 떠넘기는 방법
- 메서드의 선언부 끝에 throws 키워드와 발생할 수 있는 예외들을 쉼표로 구분하여 나열
- throw 키워드를 사용하여 의도적으로 예외를 발생시킬 수 있음

```java
반환타입 메서드명(매개변수, ...) throws 예외클래스 1, 예외클래스2... {}

// 모든 종류의 예외가 발생할 가능성이 있는 경우
void ExamMethod() throws Exception {
}
```

```java
// 예외를 의도적으로 발생시키는 경우
public class ThrowExam {
  public static void main(String[] args) {
    try {
      Exception intendedException = new Exception("의도적인 예외");
      throw intendedExcepton;
    } catch (Exception e) {
      System.out.println("의도적인 예외 발생 성공");
    }
  }
}

// 출력 결과
// 의도적인 예외 발생 성공
```

## <span style="color:#F05A28">**Java의 컬렉션 프레임워크**<span>
### 컬렉션 프레임워크(Collection Framework)란?
- 여러 데이터들을 그룹으로 묶어놓은 것을 컬렉션이라 하며 컬렉션 프레임워크는 컬렉션을 다루는 데 편리한 메서드를 미리 정의한 것
- 특정 자료 구조에 데이터를 추가, 삭제, 수정, 검색 등의 동작을 수행하는 편리한 메서드들을 제공
- 주요 인터페이스로 List, Set, Map을 제공

- List
: - 데이터의 순서가 유지되며, 중복 저장이 가능한 컬렉션을 구현할 때 사용
: - ArrayList, Vector, Stack, LinkedList 등이 List 인터페이스를 구현

- Set
: - 데이터의 순서가 유지되지 않으며, 중복 저장이 불가능한 컬렉션을 구현할 때 사용
: - HashSet, TreeSet 등이 Set 인터페이스를 구현

- Map
: - 키(key)와 값(value)의 쌍으로 데이터를 저장하는 컬렉션을 구현할 때 사용
: - 데이터의 순서가 유지되지 않으며 키는 중복 저장이 불가능하고 값은 중복 저장이 가능
: - HashMap, HashTable, TreeMap, Properties 등

### Collection 인터페이스
- List와 Set의 공통점이 추출되어 추상화된 것

|기능|리턴 타입|메소드|설명|
|:---:|:---:|:---:|:---:|
|객체 추가|boolean|add(Object o) / addAll(collection c)|주어진 객체 및 컬렉션의 객체들을 컬렉션에 추가|
|객체 검색|boolean|contains(Object o) / containsAll(Collection c)|주어진 객체 및 컬렉션의 저장 여부를 리턴|
||Iterator|iterator()|컬렉션의 iterator를 리턴|
||boolean|equals(Object o)|컬렉신여 동일한지 여부를 확인|
||boolean|isEmpty()|컬렉션이 비어있는지 여부를 확인
||int|size()|저정되어 있는 전체 객체 수를 리턴|
|객체 삭제|void|clear()|컬렉션에 저장된 모든 객체를 삭제|
||boolean|remove(Object) / removeAll(Collection c)|주어진 객체 및 컬렉션을 삭제하고 성공 여부를 리턴|
||boolean|retainAll(Collection c)|주어진 컬렉션을 제외한 모든 객체를 삭제하고 컬렉션의 변화 여부를 리턴|
|객체 변화|Object[]|toArray()|컬렉션에 저장된 객체를 객체배열로 반환|
||Object[]|toArray(Object a)|주어진 배열에 컬렉션의 객체를 저장해서 반환|

### List 인터페이스
- List 인터페이스는 배열처럼 객체를 일렬로 늘어놓은 구조
- 객체를 인덱스로 관리하므로 객체를 저장하면 자동으로 인덱스가 부여되며 인덱스로 객체를 검색, 추가 , 삭제 등 가능
- List 인터페이스를 구현한 클래스로는 ArrayList, Vector, LinkedList, Stack 등이 존재

|기능|리턴 타입|메서드|설명|
|:---:|:---:|:---:|:---:|
|객체 추가|void|add(int index, Object element)|주어진 인덱스에 객체를 추가|
||boolean|addAll(int index, Collection c)|주어진 인덱스에 컬렉션을 추가|
||Object|set(int index, Object element)|주이진 위치에 객체를 저장|
|객체 검색|Object|get(int index)|주어진 인덱스에 저장된 객체를 반환|
||int|indexOf(Object o) / lastIndexOf(Object o)|순방향 / 역방향으로 탐색하여 주어진 객체의 위치를 반환|
||ListIterator|listIterator() / listIterator(int index)|List의 객체를 탐색할 수 있는 ListIterator 반환 / 주어진 index부터 탐색할 수 있는 ListIterator 반환|
||List|subList(int fromIndex, int toIndex)|fromIndex부터 toIndex에 있는 객체를 반환|
|객체 삭제|Object|remove(int index)|주어진 인덱스에 저장된 객체를 삭제하고 삭제한 객체를 반환|
||boolean|remove(Object o)|주어진 객체를 삭제|
|객체 정렬|void|sort(Comparator c)|주어진 비교자(comparator)로 List를 정렬|

### ArrayList
- List 인터페이스를 구현한 클래스로 컬렉션 프레임워크에서 가장 많이 사용
- 기능적으로 Vector와 동일하지만 기존의 Vector를 개선한 것으로 주로 ArrayList를 사용
- 객체가 인덱스로 관리된다는 점에서 배열과 유사하며 ArrayList는 저장 용량을 초과하여 객체를 추가하면 자동으로 저장 용량이 늘어남
- 리스트 계열 자료구조의 특성을 이어받아 데이터가 연속적으로 존재하며 데이터의 순서를 유지

```java
List<타입 매개변수> 객체명 = new ArrayList<타입 매개변수>(초기 저장 용량);

List<String> box1 = new ArrayList<String>();
// String 타입의 객체를 저장하는 ArrayList 생성
// 초기 용량이 인자로 전달되지 않으면 기본적으로 10으로 지정

List<String> box2 = new ArrayList<String>(20);
// 초기 용량을 20으로 지정
```

### LinkedList
- 데이터를 효율적으로 추가, 삭제, 변경하기 위해 사용
- 데이터가 분연속적으로 존재하며 이 데이터는 서로 연결되어 있음
- LinkedList의 요소(node)들은 자신과 연결된 이전 요소 및 다음 요소의 주소값과 데이터로 구성
- 배열처럼 데이터를 이동하기 위해 복사할 필요가 없으므로 처리 속도가 빠름

### ArrayList와 LinkedList의 차이
- ArrayList
: - 중간에 위치한 객체를 추가 및 삭제할 때는 데이터 이동이 많이 일어나므로 속도가 저하
: - 검색(읽기) 측면에서 인덱스를 통해 바로 데이터에 접근할 수 있으므로 유리
: - 데이터를 순차적으로 추가하거나 삭제하는 경우 유리

- LinkedList
: - 데이터를 중간에 추가하거나 삭제하는 경우 ArrayList보다 속도가 빠름
: - 데이터 검색에 있어서는 ArrayList보다 상대적으로 속도가 느림

### Iterator
- 컬렉션에 저장된 요소들을 순차적으로 읽어오는 역할
- Collection 인터페이스에 정의된 iterator()를 호출하면 Iterator 타입의 인스턴스를 반환
- Collection 인터페이스를 방송받는 List와 Set 인터페이스를 구현한 클래스들은 iterator() 메서드 사용 가능
- iterator()로 생성된 인스턴스는 hasNext(), next(), remove() 메서드 사용 가능

|메서드|설명|
|:---:|:---:|
|hasNext()|읽어올 객체가 남아 있으면 true를 리턴, 없으면 false를 리턴|
|next()|컬렉션에서 하나의 객체를 읽음. 이 때 next()를 호출하기 전에 hasNext()를 통해 읽어올 다음 요소가 있는지 먼저 확인 필요|
|remove()|next()를 통해 읽어온 객체를 삭제. next()를 호출한 다음 remove()를 호출해야 함|

### Set 인터페이스
- 요소의 중복을 허용하지 않고, 저장 순서를 유지하지 않는 컬렉션
- 대표적인 Set을 구현한 클래스로는 HashSet, TreeSet이 있음

|기능|리턴 타입|메서드|설명|
|:---:|:---:|:---:|:---:|
|객체 추가|boolean|add(Object o)|주어진 객체를 추가하고 성공하면 true, 중복 객체면 false를 반환|
|객체 검색|boolean|contains(Object o)|주어진 객체가 Set에 존재하는지 확인|
||boolean|isEmpty()|Set이 비어있는지 확인|
||Iterator|Iterator()|저장된 객체를 하나씩 읽어오는 반복자를 리턴|
||int|size()|저장되어 있는 전체 객체의 수를 리턴|
|객체 삭제|void|clear()|Set에 저장되어 있는 모든 객체를 삭제|
||boolean|remove(Object o)|주어진 객체를 삭제|

### HashSet
- Set 인터페이스를 구현한 대표적인 컬렉션 클래스
- Set 인터페이스의 특성을 물려받아 중복된 값을 허용하지 않으며 저장 순서를 유지하지 않음

- HashSet에 값을 추가할 때 중복된 값을 판단하는 과정
1. add(Object o)를 통해 객체를 저장하고자 한다
2. 저장하고자 하는 객체의 해시코드를 hashCode() 메서드로 얻어낸다
3. Set에 저장하고 있는 모든 객체들의 해시코드를 hashCode() 메서드로 얻어낸다
4. 저장하고자 하는 객체와 Set에 저장되어 있는 객체들의 해시코드를 비교하여 같은 해시코드가 있는지 검사한다
  : - 만약 같은 해시코드를 가진 객체가 있다면 5번으로 넘어간다
  : - 같은 해시코드를 가진 객체가 없다면 Set에 객체가 추가되며 add(Object o) 메서드가 true를 리턴한다
5. equals() 메서드로 객체를 비교한다
  : - true가 리턴되면 중복 객체로 간주되어 Set에 추가되지 않고 add(Object o)가 false를 리턴한다
  : - false가 리턴되면 Set에 객체가 추가되며 add(Object o) 메서드가 true를 리턴한다

### TreeSet
- 이진 탐색 트리 형태로 데이터를 저장하며 데이터의 중복 저장을 허용하지 않고 저장 순서를 유지하지 않음
- 하나의 부모 노드가 최대 두 개의 자식 노드와 연결되는 이진 트리(Binary Tree)의 일종으로 정렬과 검색에 특화된 자료 구조
- 모든 왼쪽 자식의 값이 루트나 부모보다 작고, 모든 오른쪽 자식의 값이 루트나 부모보다 큰 값을 가짐

### Map<K, V>
- Map 인터페이스는 키(key)와 값(value)으로 구성된 객체를 저장하는 구조를 지님
- 해당 객체를 Entry객체라고 하며 키와 값을 각각 Key객체와 Value객체로 저장
- 키는 중복 저장될 수 없지만 값을 중복 저장이 가능하며 기존에 저장된 키와 동일한 키로 값을 저장하면 기존의 값이 새로운 값으로 대치

|기능|리턴 타입|메서드|설명|
|:---:|:---:|:---:|:---:|
|객체 추가|Object|put(Object key, Object value)|주어진 키로 값을 저장. 해당 키가 새로운 키면 null을 리턴하고 동일한 키가 있으면 기존의 값을 대체하고 대체되기 이전의 값을 리턴|
|객체 검색|boolean|containsKey(Object key)|주어진 키가 있으면 true, 없으면 false를 리턴|
||boolean|containsValue(Object value)|주어진 값이 있으면 true, 없으면 false를 리턴|
||Set|entrySet()|키와 값의 쌍으로 구성된 모든 Map.Entry객체를 Set에 담아 리턴|
||Object|get(Object key)|주어진 키에 해당하는 값을 리턴|
||boolean|isEmpty()|컬렉션이 비어 있는지 확인|
||Set|keySet()|모든 키를 Set객체에 담아 리턴|
||int|size()|저장된 Entry객체의 총 갯수를 리턴|
||Collection|values()|저장된 모든 값을 Collection에 담아 리턴|
|객체 삭제|void|clear()|모든 Map.Entry(키와 값)을 삭제|
||Object|remove(Object key)|주어진 키와 일치하는 Map.Entry를 삭제하고 값을 리턴|

### HashMap
- Map 인터페이스를 구현한 대표적인 클래스로 키와 값으로 구성된 Entry객체를 저장
- 해싱(Hashing)을 사용하여 많은 양의 데이터를 검색하는 것에 뛰어난 성능을 보임
- HashMap을 생성할 때는 키와 값의 타입을 따로 지정해야 함
- Entry객체는 Map 인터페이스의 내부 인터페이스인 Entry 인터페이스를 구현하여 아래의 표와 같은 메서드가 정의되어 있음

|리턴 타입|메서드|설명|
|:---:|:---:|:---:|
|boolean|equals(Object o)|동일한 Entry 객체인지 비교|
|Object|getKey()|Entry 객체의 Key 객체를 반환|
|Object|getValue()|Entry 객체의 Value 객체를 반환|
|int|hashCode()|Entry 객체의 해시코드를 반환|
|Object|setValue(Object value)|Entry 객체의 Value 객체를 인자로 전달한 Value 객체로 변경|



---

학습량이 어마어마했던 하루였다.<br>
정리할 것도 많고 이해되지 않는 부분도 너무 많아 습득하는 데 시간이 상당히 필요할 것 같다.<br>
내일 문제를 풀면서 최대한 이해할 수 있도록 해보자!
{: .notice--primary}

---

문의사항 또는 블로그 내 수정이 필요한 부분은<br>
메일로 연락해주시면 확인 및 수정하도록 하겠습니다.<br>
감사합니다.
{: .notice--info}

---