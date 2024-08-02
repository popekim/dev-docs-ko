---
title: "POCU 아카데미용 C++ 코딩 표준"
date: 2024-08-01
---

본 코딩 표준은 [Pope Kim의 C++ 코딩 표준](/ko/coding-standards/cpp)을 [POCU 아카데미](https://pocu.academy/ko/)에 적합하게 수정한 것입니다. 취소선(~~취소선~~) 표시가 된 항목들은 무시해주세요. 


* 원문(영어): [C++ Coding Standards](https://docs.popekim.com/en/coding-standards/cpp)

## 머리말
### 기본 원칙

1. 가독성을 최우선으로 삼는다. (대부분의 경우 코드는 그 자체가 문서의 역할을 해야 함) 
2. 문제가 있을 경우 최대한 빨리 크래시가 나거나 assert에 걸리도록 코드를 작성한다.  어떻게든 프로그램을 돌게 만들다가 나중에 크래시가 나는 것보다 나쁜 상황은 없다.
3. 정말 합당한 이유가 있지 않는 한, 통합개발환경(IDE)의 자동 서식을 따른다. (비주얼 스튜디오의 "Ctrl + K + D" 기능)
4. 본 코딩표준을 따라 잘 짜여진 기존의 코드에서 배운다.

## I. 메인 코딩 표준
1. 클래스와 구조체의 이름은 파스칼 표기법을 따른다.
    ```cpp
    class PlayerManager;
    struct AnimationInfo;
    ```

2. 지역 변수 그리고 함수의 매개 변수의 이름은 카멜 표기법을 따른다.
    ```cpp
    void SomeMethod(const int someParameter);
    {
        int someNumber;
        int id;
    }
    ```

3. 정적(`static`) 변수의 이름 앞에는 `s`를 붙인다.

   ```c
   static int sNumMonsters;     // 지역 변수와 public 멤버 변수의 경우
   static bool sbInitialized;   // 클래스의 private static 변수의 경우
   ```

4. 메서드 이름은 동사-목적어 쌍으로 표기한다.

    a. public 메서드의 이름은 파스칼 표기법을 따른다.

    ```cpp
    public:
        void DoSomething();
    ```

    b. 그 외 다른 메서드의 이름은 카멜 표기법을 따른다.

    ```cpp
    private:
        void doSomething();
    ```

5. 상수 또는 `#define` 으로 정의된 상수의 이름은 모두 대문자로 하되 밑줄로 각 단어를 분리한다.

    ```cpp
    constexpr int SOME_CONSTANT = 1;
    ```

6. 네임스페이스는 모두 소문자로 작성한다.
    ```cpp
    namespace abc{};
    ```

7. 부울(boolean)형 변수는 앞에 `b`를 붙인다.
    ```cpp
    bool bFired;	// 지역 변수와 public 멤버 변수의 경우
    bool mbFired;	// 클래스의 private 멤버 변수의 경우
    ```

8. 인터페이스를 선언할 때는 앞에 `I`를 붙인다.
    ```cpp
    class ISomeInterface;
    ```

9. 열거형을 선언할 때는 앞에 `e`를 붙인다.
    ```cpp
    enum class eDirection
    {
        North,
        South
    }
    ```

10. 클래스 멤버 변수명은 앞에 `m`을 붙인다.
    ```cpp
    class Employee
    {
    protected:
        int mDepartmentID;
    private:
        int mAge;
    }
    ```

11. `goto` 레이블 명은 모두 대문자로 하되 밑줄로 각 단어를 분리한다.
    
    ```cpp
    goto MY_LABEL;


    // ....

    MY_LABEL:
        std::cout << "Magic!" << std::endl;
        return 0;

    ```

12. 값을 반환하는 함수의 이름은 무엇을 반환하는지 알 수 있게 짓는다.
    ```cpp
    uint32_t GetAge() const;
    ```

13. 단순히 반복문에 사용되는 변수가 아닌 경우엔 `i`, `e` 같은 변수명 대신 `index`, `employee `처럼 변수에 저장되는 데이터를 한 눈에 알아볼 수 있는 변수명을 사용한다.

14. 뒤에 추가적인 단어가 오지 않는 경우 줄임말은 모두 대문자로 표기한다.

    ```cpp
    int OrderID;
    int HttpCode;
    ```

15. 클래스 멤버 변수에 접근할 때는 항상 setter와 getter를 사용한다.

    **틀린 방식:**
    ```cpp
    class Employee
    {
    public:
        string Name;
    }
    ```

    **올바른 방식:**
    ```cpp
    class Employee
    {
    public:
        const string& GetName() const;
        void SetName(const string& name);
    private:
        string mName;
    }
    ```

16. 구조체는 오직 `public` 멤버 변수만 가질 수 있다. 구조체의 멤버 변수명은 파스칼 표기법을 따르며, 구조체 안에서 함수는 사용하지 않는다.
    ```cpp
    struct MeshData
    {
        int32_t VertexCount;
    }
    ```

17. 외부 헤더 파일을 인클루드 할 때는 `#include<>` 을 사용. 자체적으로 만든 헤더 파일을 인클루드 할 때는 `#include ""` 를 사용한다.

18. 외부 헤더 파일을 먼저 인클루드한 뒤, 내부 헤더 파일을 인클루드 한다. 인클루드를 할  때, 가능하다면 알파벳 순서를 따른다.

    ```cpp
    #include <vector>
    #include <unordered_map>

    #include "AnimationInfo.h"
    ```

19. 모든 헤더 파일 첫 번째 줄에 `#pragma once`를 기재한다.

20. 지역 변수를 선언할 때는 그 지역 변수를 사용하는 코드와 동일한 줄에 선언하는 것을 원칙으로 한다.

21. `double`이 반드시 필요한 경우가 아닌 이상 부동 소수점 값에 `f`를 붙여준다

    ```cpp
    float f = 0.5f;
    ```

22. switch case 문에는 항상 `default:`를 넣는다.
    ```cpp
    switch (number)
    {
    case 0:
        ... 
        break;
    default:
        break;
    }
    ```

23. switch case 문 끝에 `break;`를 넣지 않고 그 바로 아래 `case` 문의 코드를 실행하고 싶은 경우, 미리 정의해둔 FALLTHROUGH 매크로를 추가한다. 단, case 문 안에 코드가 없는 경우는 예외이다. 이는 C++17 사양에서 `[[fallthrough]]` 애트리뷰트로 대체될 것이다.
    ```cpp
    switch (number)
    {
    case 0:
        DoSomething();
        FALLTHROUGH
    case 1:
        DoFallthrough();
        break;
    case 2:
    case 3:
        DoNotFallthrough();
        break;
    default:
        break;
    }
    ```

24. `default` case가 절대 실행될 일이 없는 경우, `default` case 안에 `assert(false);` 란 코드를 추가한다. ~~`Assert()`를 직접 구현하면 그 안에서 릴리즈 빌드 시 최적화 힌트를 추가할 수 있다.~~

    ```cpp
    switch (type)
    {
    case 1:
        ... 
        break;
    default:
        assert(false); // unknown type"
        break;
    }
    ```

25. 원칙적으로 모든 곳에 `const`를 사용한다. 여기에는 지역 변수와 함수 매개 변수도 포함된다.

26. 개체를 수정하지 않는 멤버 함수에는 모두 `const`를 붙인다.
    ```cpp
    int GetAge() const;
    ```

27. 값(value) 형식의 변수를 const로 반환하지 않는다. 포인터나 참조(reference)를 반환할 경우에만 `const` 반환을 한다.

28. 재귀 함수는 이름 뒤에 `Recursive`를 붙인다.

    ```cpp
    void FibonacciRecursive();
    ```

29. 클래스 안에서 멤버 변수와 메서드의 등장 순서는 다음을 따른다.
    a. friend 클래스들
    b. public 메서드들
    c. protected 메서드들
    d. private 메서드들
    e. protected 변수들
    f. private 변수들

30. 대부분의 경우 함수 오버로딩을 피한다.

    **틀린 방식:**
    ```cpp
    const Anim* GetAnim(const int index) const;
    const Anim* GetAnim(const char* name) const;
    ```

    **올바른 방식:**
    ```cpp
    const Anim* GetAnimByIndex(const int index) const;
    const Anim* GetAnimByName(const char* name) const;
    ````

31. `const` 반환을 위한 함수 오버로딩은 허용한다.

    ```cpp 
    Anim* GetAnimByIndex(const int index);
    const Anim* GetAnimByIndex(const int index) const;
    ```

32. `const_cast`를 직접적으로 사용하지 않는다. 대신 `const`인 개체를 수정 가능한 형태로 변환해서 반환하는 함수를 만든다.

33. 클래스는 각각 독립된 소스 파일에 있어야 한다. 단, 작은 클래스 몇 개를 한 파일 안에 같이 넣어두는 것이 상식적일 경우 예외를 허용한다.

34. 파일 이름은 대소문자까지 포함해서 반드시 클래스 이름과 일치해야 한다.
    ```cpp
    class PlayerAnimation;

    PlayerAnimation.cpp
    PlayerAnimation.h
    ```

35. 여러 파일이 하나의 클래스를 이룰 때, 파일 이름은 클래스 이름으로 시작하고, 그 뒤에 밑줄과 세부 항목 이름을 붙인다.

    ```cpp
    class RenderWorld;

    RenderWorld_load.cpp
    RenderWorld_demo.cpp
    RenderWorld_portals.cpp
    ```

36. [Reverse OOP 패턴](https://www.youtube.com/watch?v=GnWASmocihE)을 사용할 때, 플랫폼 전용 클래스는 위 항목과 비슷한 명명 규칙을 사용한다.

    ```cpp
    class Renderer;

    Renderer.h			// 게임에서 호출되는 모든 Renderer 인터페이스
    Renderer.cpp		// 모든 플랫폼 용 Renderer 구현 소스
    Renderer_gl.h		// Renderer가 호출하는 RendererGL 인터페이스
    Renderer_gl.cpp		// RendererGL 구현 소스
    ```

37. ~~표준 C assert 대신에 자신만의 Assert 버전을 구현한다.~~

38. 특정 조건이 반드시 충족되어야 한다고 가정(assertion)하고 짠 코드 모든 곳에 assert를 사용한다. assert는 복구 불가능한 조건이다. ~~Assert는 릴리즈 빌드에서 [__assume](https://docs.microsoft.com/en-us/cpp/intrinsics/assume) 키워드로 대체하여 컴파일러에 최적화 힌트를 줄 수 있다.~~

39. ~~모든 메모리 할당은 직접 구현한 New, Delete 키워드를 통해 호출한다.~~

40. ~~memset, memcpy, memmove와 같은 메모리 연산 역시 우리 고유의 MemSet, MemCpy, MemMove 키워드를 통해 호출해야 한다.~~

41. 어떤 이유로든 매개변수로 `nullptr`가 넘어올 수 있는 경우가 아니라면, 포인터 대신 참조자( `&` )를 사용하는 것을 원칙으로 한다. (예외는 다음 항목을 참고)

42. 함수에서 매개변수를 통해 값을 반환할 때(`out` 매개변수)는 포인터를 사용하며, 매개변수 이름 앞에 `out`을 붙인다.

    **함수:**
    ```cpp
    void GetScreenDimension(uint32_t* const outWidth, uint32_t* const  outHeight)
    {
    }
    ```

    **호출:**
    ```cpp
    uint32_t width;
    uint32_t height;
    GetScreenDimension(&width, &height);
    ```

43. 위 항목의 `out` 매개 변수는 반드시 null이 아니어야 한다. (함수 내부에서 `if` 문 대신 `assert`를 사용할 것)
    ```cpp
    void GetScreenDimension(uint32_t* const outWidth, uint32_t* const  outHeight)
    {
        assert(outWidth);
        assert(outHeight);
    }
    ```

44. 매개 변수가 클래스 내부에서 저장될 때는 포인터를 사용한다.
    ```cpp
    void AddMesh(Mesh* const mesh)
    {
        mMeshCollection.push_back(mesh);
    }
    ```

45. 매개 변수가 `void` 포인터여야 하는 경우는 포인터를 사용한다.
    ```cpp
    void Update(void* const something)
    {
    }
    ```

46. 비트 플래그 열거형은 이름 뒤에 `Flags`를 붙인다.
    ```cpp
    enum class eVisibilityFlags
    {
    }
    ```

47. 특정 크기(예를 들어, 데이터 멤버의 직렬화를 위한 크기)가 필요하지 않은 한 열거형에 크기 지정자를 추가하지 않는다.
    ```cpp
    enum class eDirection : uint8_t
    {
        North,
        South
    }
    ```

48. 디폴트 매개 변수 대신 함수 오버로딩을 선호한다.

49. 디폴트 매개 변수를 사용하는 경우, null이나 `false`, `0` 같이 비트 패턴이 0인 값을 사용한다.

50. 가능한 한 고정된 크기(size)의 컨테이너를 사용한다.

51. 동적 컨테이너를 사용해야 한다면 가능한 한 미리 `reserve()`를 호출한다.

52. `#define` 으로 정의된 상수는 항상 괄호로 감싸준다.
    ```cpp
    #define NUM_CLASSES (1)
    ```

53. 상수는 `#define` 보다 `const` 상수 변수로 선언한다.

54. 클래스를 상호 참조할 때는 `#include` 보다 전방선언(forward declaration)을 최대한 이용한다.

55. 모든 컴파일러 경고는 반드시 고친다.

56. 변수 가리기(variable shadowing)는 허용되지 않는다. 외부 변수가 동일한 이름을 사용중이라면 내부 변수에는 다른 이름을 사용한다.

    ```cpp
    class SomeClass
    {
    public:
        int32_t Count;
    public:
        void Func(const int32_t Count)
        {
            for (int32_t count = 0; count != 10; ++count)
            {
                // Use Count
            }
        }
    }
    ```

57. 한 줄에 변수 하나만 선언한다.

    **틀린 방식:**

    ```cpp
    int counter = 0, index = 0;
    ```

    **올바른 방식:**
    ```cpp
    int counter = 0;
    int index = 0;
    ```

58. 지역 객체를 반환할 때 [NRVO](https://docs.microsoft.com/en-us/previous-versions/ms364057(v=vs.80))의 이점을 활용한다. 이는 함수 내에 하나의 return문 만 쓴다는 것을 의미하며, 이것은 값으로 객체를 반환할 때만 적용된다.

59. `struct`나 `class`에서 초기화 후 값 변경을 막으려고 `const` 멤버 변수를 쓰지 않는다. 참조(`&`) 멤버변수의 경우도 마찬가지

60. 멤버 변수를 초기화할 때는 초기화 리스트를 사용하는 것을 기본으로 한다. 

61. <<<미정: __restrict keyword

## II. 소스 코드 포맷팅

1. `include` 전처리문 블록과 코드 본문 사이에 반드시 빈 줄이 있어야 한다.

2. ~~탭(tab)은 비주얼 스튜디오 기본값을 사용하며, 비주얼 스튜디오를 사용하지 않을 시 띄어쓰기 4칸을 탭으로 사용한다.~~ \
탭(tab)은 비주얼 스튜디오 기본값인 실제 탭 문자를 사용하며 스페이스 문자로 바꾸지 않는다.

3. 중괄호( { )를 열 때는 언제나 새로운 줄에 연다.

4. 중괄호 안( { } )에 코드가 한 줄만 있더라도 반드시 중괄호를 사용한다.

    ```cpp
    if (bSomething)
    {
        return;
    }
    ```
5. 포인터나 참조 기호는 자료형에 붙인다.
    ```cpp
    int& number;
    int* number;
    ```

6. 초기화 리스트를 이용해 멤버 변수를 초기화할 때는 아래와 같은 포맷을 따라 한 줄에 변수 하나씩 초기화한다.

    **틀린 방식:**
    ```cpp
    MyClass::MyClass(const int var1, const int var2)
      :mVar1(var1), mVar2(var2), mVar3(0)
    {
    ```
    
    **올바른 방식:**
    ```cpp
    MyClass::MyClass(const int var1, const int var2)
      : mVar1(var1)
      , mVar2(var2)
      , mVar3(0)
    {
    ```

## III. 최신 C++ 기능 관련

1. `override` 와 `final` 키워드를 반드시 사용한다.

2. 항상 `enum class` 를 사용한다.
    ```cpp
    enum class eDirection
    {
        North,
        South
    }
    ```

3. 가능한 `assert` 대신 `static_assert` 를 사용한다.

4. 포인터에 `NULL` 대신 `nullptr` 를 사용합니다.

5. 개체의 수명이 클래스 내에서만 처리되는 경우 `unique_ptr` 를 사용한다. (즉, 개체 생성은 생성자에서, 개체 파괴는 소멸자에서)

6. 적용 가능한 곳이라면 범위기반 `for` 문을 사용한다.

7. `반복자`나 `new` 키워드가 같은 줄에 있어서, 어떤 개체가 만들어지는 지 명확하게 드러나는 경우가 아니라면 `auto` 키워드를 사용하지 않는다.

8. `std::move`를 사용하여 수동으로 반환 값을 최적화하지 않는다. 이럴 경우, [자동 NRVO 최적화](https://msdn.microsoft.com/en-us/library/ms364057(v=vs.80).aspx )가 적용되지 않는다.

9. 이동 생성자(move constructor)와 이동 대입 연산자(move assignment operator)를 사용해도 된다.

10. 단순 상수 변수에는 `const` 대신 `constexpr` 을 사용한다.

    **적용 전:**
    ```cpp
    const int DEFAULT_BUFFER_SIZE = 65536;
    ```

    **적용 후:**
    ```cpp
    constexpr int DEFAULT_BUFFER_SIZE = 65536;
    ```

11. <<<미정: constexpr

12. <<<미정: Lambda

13. <<<미정: do not use shared_ptr

## ~~IV. 프로젝트 설정 및 프로젝트 구조~~

1. ~~Visual C++: 프로젝트 설정을 변경하려면 항상 속성 시트(property sheets)에서 변경 한다.~~

2. ~~프로젝트 설정에서 컴파일 경고를 비활성화 하지 않는다. 그 대신, 코드에서 `#pragma` 를 사용한다.~~
