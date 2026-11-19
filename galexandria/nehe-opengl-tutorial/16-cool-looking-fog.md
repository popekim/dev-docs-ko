---
title: "16. 그럴듯한 안개효과"
date: 2026-11-19
---

## 소개 {#TOC--}

[![16\. 그럴듯한 안개효과 실행 화면](/ko/galexandria/nehe-opengl-tutorial/images/lesson16.jpg)](/ko/galexandria/nehe-opengl-tutorial/images/lesson16.jpg)

이 강좌코드는 Chris Aliotta 님께서 친히 기부해 주셨습니다. 소스코드는 7장에 기초하여 만들어졌습니다. 저기 보이는 궤짝이  어디선가 본듯한 느낌이 드는 이유도 그때문입니다 ^^. 이 강좌는 여러분에게 안개에 대해서 설명할 목적으로 만들어진 매우 짧은 강좌입니다. 여러분은 세가지 다른 안개 필터를 사용하는 법과 안개의 색을 바꾸는 방법, 그리고 안개의 시작과 끝을 지정하는 법을 배우게 되실 것입니다. 분명히 알아두면 멋진 효과를 만들수 있지요!

## 본문 {#TOC--1}

이 강좌는 Chris Aliotta님이 제공해 주셨습니다.

OpenGL 프로그램에 안개를 추가하고 싶으시다고요? 네, 이 강좌에서 저는 여러분에게 그 방법을 정확히 알려드리겠습니다. 이것은 제가 쓰는 첫 강좌입니다, 저는 아직도 비교적 OpenGL/C++ 프로그래밍에 초보이니 잘못된 게 있다면 저를 잡아드시지 마시고 그냥 곧바로 저에게 알려주세요. 이 코드는 제7강코드에 기반을 두고 있습니다.

### 데이터 설정 {#TOC--2}

우선 안개에 필요한 정보를 저장하는데 필요한 변수들을 지정합니다. fogMode 변수는 세가지 안개종류인: GL\_EXP, GL\_EXP2, GL\_LINEAR를 저장하는데 사용됩니다. 이 셋의 차이점에 대해서는 나중에 설명드리겠습니다. 이 변수들은 코드의 시작부분에서 GLuint texture\[3\] 바로 밑에 위치합니다. fogfilter변수는 어떤 안개형태를 저희가 사용하고 있는지를 기억합니다. fogColor는 안개의 색을 저장할 것입니다. 또한 저는 불리언 변수 gp를 코드의 젤 위에 추가하여 'g'키를 눌렸는지를 기억하게 했습니다.

```cpp
bool   gp;                        // G키가 눌렸는가? ( 새코드 )
GLuint filter;                        // 사용할 필터
GLuint fogMode[]= { GL_EXP, GL_EXP2, GL_LINEAR };    // 세가지 안개종류
GLuint fogfilter= 0;                    // 사용할 안개종류
GLfloat fogColor[4]= {0.5f, 0.5f, 0.5f, 1.0f};        // 안개색상
```

### DrawGLScene 설정 {#TOC-DrawGLScene-}

이제 변수들을 정의했으니 InitGL로 내려가봅시다. 보다 나은 효과를 위해 glClearColor() 라인이 안개와 동일한 색상으로 화면을 지우도록 변경했습니다. 안개가 동작하게 하는데 연관되는 코드는 그리 많지 않습니다. 여러분도 이것이 매우 간단하다는 사실을 곧 발견하게 되실 것입니다.

```cpp
glClearColor(0.5f,0.5f,0.5f,1.0f);            // 안개 색으로 화면을 지운다(수정된 코드)

glFogi(GL_FOG_MODE, fogMode[fogfilter]);        // 안개모드
glFogfv(GL_FOG_COLOR, fogColor);            // 안개 색을 설정
glFogf(GL_FOG_DENSITY, 0.35f);                // 안개의 밀집도
glHint(GL_FOG_HINT, GL_DONT_CARE);            // 안개 힌트 값
glFogf(GL_FOG_START, 1.0f);                // 안개 시작 깊이
glFogf(GL_FOG_END, 5.0f);                // 안개 끝 깊이
glEnable(GL_FOG);                    // GL_FOG를 켠다.
```

이 코드의 처음 세 라인을 살펴봅시다. 첫 라인인 glEnable(GL\_FOG);은 설명할 필요도 없이 코드만 봐두 그 의미를 알수 있습니다. 이것은 기본적으로 안개를 사용하도록 준비를 합니다.

두번째 라인인 glFogi(GL\_FOG\_MODE, fogMode\[fogfilter\]);는 안개 필터모드를 지정합니다. 앞서 fogMode 배열을 선언했던거 기억하시죠? 이것은 GL\_EXP, GL\_EXP2, GL\_LINEAR를 가집니다. 이 부분이 이 변수들이 효력을 발휘하는 곳입니다. 각자에 대해 설명드리겠습니다:

