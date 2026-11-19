---
title: "5. return"
date: 2026-11-19
---

return 구문은 일반적으로는 어떠한 값을 리턴하는데 사용합니다. 또는 void형을 반환하는 함수에 대하여 함수를 종료하기 위하여 return ;을 사용합니다. 하지만 위의 방법을 제외하고 한가지 더 유용한 방법이 있습니다. 역시나 이것도 template과 결합했을 때 강력하게 사용될 수 있습니다.

아래와 같이 Func1()이라는 함수가 정의되어 있었다고 치죠.

```cpp
void Func1(void);
```

위 Func1()은 입력 인자도 없으며, 반환값도 없습니다. 이제 새로운 함수 Func2()에서 위 Func1()을 호출한다고 가정해봅시다.

```cpp
void Func2(void)
{
 ...
   return Func1();
}
```

자 위와 같이 함수를 만들었을 경우 문제점이 있다고 생각되십니까?

void형 함수를 호출하면서 return 구문과 결합해 놓았습니다. 일반적으로 반환값이 있는 경우에는 저렇게 사용을 많이 해왔지만, void를 반환하는 함수에 대하여는 저런 식으로 작성하신 분은 적으리라 생각됩니다(실제로 Visual Studio 6.0 이하에서는 저런 구문은 에러로 처리되었습니다).

저도 저런 식의 구문이 가능하다는 것을 안 것은 얼마 되지 않습니다. 'C++ 기초 플러스 제4판'(ISBN 8931547900)을 보고 나서 저런 구문이 가능하다는 사실을 깨달았고, TC++PL(ISBN 8945072047)을 보면서 확실히 알게 되었습니다. 일반적으로 많은 분들께서 저것이 쓸모없을 거라고 생각하십니다. 제가 알고 있던 분들도 그냥 그런게 있었냐고 대충 넘어가시죠. 하지만 저 구문은 생각보다 강력한 힘을 발휘할 수 있습니다.

template에서는 임의의 함수 포인터나, 함수 객체를 입력 인자로 받는 Generic 함수나 함수 객체들이 꽤 있습니다. 아래는 binder1st라는 함수 객체입니다. 사실 저도 제대로 쓸 수 있는 함수가 아닙니다. 책의 예제들만 놓고 보면 환상적인 작동을 하는 함수지만 제가 아직 자유자재로 쓸 수준이 안되서요. 어쨋든 아래를 보시면

```cpp
// TEMPLATE CLASS binder1st
template<class _Fn2>
class binder1st
   : public unary_function<typename _Fn2::second_argument_type,
   typename _Fn2::result_type>
{    // functor adapter _Func(stored, right)
public:
   typedef unary_function<typename _Fn2::second_argument_type,
       typename _Fn2::result_type> _Base;
   typedef typename _Base::argument_type argument_type;
   typedef typename _Base::result_type result_type;

   binder1st(const _Fn2& _Func,
       const typename _Fn2::first_argument_type& _Left)
       : op(_Func), value(_Left)
   {    // construct from functor and left operand
   }

   result_type operator()(const argument_type& _Right) const
   {    // apply functor to operands
       return (op(value, _Right));
   }

   result_type operator()(argument_type& _Right) const
   {    // apply functor to operands
       return (op(value, _Right));
   }

protected:
   _Fn2 op;    // the functor to apply
   typename _Fn2::first_argument_type value;    // the left operand
};
```

자 op라는 것은 함수 객체입니다. 몇가지 형식이 들어가 있어야 합니다만, 어쨋든 단순히 op라는 함수 객체만 놓고 보면 반환값이 정해져 있지 않습니다. 즉 반환값이 void일 가능성이 있다는 것이지요. 하지만 binder1st 함수 객체는 전혀 상관없이 op()를 호출하고 있습니다. 만약 void에 대하여 저런 문법이 없었다면 binder1st 함수 객체는 존재 자체가 불가능했을 것입니다.

ps. 근데 bind1st로 사용했던 것 같은데 functional을 찾아보니 binder1st등으로 정의되어 있더군요. 전혀 다른 함수 객체인가요? 사용하는 것만 놓고 보면 거의 같아 보입니다만...

## 저술정보 {#TOC--}

*   자꾸 잊는 프로그래밍 이야기 4편
*   저자: 류인환(rihwan)
*   저술일: 2005년 10월 7일
