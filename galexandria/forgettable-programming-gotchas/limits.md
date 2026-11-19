---
title: "2. Limits"
date: 2026-11-19
---

일반적으로 int는 4 byte(2005년 현재), char는 1 byte등 프로그래머는 이러한 가정하에 프로그램을 짜는 경우가 많습니다. 특히 게임 프로그래밍에서는요. 하지만 실질적으로 int가 4 byte라고 가정하는 것은 무척 위험한 가정입니다. 예전에 퀘이크였나요? 윈도우용 게임을 리눅스로 포팅을 하게 되었었죠? 그때 만약 윈도우용 게임에서 저러한 가정을 하고 프로그래밍이 되어 있었다면 아마도 리눅스로 포팅하는데 엄청난 고통을 겪게 되었을 것입니다. 어디서 버그가 나는지도 모르게 버그가 끈임없이 발생할테니까요. 그리고 저러한 가정은 코드의 곳곳에 숨어서 넓게 퍼져 있을 가능성이 무척 큽니다. 따라서 하나하나 일일히 수정하는 일도 힘들어 지겠죠? 또 자신의 코드가 다른 시스템으로 포팅될 가능성이 없다고 생각하는 것은 실무 프로그래머로서는 무척 안좋은 생각입니다. 왜냐하면 어떤 시스템으로 포팅이 되고 안되고는 프로그래머가 판단할 문제가 아니기 때문입니다. 회사에서 경영진이 결정할 문제겠지요.

과거 C/C++에서는 한계값이 정의된 limits.h 파일이 존재하여 그곳에 #define으로 각 한계값들이 정의되어 있었습니다.

limits.h 파일의 내용 중 일부를 뽑아 보았습니다.

```cpp
#define SHRT_MIN    (-32768)        /* minimum (signed) short value */
#define SHRT_MAX      32767         /* maximum (signed) short value */
#define USHRT_MAX     0xffff        /* maximum unsigned short value */
#define INT_MIN     (-2147483647 - 1) /* minimum (signed) int value */
#define INT_MAX       2147483647    /* maximum (signed) int value */
#define UINT_MAX      0xffffffff    /* maximum unsigned int value */
#define LONG_MIN    (-2147483647L - 1) /* minimum (signed) long value */
#define LONG_MAX      2147483647L   /* maximum (signed) long value */
#define ULONG_MAX     0xffffffffUL  /* maximum unsigned long value */
```

보시면 각각의 값들의 한계값이 정의되어 있는 것을 볼 수 있습니다. 과거에는 저 한계값 define을 이용해서 프로그래밍을 했었습니다. 하지만 Standard C++에서는 조금 더 유연하면서 안정적인 방법이 등장하였습니다(안정적이라고 말한 이유는 define된 것이 아니기 때문입니다.).

아래는 그것의 내용입니다.

```cpp
#include <limits>
#include <iostream>

int main()
{
       std::cout << "largest_float == " << std::numeric_limits<float>::max()
               << ", char is singed == " << std::numeric_limits<char>::is_signed << '\n';
       return 0;
}
```

numeric\_limits<float>과 같은 경우는 이미 컴파일러 또는 코드에서 보통은 특수화한 template입니다. 실제로 numeric\_limits의 정의를 쫒아가 보면

```cpp
template<> class _CRTIMP2 numeric_limits<float>
```

와 같이 되어 있습니다. 위에서 \_CRTIMP2 같은 문장은 굳이 생각하지 않아도 되겠죠? 왜냐하면 define일 것이고 아마도 빈 define일 것이기 때문입니다.

ps. 실제로 위의 내용을 프로젝트에 적용하려고 하다보면 컴파일 에러가 뜨는 경우가 많을 것입니다. 제가 Visual Studio .Net 2003에서 테스트한 결과는 에러가 떳었습니다. 그 이유를 찾아본 결과 일반적인 windows.h 파일에는

```cpp
#define max(a, b) ...
#define min(a, b) ...
```

와 같은 정의문이 포함되어 있기 때문입니다. 저러한 define은 컴파일러가 컴파일 하기 전에 전처리 과정으로 변해 버릴 것이며, 그로 인해 컴파일 에러가 발생합니다. 그래서 저 같은 경우는 위의 내용을 사용하기 위하여 windows.h를 포함한 후

```cpp
#ifdef max
#undef max
#endif
```

와 같이 하여 max 매크로를 제거한 후 사용합니다. 아직까지 Visual Studio도 표준에 다 따라가지 못한 것 같습니다.

## 저술정보 {#TOC--}

*   자꾸 잊는 프로그래밍 이야기 2편
*   저자: 류인환(rihwan)
*   저술일: 2005년 9월 14일
