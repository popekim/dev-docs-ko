---
title: "POCU 아카데미용 C 코딩 표준"
date: 2024-09-15
---

## 머리말
### 기본 원칙

1. 가독성을 최우선으로 삼는다. (대부분의 경우 코드 그 자체가 문서의 역할을 해야 함) 
2. 문제가 있을 경우 최대한 빨리 크래시가 나거나 assert에 걸리도록 코드를 작성한다. 어떻게든 프로그램을 돌게 만들다가 나중에 크래시가 나는 것보다 나쁜 상황은 없다.
3. 본 코딩표준을 따라 잘 짜여진 기존의 코드에서 배운다.

### 참조문서

이 코딩 표준은 아래의 코딩 표준들에서 영감을 얻었음.

* [Indian Hill](https://www2.cs.arizona.edu/~mccann/cstyle.html)
* [Linux Kernel](https://www.kernel.org/doc/html/latest/process/coding-style.html)
* [GNU](https://www.gnu.org/prep/standards/html_node/Writing-C.html#Writing-C)


## I. 메인 코딩 표준

1. 변수, 구조체, `typedef`, 함수 매개변수, 함수의 이름은 스네이크 표기법을 따른다.

   ```c
   int num_monsters;
   ```

1. 단순히 반복문에 사용되는 변수가 아닌 경우엔 `i`, `e` 같은 변수명 대신 `index`, `employee` 처럼 변수에 저장되는 데이터를 한 눈에 알아볼 수 있는 변수명을 사용한다.

1. 전역 변수의 이름 앞에는 `g_`를 붙인다.

   ```c
   int g_num_monsters = 0;
   ```

1. 정적(`static`) 변수의 이름 앞에는 `s_`를 붙인다.

   ```c
   static int s_num_monsters = 0;
   ```

1. 함수의 이름은 동사로 시작한다.

   ```c
   int calculate_income(void)
   {
       return 0;
   }
   ```

1. 단, 단순히 부울(bool) 상태를 반환하는 메서드의 동사 부분은 최대한 `is`, `can`, `has`, `should`를 사용하되 그러는 것이 부자연스러울 경우에는 상태를 나타내는 다른 3인칭 단수형 동사를 사용한다.

    ```c
    int is_alive(const person_t* person);
    int has_child(const person_t* person);
    int can_accept(const person_t* person);
    int should_delete(const person_t* person);
    int exists(const person_t* person);
    ```

1. 열거형(`enum`)의 이름은 스네이크 표기법을 따른다. 

   ```c
   enum game_role {
       GAME_ROLE_MID,
       GAME_ROLE_JUNGLE
   };
   ```

1. 열거형 상수의 이름은 모두 대문자로 하되 밑줄로 각 단어를 분리한다.

   ```c
   enum game_role {
       GAME_ROLE_MID,
       GAME_ROLE_JUNGLE
   };
   ```

8. 열거형 상수는 언제나 열거형 이름으로 시작하여야 한다.

   ```c
   enum month {
       MONTH_JAN,
       MONTH_FEB
   };
   ```

9. `typedef`의 끝에 `_t`를 붙인다.

   ```c
   typedef enum color { 
       COLOR_RED,
       COLOR_GREEN, 
       COLOR_BLUE 
   } color_t;
   ```

10. 가능한 열거형과 구조체에 `typedef`를 사용한다.

    ```c
    typedef enum color { 
        COLOR_RED,
        COLOR_GREEN, 
        COLOR_BLUE 
    } color_t;
    
    typedef struct point {
        int x;
        int y;
    } point_t;
    ```

11. 상수 또는 `#define` 으로 정의된 상수의 이름은 모두 대문자로 하되 밑줄로 각 단어를 분리한다.

    ```c
    #define NUMBER_ZERO (0)
    const int INTEGER_ZERO = 0;
    ```

12. `#define`으로 정의한 숫자나 표현식은 소괄호로 감싼다.

    ```c
    #define PI (3.14)
    #define CIRCLE_AREA(r) (PI * r * r)
    ```

13. 언제나 함수 원형으로 반환형을 선언한다. 컴파일러가 지멋대로 추측하는 일은 없도록 한다.

    틀린 방식:

    ```c
    calculate_income(const unsigned int month);
    ```

    올바른 방식:

    ```c
    unsigned int calculate_income(const unsigned int month);
    ```

14. 값을 반환하는 함수의 이름은 무엇을 반환하는지 알 수 있게 짓는다.

    ```c
    unsigned int get_age(void)
    {
        return g_age;
    }
    ```

15. 매개변수를 받지 않는 함수는 매개변수 목록에 반드시 `void`를 넣어준다.

    ```c
    int get_number(void)
    {
        return 0;
    }
    ```

16. 널 포인터를 반환하는 함수의 이름은 `_or_null`로 끝나야 한다.

    ```c
    const char* get_owner_name_or_null(const car_type_t car_type)
    {
        ...
    
        return NULL;
    }
    ```

17. 널 포인터를 허용하는 매개변수의 이름은 `_or_null`로 끝나야 한다.

    ```c
    const char* get_owner_name_or_null(const car_type_t* car_type_or_null)
    {
        ...
    
        return NULL;
    }
    ```

18. `switch` 문 안에 언제나 `default` 케이스를 넣는다.

    ```c
    switch (op) {
    case 0:
        /* intentional fallthrough */
    case 1:
        printf("valid entry");
        break;
    default:
        printf("invalid entry");
        break;
    }
    ```

19. 일부러 `case` 문을 다음 `case` 문으로 넘어가게 할 때에는 `/* intentional fallthrough */` 이란 주석을 넣는다.

    ```c
    switch (op) {
    case 0:
        /* intentional fallthrough */
    case 1:
        printf("valid entry");
        break;
    default:
        printf("invalid entry");
        break;
    }
    ```

20. 모든 `case` 문은 `break` 또는 `return` 키워드를 사용한다. 단, 일부러 `case` 문을 다음 `case` 문으로 넘어가게 하고 싶을 때는 예외로 한다.

    ```c
    switch (op) {
    case 0:
        /* intentional fallthrough */
    case 1:
        printf("valid entry");
        break;
    default:
        printf("invalid entry");
        break;
    }
    ```

21. `switch` 문에서 `default` 케이스가 일어나지 말아야 한다면 `assert(0)`을 사용한다.

    ```c
    switch (type) {
    case 1:
        ... 
        break;
    default:
        assert(0);
        break;
    }
    ```

22. 변수의 이름을 숨기지 않는다. 그 대신 다른 변수명을 사용한다.

    ```c
    /* DO NOT DO THIS */
    int a = 0;
    
    {
        int a = 1;
        printf(a); /* 1 */
    }
    
    printf(a): /* 0 */
    ```

23. 외부 헤더파일을 인클루드 할 때는 `#include<>`를 사용한다. 자체 개발한 헤더파일은 `#include ""` 로 인클루드한다.

24. 외부 헤더파일을 먼저, 그리고 자체 개발한 헤더파일을 인클루드 한다. 이 때 가능하면 알파벳 순서를 따른다.

    ```c
    #include <assert.h>
    #include <stdio.h>
    
    #include "event_loop.h"
    ```

25. 대문자로 표현한 파일명을 사용하여 인클루드 가드(include guard)를 사용한다.


    Filename: grandparent.h

    ```c
    #ifndef GRANDPARENT_H
    #define GRANDPARENT_H
    
    const char* g_grandfather_name = "Joseph";
    
    #endif /* GRANDPARENT_H */
    ```

26. 인클루드 가드를 닫는 곳에 대문자로 표현한 파일명을 주석으로 삽입한다.

    Filename: grandparent.h

    ```c
    #ifndef GRANDPARENT_H
    #define GRANDPARENT_H
    
    const char* g_grandfather_name = "Joseph";
    
    #endif /* GRANDPARENT_H */
    ```

27. 최대한 `const` 키워드를 많이 사용한다. 이는 지역 변수나 함수 매개변수에도 마찬가지이다.

28. `const` 값형을 반환하지 않는다. `const` 반환은 포인터에만 쓴다.

29. 불리언 비교에 숫자를 그대로 사용하지 않는다. 그 대신 0과 비교를 한다.

    틀린 방식:

    ```c
    int counter = 10;
    while (counter--) {
        printf("%d\n", counter);
    }
    ```

    올바른 방식: 

    ```c
    int counter = 10;
    while (counter-- != 0) {
        printf("%d\n", counter);
    }
    ```

30. 널 포인터를 표현할 때는 숫자 0 대신 `NULL`을 `#define`해 놓은 것을 사용한다.

    틀린 방식:

    ```c
    #define PRICE (2)
    
    void increase_price(int* current_price)
    {
        assert(current_price != 0);
    
        *current_price += PRICE ;
    }
    ```

    올바른 방식:  

    ```c
    #define PRICE (2)
    
    void increase_price(int* current_price)
    {
        assert(current_price != NULL);
    
        *current_price += PRICE ;
    }
    ```

31. 재귀 함수의 이름은 `recursive`로 끝난다.

    ```c
    void fibonacci_recursive()
    {
        ...
    }
    ```

32. 포인터가 out 매개변수로 사용될 경우 매개변수 이름 앞에 `out_`을 붙인다.

    함수:

    ```c
    void get_screen_dimension(int* const out_width, int* const  out_height)
    {
    }
    ```

    호출자:

    ```c
    int width;
    int height;
    get_screen_dimension(&width, &height);
    ```

33. 위 out 매개변수는 널 포인터가 아니어야 한다. (이 때, `if`문이 아니라 `assert`를 쓸 것)

    ```c
    void get_screen_dimension(int* const out_width, int* const  out_height)
    {
        assert(out_width != NULL);
        assert(out_height != NULL);
    }
    ```

34. 모든 컴파일러 경고(warning)은 고친다.

35. 레이블 명은 스네이크 표기법을 따른다.

    ```c
    goto fatal_error;
    
    …
    
    fatal_error:
        printf(“A fatal error has occurred”);
        return 0;
    ```

36. `goto` 문은 언제나 아래 쪽에 위치한 레이블로만 점프할 수 있다..

    틀린 방식:

    ```c
    begin:
        …
    
    if (finshed_task == 0)
    {
        goto begin;
    }
    ```

    올바른 방식:

    ```c
    if (x != 3)
    {
        goto error;
    }
    
    …
    
    error:
        printf(“An error has occurred”);
    ```

37. 전역 변수의 선언은 헤더파일 안에서 `extern` 키워드를 사용하여 한다.

    Filename: globals.h

    ```c
    #ifndef GLOBALS_H
    #define GLOBALS_H
    
    /* declare */
    extern int g_counter;
    
    #endif /* GLOBALS_H */
    ```

    Filename: globals.c

    ```c
    /* define */
    int g_counter = 0;
    
    Filename: main.c
    
    #include <stdio.h>
    #include "globals.h"
    
    int main(void)
    {
        printf("current counter: %d", g_counter);
    
        return 0;
    }
    ```

38. 변수는 가능한 최소한의 범위로 제한한다.

39. 한 함수에서만 사용되는 변수는 반드시 그 함수 안에서 지역변수나 정적변수로 선언되어야 한다.

40. 한 파일에 존재하는 여러 함수에서만 사용하는 변수가 있다면 그 변수는 그 파일 안에서 정적 변수로 선언되어야 한다.

41. 여러 소스 파일에서 사용하는 변수들만 전역 변수로 선언한다.

42. 초기화 목록에서 요소를 생략하여 배열을 0으로 초기화 할 때는 0 이후에 쉼표(,)를 넣어 의도가 명확히 보이도록 한다.

    ```c
    char buffer[30] = { 0, }
    ```

43. 동적으로 할당한 메모리를 저장하는 포인터 변수의 이름은 `pa_`으로 시작한다. 

    틀린 방식:

    ```c
    int* nums;
    
    nums = malloc(LENGTH * sizeof(int));
    
    free(nums);
    ```

    올바른 방식:

    ```c
    int* pa_nums;
    
    pa_nums = malloc(LENGTH * sizeof(int));
    
    free(pa_nums);
    ```

44. 동적으로 할당한 메모리 주소를 저장하고 있는 포인터 변수를 연산에 사용할 때는 그 포인터의 사본을 만들어 사용한다. 이는 잘못된 메모리 해제를 막기 위함이다.

    틀린 방식:

    ```c
    int* nums;
    
    nums = malloc(LENGTH * sizeof(int));
    
    for (i = 0; i < LENGTH; ++i) {
        *nums++ = 15 * (i + 1);
    }
    
    free(nums); /* 잘못된 메모리를 해제함 */
    ```

    올바른 방식:

    ```c
    void* pa_nums;
    int* p;
    
    pa_nums = malloc(LENGTH * sizeof(int));
    
    p = pa_nums;
    
    for (i = 0; i < LENGTH; ++i) {
        *p++ = 15 * (i + 1);
    }
    
    free(pa_nums); /* 올바른 메모리를 해제함 */
    ```

45. 동적으로 할당한 메모리를 해제한 후에는 반드시 포인터 변수에 `NULL`을 대입한다.

    ```c
    int* nums;
    
    nums = malloc(LENGTH * sizeof(int));
    
    ...
    
    free(nums);
    nums = NULL;
    ```

46. 내부에서 동적으로 메모리를 할당하는 함수의 이름은 반드시 `_malloc`으로 난다. 

    ```c
    const char* combine_string_malloc(const char* str1, const char* str2)
    {
        void* pa_str;
        char* p;
    
        pa_str = malloc(size);
        p = pa_str;
    
        /* combine string here */
    
        return pa_str;
    }
    ```

## II. 소스 코드 포맷팅

1. 들여쓰기는 4칸의 스페이스로 한다.

1. 이항 연산자에는 앞뒤로 스페이스 1칸을 넣는다.

    BAD:
    ```c
    int a = nums[n+1];
    ```

    GOOD:
    ```c
    int a = nums[n + 1];
    ```

1. 함수의 범위를 나타내는 중괄호는 언제나 새로운 줄에서 열고 닫으며, 들여쓰기는 함수 헤더의 위치에 맞춘다.

   ```c
   int make_decision(int beans)
   {
       return 0;
   }
   ```

1. 조건문 및 반복문의 범위 시작을 나타내는 중괄호는 새로운 줄에서 열지 않는다.

   ```c
   if (beans == 2) {
       beans++;
   } else if (beans % 3 == 0) {
       beans += 5;
   } else {
       beans += 2;
   }
   
   do {
       beans++;
   } while (beans < 100);
   ```

1. 임의로 새로운 범위를 만들기 위해 중괄호를 사용할 때는 새로운 줄에 열고 닫는다.

   ```c
   int main(void)
   {
       {
           /* 새로운 범위 */
       }
   
       return 0;
   }
   ```

1. 구조체, 열거형, 공용체의 중괄호는 새로운 줄에서 열지 않는다.

   ```c
   typedef enum color { 
       COLOR_RED,
       COLOR_GREEN, 
       COLOR_BLUE 
   } color_t;
   
   typedef struct point {
       int x;
       int y;
   } point_t;
   
   typedef union number {
       int integer_value;
       float float_value;
   } number_t;
   ```

1. 조건문 및 반복문 블록에 들어가는 코드가 한 줄일 때에도 언제나 중괄호를 사용한다.

   ```c
   if (x > y) {
       x = y;
   } else {
       y = x * y - 2;
   }
   ```

1. 함수 반환형은 함수 명과 동일한 줄에서 선언한다.

   틀린 방식:

   ```c
   unsigned int
       calculate_income(const unsigned int month);
   ```

   올바른 방식:

   ```c
   unsigned int calculate_income(const unsigned int month);
   ```

1. 함수 매개변수는 쉼표(,) 뒤에 스페이스를 하나 넣어서 분리한다.

   ```c
   int sum(const int a, const int b, const int c)
   {
       return a + b + c;
   }
   ```

1. 변수와 연산자 사이에는 스페이스를 한 칸 넣는다.

   ```c
   if (x + b == x * (c + 22)) {
   }
   ```

1. `if`, `switch`, `case`, `for`, `do`, `while` 키워드 뒤에 따라오는 소괄호 앞에는 스페이스를 한 칸 넣는다.

    ```c
    if (xyz == zyx) {
    }
    ```

1. 함수 이름과 소괄호 사이에는 스페이스를 넣지 않는다.

    ```c
    int get_zero(void) 
    {
        return 0;
    }
    ```

1. `case` 문의 들여쓰기는 `switch` 문의 닫는 중괄호와 같게 맞춘다.

    ```c
    switch (op) {
    case 0:
        /* intentional fallthrough */
    case 1:
        printf("valid entry");
        break;
    default:
        printf("invalid entry");
        break;
    }
    ```

1. 인클루드 문과 코드 사이에는 반드시 빈 줄을 하나 넣는다.

    ```c
    #include <stdio.h>
    #include "globals.h"
    
    int main(void)
    {
        /* code here */
    }
    ```

1. 포인터 표기는 데이터 형의 오른쪽에 붙인다.

    ```c
    int* number;
    ```

1. 한 줄에 변수 하나만 선언한다.

    틀린 방식:

    ```c
    int counter, index;
    ```

    올바른 방식:

    ```c
    int counter;
    int index;
    ```

1. `goto` 레이블은 블록 스코프에 맞춰 정렬한다.

    ```c
    void do_something()
    {
        int i;
        int j;
    
        goto my_label;
    
    my_label:
        for (i = 0; i < 10; ++i) {
            for (j = 0; j < 10; ++j) {
                if (j == 2) {
                    goto my_another_label;
                }
            }
    
        my_another_label:
            /* do something */
        }
    }
    ```

1. 전처리기 지시자는 (`#include`, `#define`, `#if`, `#elif`, `#endif`, `#ifdef`, etc) 인덴트 하지 않는다.

    BAD:

    ```c
    int magic()
    {
        #if 1
    printf("indent!!");
        #endif
    }
    ```

    GOOD:

    ```c
    int magic()
    {
    #if 1
        printf("indent!!");
    #endif
    }
    ```
