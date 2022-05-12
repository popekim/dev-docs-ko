---
title: "POCU 아카데미용 Java 코딩 표준"
date: 2021-03-27
---

* 원문(영어): [POCU Java Coding Standards](https://docs.google.com/document/d/1F6A1yc6ZfeLyzAjmPjdJZwLXTd-QXy_MhGA_so7NlSQ/edit)

## 머리말
### 기본 원칙

1. 가독성을 최우선으로 삼는다. (대부분의 경우 코드 그 자체가 문서의 역할을 해야 함) 
2. 정말 합당한 이유가 있지 않는 한, 통합개발환경(IDE)의 자동 서식을 따른다. (윈도우 IntelliJ의 "Ctrl + Alt + L" 기능)
3. 본 코딩표준을 따라 잘 짜여진 기존의 코드에서 배운다.

## 참조문서
이 코딩 표준은 아래의 코딩 표준들에서 영감을 얻었음.
* [Google Java Style Guide](https://google.github.io/styleguide/javaguide.html)
* [Android Open Source Project Java Code Style for Contributors](https://source.android.com/setup/contribute/code-style)
* [Java Code Conventions](https://www.oracle.com/technetwork/java/codeconventions-150003.pdf) by Oracle


## I. 메인 코딩 표준
1. 패키지 이름은 모두 소문자로 작성한다.
    ```java
    package com.awesome.math;
    ```

2. `import`를 할 때는 전체 이름을 다 적는다. (*를 사용하지 않음)
    **틀린 방식:**
    ```java
    import com.awesome.*;
    ```

    **올바른 방식:**
    ```java
    import foo.bar;
    ```

3. 클래스와 열거형을 선언할 때는 파스칼 표기법을 따른다.
    
    ```java
    public class PlayerManager {
        // 코드 생략
    }
    ```

    ```java
    public enum AccountType {
        // 열거형 멤버 생략
    }
    ```

4. 클래스, 멤버 변수, 메서드에는 언제나 접근 제어자를 붙인다. 단, 기본 패키지 접근 권한이 필요할 경우에는 그렇지 않는다.

    ```java
    public class Person {
        int mHeight; // 기본 (패키지) 접근 권한
        private int age;

        public int getAge() {
            // 메서드 구현 생략
        }

        private void doSomething() {
            // 메서드 구현 생략
        }
    }
    ```

5. 접근 제어자는 다른 수정자(modifier)앞에 붙인다.

    **틀린 방식:**
    ```java
    static public void doSomething() {
        // 메서드 구현 생략
    }
    ```

    **올바른 방식:**
    ```java
    public static void doSomething() {
        // 메서드 구현 생략
    }
    ```

6. 모든 메서드 이름은 카멜 표기법을 따라 짓는다.
    ```java
    public int getAge() {
        // 메서드 구현 생략
    }
    ```

7. 지역 변수와 메서드 매개변수 이름은 카멜 표기법을 따라 짓는다.
    
    ```java
    int age = 10;

    public void someMethod(int someParameter) {
        int someNumber;
    }
    ```

8. 메서드 이름은 동사로 시작한다.
    ```java
    public int getAge() {
        // 메서드 구현 생략
    }
    ```

9. 상수로 사용하는 `final` 필드의 변수명은 모두 대문자로 하되 밑줄로 각 단어를 분리한다.
    ```java
    final int SOME_CONSTANT = 1;
    ```

10. 인터페이스의 이름은 `I`로 시작한다.

    ```java
    interface IFlyable;
    ```

11. 열거형 멤버의 이름은 모두 대문자로 하되 밑줄로 각 단어를 분리한다.

    ```java
    public enum MyEnum {
        FUN,
        MY_AWESOME_VALUE
    }
    ```

12. 멤버 변수의 이름은 카멜 표기법을 따른다.

    ```java
    public class Employee {
        public String nickName;
        protected String familyName;
        private int age;
    }
    ```

13. 값을 반환하는 메서드의 이름은 무엇을 반환하는지 알 수 있게 짓는다.

    ```java
    public int getAge();
    ```

14. 단순히 반복문에 사용되는 변수가 아닌 경우엔 `i`, `e` 같은 변수명 대신 `index`, `employee` 처럼 변수에 저장되는 데이터를 한 눈에 알아볼 수 있는 변수명을 사용한다.

    ```java
    int i; // BAD
    int a; // BAD
    int index; // GOOD
    int age; // GOOD
    ```

    ```java
    // GOOD
    for (int i = 0; i < 10; ++i) {

    }
    ```

15. 줄임말(축약어)를 변수 및 메서드 명에 사용할 때는 기타 단어들과 동일하게 사용한다. 즉, 파스칼 표기법을 따르는 경우에는 오직 첫 번째 글자만 대문자로 바꾸며, 카멜 표기법을 따르는 경우에는 두 번째 단어부터 첫 번째 글자만 대문자로 바꾼다.

    ```java
    int orderId
    String httpAddress;
    String myHttp;
    ```

16. `public` 멤버 변수 대신 getter와 setter 메서드를 사용한다.

    **틀린 방식:**
    ```java
    public class Employee {
        public String Name;
    }
    ```

    **올바른 방식:**
    ```java
    public class Employee {
        private String name;

        public String getName();
        public String setName(String name);
    }
    ```

17. 지역 변수를 선언할 때는 그 지역 변수를 사용하는 코드와 동일한 줄에 선언하는 것을 원칙으로 한다.

18. `double`이 반드시 필요한 경우가 아닌 이상 부동 소수점 값에 `f`를 붙여준다.

    ```java
    float f = 0.5f;
    ```

19. `switch` 문에 언제나 `default:` 케이스를 넣는다.

    ```java
    switch (number) {
        case 0:
            ... 
            break;
        default:
            break;
    }
    ```

20. `switch` / `case` 문 끝에 `break`를 넣지 않고 그 바로 아래 `case` 문의 코드를 실행하고 싶은 경우 `// intentional fallthrough` 라는 주석을 추가한다.

    ```java
    switch (number) {
        case 0:
            doSomething();
            // intentional fallthrough
        case 1:
            doFallthrough();
            break;
        case 2:
        case 3:
            doNotFallthrough();
            break;
        default:
            break;
    }
    ```

21. `switch` 문에서 `default:` 케이스가 절대 실행될 일이 없는 경우, `default:` 안에 `assert (false)` 란 코드를 추가하거나 예외를 던진다.

    ```java
    switch (type) {
        case 1:
            ... 
            break;
        default:
            assert (false) : "unknown type";
            break;
    }
    ```

    또는

    ```java
    switch (type) {
        case 1:
            ... 
            break;
        default:
            throw new IllegalArgumentException("unknown type");
            break;
    }
    ```

22. 재귀 메서드는 이름 뒤에 `Recursive`를 붙인다.

    ```java
    public void fibonacciRecursive();
    ```

23. 클래스 안에서 멤버 변수와 메서드의 등장 순서는 다음을 따른다.

    ```
    a. public 멤버 변수
    b. default 멤버 변수
    c. protected 멤버 변수
    d. private 멤버 변수
    e. 생성자
    f. public 메서드
    g. default 메서드
    h. protected 메서드
    i. private 메서드
    ```
    
24. 대부분의 경우 메서드 오버로딩을 피한다.

    **틀린 방식:**
    ```java
    public Anim getAnim(int index);
    public Anim getAnim(String name);
    ```

    **올바른 방식:**
    ```java
    public Anim getAnimByIndex(int index);
    public Anim getAnimByName(String name);
    ```

25. 클래스는 각각 독립된 소스 파일에 있어야 한다. 단, 작은 클래스 몇 개를 한 파일 안에 같이 넣어두는 것이 상식적일 경우 예외를 허용한다.


26. 파일 이름은 대소문자까지 포함해서 반드시 클래스 이름과 일치해야 한다.

    ```java
    // file: PlayerAnimation.java

    public class PlayerAnimation {

        private class InnerClass1 {
        }
        
        private class InnerClass2 {
        }
    }
    ```

27. 특정 조건이 반드시 충족되어야 한다고 가정(assertion)하고 짠 코드 모든 곳에 `assert`를 사용한다. `assert`는 복구 불가능한 조건이다.(예: 대부분의 메서드는 다음과 같은 `assert`를 가질 수도... `assert (parameter != null)`)

28. `assert`를 사용할 때는 표현식을 소괄호로 감싼다.

    **틀린 방식:**
    ```java
    assert x > 5 && x < 0 : "Custom message";
    ```

    **올바른 방식:**
    ```java
    assert (x > 5 && x < 0) : "Custom message"
    ```

29. 비트 플래그 열거형은 이름 뒤에 `Flags`를 붙인다.

    ```java
    public enum VisibilityFlags {
        // 플래그들 생략
    }
    ```

30. 변수 가리기(variable shadowing)는 허용하지 않는다. 이 규칙에 대한 유일한 예외는 멤버 변수와 생성자/setter 매개변수에 동일한 이름을 사용할 때이다.

    ```java
    public class SomeClass {
        private int count = 5;

        public void func() {
            for (int count = 0; count != 10; ++count) {
                System.out.println(count);
                System.out.println(this.count);
            }
        }
    }
    ```

31. `var` 키워드를 사용하지 않으려 노력한다. 단, 대입문의 우항에서 자료형이 명확하게 드러나는 경우, 또는 데이터형이 중요하지 않은 경우는 예외를 허용한다..  `IIterable`/`ICollection`에 `var`를 사용하거나 우항의 `new` 키워드를 통해 어떤 개체가 생성되는지 알 수 있는 등이 허용되는 경우의 좋은 예이다.

    ```java
    var text = "string obviously"; // Okay
    var age = 28;                  // Okay
    var employee = new Employee(); // Okay

    var accountNumber1 = getAccountNumber(); // BAD
    int accountNumber2 = getAccountNumber(); // GOOD
    ```

32. 외부로부터 들어오는 데이터의 유효성은 외부/내부 경계가 바뀌는 곳에서 검증(validate)하고 문제가 있을 경우 내부 메서드로 전달하기 전에 반환해 버린다. 이는 경계를 넘어 내부로 들어온 모든 데이터는 유효하다고 가정한다는 뜻이다.

33. 따라서 내부 메서드에서 예외(익셉션)을 던지지 않으려 노력한다. 예외는 경계에서만 처리하는 것을 원칙으로 한다.

34. 위 규칙의 예외: `enum`형을 `switch`문에서 처리할 때 실수로 처리 안 한 `enum` 값을 찾기 위해 `default:` 케이스에서 예외를 던지는 것은 허용

    ```java
    switch (accountType) {
        case AccountType.PERSONAL:
            return something;
        case AccountType.BUSINESS:
            return somethingElse;
        default:
            throw new IllegalArgumentException("unknown type");
    }
    ```

35. 메서드의 매개변수로 `null`을 허용하지 않는 것을 추구한다. 특히 `public` 메서드일 경우 더욱 그러하다.

36. `null` 매개변수를 사용할 경우 변수명 뒤에 `OrNull`를 붙인다.

    ```java
    public Anim getAnim(String nameOrNull) {
        ...
    }
    ```

37. 메서드에서 `null`을 반환하지 않는 것을 추구한다. 특히 `public` 메서드일 경우 더욱 그러하다. 그러나 때로는 예외를 던지는 것을 방지하기 위해 그래야 할 경우도 있다.

38. 메서드에서 `null`을 반환할 때는 메서드 이름 뒤에  `OrNull`을 붙인다.

    ```java
    public String getNameOrNull();
    ```

39. 메서드를 오버라이딩할 때는 언제나 `@Override` 어노테이션을 붙인다.


## II. 소스 코드 포맷팅
1. 탭(tab)은 IntelliJ의 기본 값을 사용한다. 다른 IDE를 사용할 경우에는 실제 탭 문자 대신 띄어쓰기 4칸을 사용한다.

2. 중괄호 (`{}`) 는 새로운 줄에서 열지 않으나 닫을 때는 새로운 줄에서 닫는다. 단, 다음 항목의 예외는 허용한다.

    ```java
    public void myMethod() {
        while (expression) {
            // 코드 생략
        }

        try {
            // 코드 생략
        } catch (ExceptionClass ex) {
            // 코드 생략
        }
    }
    ```

3. `if`, `if-else`, `if-else-if-else` 문은 다음의 중괄호 스타일을 사용한다.

    ```java
    if (expression) {
        // 코드 생략
    } else if (expression) {
        // 코드 생략
    } else {
        // 코드 생략
    }
    ```

4. 중괄호 안(`{}`)에 코드가 한 줄만 있더라도 반드시 중괄호를 사용한다.

    ```java
    if (!alive) {
        return;
    }
    ```

5. 한 줄에 변수 하나만 선언한다.

    **틀린 방식:**
    ```java
    int counter = 0, index = 0;
    ```

    **올바른 방식:**
    ```java
    int counter = 0;
    int index = 0;
    ```