*   GL\_EXP - 화면 전체가 안개로 덮어버리는 기본적인 안개입니다. 이것은 별다른 안개효과를 가져다 주지 못하나 구형 PC에서도 잘 작동하는 방법입니다.
*   GL\_EXP2 - GL\_EXP보다 한단계 나아진 방법입니다. 이것은 화면 전체를 안개로 덮어버리나 장면에 더 나은 깊이감을 줄수 있게 해줍니다.
*   GL\_LINEAR - 이것이 가장 최고의 안개 렌더링 모드입니다. 물체들이 안개속으로 사라지고 다시 보여지는 모습이  훨씬 낳습니다.

세번째 라인인 glFogfv(GL\_FOG\_COLOR, fogcolor);는 안개의 색상을 정합니다. 저희는 미리 fogcolor 변수를 (0.5f, 0.5f, 0.5f, 1.0f)로 정해놓았습니다. 이것은 멋진 회색입니다.

이제 이 코드의 마지막 네 라인을 살펴봅시다. glFogf(GL\_FOG\_DENSITY, 0.35f);는 안개의 밀도를 정의합니다. 이 수를 높이면 안개가 더욱 짙어질 것이고 낮추면 흐려집니다.

glHint(GL\_FOG\_HINT, GL\_DONT\_CARE);라인은 힌트를 지정합니다. 저는 힌트 값에 대해 별 상관을 하지 않으므로 GL\_DONT\_CARE를 사용했습니다.

Eric Desrosiers님이 추가한 내용: glHint(GL\_FONT\_HINT, hintval);에 대해 약간의 설명을 하자면...

hintval은 GL\_DONT\_CARE, GL\_NICEST, 또는 GL\_FASTEST가 될 수 있습니다.

* GL\_DONT\_CARE - OpenGL이 안개의 종류(픽셀당 또는 정점당)와 알려지지 않은 공식을 선택하도록 합니다.  
* GL\_NICEST - 픽셀당 안개를 만듭니다. (품질이 좋음)  
* GL\_FASTEST - 정점당 안개를 만듭니다. (속도가 빠르나 품질이 떨어짐)

다음 라인인 glFogf(GL\_FOG\_START, 1.0f);는 화면의 어느 깊이에서부터 안개가 시작해야하는지를 지정합니다. 안개를 시작시키고자 하는 위치에 따라 이 수를 마음껏 변경하십시요. 다음 라인도 비슷한 glFogf(GL\_FOG\_END, 5.0f);입니다. 이 라인은 OpenGL 프로그램이 어느 깊이에서 안개를 멈춰야 하는지를 알려줍니다.

### 키보드 이벤트 {#TOC--3}

이제 안개를 그리는 코드를 살펴봤으니 상이한 안개모드를 시도할 수 있도록 키보드 명령을 추가해봅시다. 이 코드는 프로그램의 젤 마지막에 다른 키핸들링 코드와 함께 위치합니다.

```cpp
if (keys['G'] && !gp)                    // G키가 눌리고 있는가?
{ 
   gp=TRUE;                    // gp를 참으로 지정
   fogfilter+=1;                    // fogfilter를 1만큼 증가시킴
   if (fogfilter>2)                // fogfilter가 2보다 크면
   {
       fogfilter=0;                // fogfilter를 0으로 만듬
   }
   glFogi (GL_FOG_MODE, fogMode[fogfilter]);    // 안개 모드
} 
if (!keys['G'])                        // G키가 더이상 안눌리는가?
{
   gp=FALSE;                    // 그렇다면 gp를 FALSE으로 설정
}
```

이것이 전부입니다! 다 끝났어요! 이제 여러분의 OpenGL 프로그램 안에 안개를 넣을수가 있습니다. 정말 쉽지 않나여?  여러분이 만약 어떤 질문이나 의견이 있으시다면 주저말고 chris@incinerated.com으로 메일을 보내주세여. 또한 제 웹사이트인 [http://www.incinerated.com/](http://www.incinerated.com/) 과 [http://www.incinerated.com/precursor](http://www.incinerated.com/precursor) 에도 들려주시기 바랍니다.

## 소스코드 다운로드 {#TOC--4}

원문 페이지에서 소스코드를 다운로드 받으실 수 있습니다.

## 원문 정보 {#TOC--5}

*   저자: Christopher Aliotta (Precursor), Jeff Molofee (NeHe)
*   원문보기: [Lesson 16](https://nehe.gamedev.net/tutorial/cool_looking_fog/19001/)
