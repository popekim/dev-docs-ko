---
title: "3. set_new_handle"
date: 2026-11-19
---

연산자 new를 이용하여 Heap 메모리를 할당하는 루틴은 거의 모든 프로그램이 가지고 있습니다. 하지만 제가 만들었던 프로그램들도 그랬던 것처럼 다른 프로그램들도 Heap '메모리 할당의 실패에 대한 처리'는 하지 않는 경우가 많습니다. 여기서 '메모리 할당의 실패에 대한 처리'라는 말은 단순히 new로 생성한 포인터가 0(NULL)인지 아닌지를 체크하는 루틴을 말하는 것이 절대로 아닙니다. 많은 상용&비상용 프로그램들은 반환받는 포인터가 0인지 아닌지를 체크하는 루틴을 가지고 있습니다. 하지만 그것은 '메모리 할당의 실패에 대한 처리'가 아니라 '메모리 할당의 실패'일 뿐입니다.

이제 '메모리 할당의 실패에 대한 처리'를 가능하게 해주는 set\_new\_handler()에 대한 내용을 이야기해보고자 합니다. 이 내용들은 제가 읽은 책 중 아래 두 권, TC++PL(The C++ Programming Language Special Edition : ISBN 8945072047)과 C++ 기초 플러스 제4판(ISBN 8931547900)에서 배우고 익힌 것입니다.

우선 간단한 Heap 메모리 할당이 실패하게끔 프로그램을 만들어 보죠.

```cpp
#include <windows.h>
#include <limits>
#include <iostream>

int main()
{
   BYTE* p = 0;
   size_t size = 10000;

   while ( true )
   {
       p = new BYTE[size];
       if ( 0 == p )
           break;
       else
       {
           size *= 2;
           delete [] p;
       }
   }

   std::cout << "메모리 할당에 실패하였습니다." << '\n';
   std::cout << "실패한 사이즈: " << size << '\n';

   return 0;
}
```

제 컴퓨터의 경우에는 2621440000 정도의 메모리 할당을 요청할 때 0이 반환되는 것을 확인하였습니다. 위와 같은 에러 처리 방법은 단순하다는 장점이 있으나 범용적인 '메모리 할당 실패에 대한 처리'의 방법이 되기에는 좋지 않습니다. 왜냐하면 new를 사용하는 모든 곳에 if로 반환된 포인터를 검사해야 하며, 각각의 경우 다른 방법의 실패에 대한 처리를 해주어야 하기 때문입니다. 더 좋은 방법은 아래에서 보여주고 있는 set\_new\_handler()라는 함수를 이용하는 방법입니다.

```cpp
#include <windows.h>
#include <limits>
#include <iostream>
#include <new>

void cannot_alloc_heap()
{
   std::cerr << "메모리 할당에 실패하였습니다." << '\n';
   throw std::bad_alloc();
}

int main()
{
   BYTE* p = 0;
   size_t size = 10000;
   bool bLoop = true;

   std::set_new_handler(cannot_alloc_heap);

   try {
       while ( bLoop )
       {
           p = new BYTE[size];
           size *= 2;
           delete [] p;
       }
   }
   catch ( const std::exception& e ) {
       std::cerr << e.what() << '\n';
       std::cerr << "실패한 사이즈: " << size << '\n';
   }

   return 0;
}
```

set\_new\_handler() 함수는 new 연산의 실패시 set\_new\_handler()에 등록된 함수 포인터의 함수를 호출해 주는 역할을 합니다. 즉 new 연산자에서 0이 반환되기 전 먼저 등록된 함수를 수행하므로 if로 포인터가 0인지 비교하는 것에 비하여 범용적으로 사용할 수 있습니다. 그리고 위의 cannot\_alloc\_heap() 함수와 마찬가지로 마지막에 bad\_alloc 예외를 던짐으로서 스택 언와인딩(unwinding)을 수행하게 할 수 있으며, 하위에서의 메모리 할당 실패를 상위에서 catch하여 if의 비교보다 더 구조적이며, 안정적으로 처리할 수 있게 해줍니다(게임에서는 try, catch의 남발은 성능상의 제약으로 작용할 수도 있습니다). 또한 상위 수준에서 메모리 할당의 실패를 catch하였을 경우 불필요하고 사용하지 않는 메모리를 줄인 후 다시 시도해볼 수 있게 해준다는 점이 바로 '메모리 할당의 실패에 대한 처리'입니다.

new\_handler가 미리 등록되어 있는지 아닌지 알 수 없는 상태에서 새로운 new\_handler 를 넣고자 한다면 어떻게 되겠습니까?

set\_new\_handler()는 새로운 new\_handler가 입력되면 기존에 가지고 있던 new\_handler를 반환합니다. 따라서 새로운 new\_handler가 처리해야 하는 범위를 벗어나면 기존의 new\_handler를 다시 입력해주는 방법을 사용하면 될 것입니다.

사실 try, catch를 사용하지 않는다면 set\_new\_handler()는 많은 경우 쓸모가 없을 것입니다. 특히나 게임쪽이라면 try, catch에 대한 거부감을 약간씩 가지고 있으므로, set\_new\_handler를 얼마나 쓸런지는 모르겠습니다만 20:80의 법칙과 같이 게임의 속도를 좌지우지 하는 넘들은 코드의 20%에 불과하므로 나머지 코드에는 try, catch와 set\_new\_handler()를 이용하여 조금 더 범용적이면서 실용적이고(매번 if로 0을 검사할 필요가 없으므로) 표준화된 코드를 작성해보심이 어떠십니까?

**ps**  
궁금한 점 중 하나는 set\_new\_handler에서 메모리를 할당하고자 한다면 어떻게 될까요? 두번째로 궁금한 점은 왜 standard group은 set\_new\_handler를 템플릿과 함수 객체를 이용하여 만들지 않았을까요?

아시는 분께서는 제게 답변을 해주시면 감사드리겠습니다. - rihwan@gmail.com

## 저술정보 {#TOC--}

*   자꾸 잊는 프로그래밍 이야기 3편
*   저자: 류인환(rihwan)
*   저술일: 2005년 9월 23일
