---
title: "47.  CG 정점 쉐이더"
date: 2026-11-19
---

## 소개 {#TOC--}

[![47\.  CG 정점 쉐이더 실행 화면](/ko/galexandria/nehe-opengl-tutorial/images/lesson47.jpg)](/ko/galexandria/nehe-opengl-tutorial/images/lesson47.jpg)

여러가지 구차스러운 렌더링 작업에서 정점 쉐이더나 픽셀 쉐이더를 사용하면 많은 혜택을 볼 수 있습니다. 가장 눈에 띄게 좋은 이점은 그래픽에 관련된 많은 것들을 GPU에서 처리하여 CPU에 부담을 주지 않는다는 것입니다. Cg는 매우 강력한 쉐이더를 작성하는 비교적 간단한 언어를 제공하고 있습니다. 이 강좌는 여러가지 목적을 가지고 있습니다. 첫째로 간단한 정점 쉐이더를 여러분에게 보여주어 실제로 정점쉐이더가 어떤 일을 할 수 있는지를 설명하는 것이며 두번째는 OpenGL을 이용해서 화면에 있는 오브젝트에 정점 쉐이더를 실행하여 기본적인 정점쉐이더의 동작원리를 설명하는 것입니다. 따라서 이 튜토리얼은 OpenGL을 어느정도 좀 알고 있고 Cg에 흥미를 갖고 있는 초보자분들을 대상으로 합니다. 이 튜토리얼은 Owen Bourne씨가 작성해 주셨습니다. 이 강좌를 즐겁게 읽으셨다면 그분에게 감사하다는 이메일 정도는 보내주세여. 그러면 그 분도 속으로 가슴 뿌뜻해 하실 것 같네여 ^^

## 본문 {#TOC--1}

이 튜토리얼은 가장 최신의 NeHeGL 기반코드를 기초로 해서 만들었습니다. Cg에 대해서 좀 더 많은 정보를 원하시는 분들은 nVidia의 웹사이트( [http://developer.nvidia.com](http://developer.nvidia.com) )와 [http://www.cgshaders.org](http://www.cgshaders.org) 를 살펴봐주시기 바랍니다.

NOTE: 이 튜토리얼은 Cg를 사용해서 정점 쉐이더를 작성하는데 필요한 모든 것들을 설명하지는 않습니다. 이 튜토리얼은 OpenGL내에서 정점 쉐이더를 성공적으로 로드하고 실행하는 방법에 대해서 설명할 목적으로 작성되었습니다.

### 설정: {#TOC-:}

첫번째 단계는 nVidia에서 **Cg Compiler**를 다운로드 하는것입니다. 사이트에 여러가지 버전들(1.0,1.1)이 있는데 버젼마다 각각 변수 이름과 함수들이 변경되어서 각 버젼마다 쉐이더 코드가 컴파일되는 게 있고 안되는 게 있습니다. 그러므로 버젼 1.1을 다운로드 해주시길 바랍니다.

두번째 단계는 비주얼 스튜디오가 Cg의 위치를 알 수 있게끔 Cg 헤더 파일과 라이브러리 파일을 설정하는 것입니다. 저는 선천적으로 설치프로그램이 자동으로 세팅을 해준다고 믿지 않기 때문에 라이브러리 파일을 아래와 같이 복사했습니다.  
   
From: C:\\Program Files\\NVIDIA Corporation\\Cg\\lib  
To:   C:\\Program Files\\Microsoft Visual Studio\\VC98\\Lib

그리고 헤더파일들(Cg 하위 디렉토리와 GL 하위 디렉토리안에 있는 GLext.h)을 아래의 디렉토리에 복사합니다.

From: C:\\Program Files\\NVIDIA Corporation\\Cg\\include  
To:   C:\\Program Files\\Microsoft Visual Studio\\VC98\\Include

자! 이제 제대로 배울 준비가 되었습니다.

### Cg 튜토리얼 {#TOC-Cg-}

이 튜토리얼에서 담고 있는 Cg에 대한 정보 대부분은 Cg Toolkit 사용자 매뉴얼에 있는 내용들입니다.

정점 또는 프래그먼트(픽셀) 프로그램을 작성하실 때 반드시 기억해야 할 중요한 것들이 몇가지 있습니다. 첫번째로 알아야 할 것은 정점 쉐이더는 모든 정점 각각에 대해 개별적으로 실행된다는 사실입니다. 따라서 선택된 정점에 대해 정점 프로그램을 실행하는 유일한 방법은 각각의 정점마다 따로 따로 정점 프로그램을 로드/언로드 하거나 정점들을 정점 프로그램의 영향을 받는 것들과 그렇지 않는 것들로 스트림을 나눠 놓는 것입니다.

두번째로 기억해야 할 점은 정점 프로그램의 출력은 여러분이 프래그먼트 쉐이더를 구현하거나 프래그먼트 쉐이더를 활성화시켰는지 여부에 상관없이 프래그먼트 쉐이더로 흘러간다는 것입니다.

마지막으로 정점 프로그램은 기본도형 조합(Primitive Assembly)전에 정점들에 대해 실행되지만 프래그먼트 프로그램은 래스터라이제이션 후에 실행된다는 사실을 기억하고 있어야 합니다. 이제 튜토리얼을 살펴 보겠습니다.

먼저 빈 파일 하나를 만들어 "wave.cg"라는 이름으로 저장합니다. 그리고 나서 쉐이더에서 이용할 변수 및 정보를 기억하는 구조체를 하나 생성합니다. wave.cg 파일에 이 구조체를 추가합니다.  
   

```cpp
struct appdata 
{
   float4 position : POSITION;
   float4 color    : COLOR0;
   float3 wave    : COLOR1;
};
```

3개의 변수(position, color , wave)는 각각 미리 정의된 이름(차례대로 POSITION, COLOR0, COLOR1)에 연결되어 있습니다. 이 미리 정의된 이름들은 연관된 의미구조(Semantics)들로 참조됩니다. OpenGL에서 이렇게 미리 정의된 이름들은 입력값이 특정한 하드웨어 레지스터로 매핑되게 묵시적으로 지정되어 있습니다. 따라서 메인 프로그램이 이러한 변수들을 위한 데이터를 반드시 제공해야합니다. position 변수는 래스터라이제이션에서 사용되므로 **반드시** 필요합니다. 이 변수는 정점 프로그램에 입력되는 것 중에는 **반드시** 필요한 유일한 변수입니다.

다음 단계는 래스터라이제이션에서 후에 프래그먼트 프로세서로 넘겨지는 출력을 보관하는 구조체를 생성하는 것입니다.  
 

```cpp
struct vfconn
{
   float4 HPos    : POSITION;
   float4 Col0    : COLOR0;
};
```

정점 프로그램 입력과 마찬가지로 출력 변수들도 각각 미리 정의된 이름에 연결되어 있습니다. Hpos는 동차절단공간으로 변환된 좌표를 나타내며, Col0은 정점 프로그램에 의해 변환된 정점 색을 나타냅니다.

이제 해야하는 일은 저희가 새롭게 정의한 구조체 2개를 이용해서 실제로 정점 프로그램을 짜는 것 뿐입니다.

```cpp
vfconn main(appdata IN,    uniform float4x4 ModelViewProj)
{
 vfconn OUT;    // 정점 쉐이더용 출력 구조체 변수
}
```

C 에서처럼 리턴 타입(struct vfconn), 함수 이름(여기서는 main이지만 다른 이름이어도 상관 없습니다), 인자들을 가지도록 함수를 정의합니다. 저희의 예제에서는 입력으로 구조체 appdata를 가지도록 했습니다. 구조체 appdata는 정점의 현재 위치 및 색상과 메쉬를 가로지르는 사인(sine)파를 움직이기 위한 파형 값을 저장하고 있습니다.

이 외에 uniform 인자를 하나 넘겨주고 있습니다. 이 인자는 OpenGL에서의 현재 모델뷰 행렬입니다. 정점들을 변경해도 uniform 값은 변하지 않는 것이 일반적이므로 uniform 으로 했습니다.

**역자주:** uniform 변수는 변수의 초기 값을 지정하는 위치를 암시하는 것으로 이 uniform 변수로 선언되면 변수의 초기값은 지정된 Cg 런타임 라이브러리를 이용해서 전달됩니다. 모델뷰 행렬은 동차절단공간으로 정점 위치를 변환하기 위해 필요합니다.

정점 쉐이더에서 수정된 값들을 저장하는 변수도 하나 선언합니다. 이 값들은 함수 마지막에서 리턴되며 픽셀 쉐이더가 있다면 픽셀 쉐이더로 넘겨집니다.

이제 수정된 것들을 정점 데이터에 적용할 차례입니다.  
 

```cpp
 // Sin 파형을 기반으로 해서 정점 Y위치를 변경함
 IN.position.y = ( sin(IN.wave.x + (IN.position.z / 4.0) ) + sin(IN.wave.x + (IN.position.x / 5.0) ) ) * 2.5f;
```

정점의 현재 X/Z 위치에 따라 정점의 Y 위치를 변경합니다. 정점의 X와 Z위치를 좀더 부드럽게 만들기 위해 X와 Z위치를 각각 5.0과 4.0으로 나눴습니다(제 말의 의미를 알고 싶으시다면 두 값을 모두 1.0으로 바꿔보세여).

IN.wave 변수는 끝없이 증가하는 값을 가지고 있습니다. 이 값으로 인해 sin 파형이 메쉬 위를 이동하게 됩니다. 이 변수는 메인 프로그램 안에 정해져 있습니다. 따라서 sin 파형 값 + 현재 X 또는 Z위치로 메쉬의 X/Y위치의 Y위치를 계산합니다. 마지막으로 좀더 눈에 띄는 파형을 만들기 위해서 그 값에 2.5를 곱했습니다.

이제부터는 프래그먼트 프로그램에 출력할 값을 결정하기 위해 필요한 연산을 수행합니다.  
 

```cpp
   // 정점 위치를 동차절단공간으로 변환
   OUT.HPos = mul(ModelViewProj, IN.position);
         
   // 색상을 IN.color에 있는 값으로 설정 
   OUT.Col0.xyz = IN.color.xyz;

   return OUT;
}
```

먼저 새로운 정점 좌표를 동차절단공간으로 변환합니다. 그리고 나서 지정한 출력 색을 메인 프로그램내에 지정한 입력 색으로 설정합니다. 마지막으로 픽셀 쉐이더에서 사용할 값을 반환합니다.

이제부터 멋진 파형 효과를 만들기 위해 삼각 메쉬를 생성하고 각 정점에 쉐이더를 실행하는 메인 프로그램을 살펴보도록 하겠습니다.

### OpenGL 튜토리얼: {#TOC-OpenGL-:}

Cg쉐이더를 처리하는 기본적인 절차는 먼저 메쉬를 생성하고 Cg프로그램을 메모리에 올려서 컴파일한 뒤, 각 정점들이 그려질 때 이 Cg프로그램을 그 정점에 대해 실행시키는 것입니다.

먼저 Cg를 하기에 앞서 몇가지 필요한 환경설정을 해야합니다. OpenGL에서 Cg쉐이더를 작동시키기 위해서는 반드시 헤더 파일을 인클루드하셔야 합니다. 아래 코드 처럼 #include 다음에 Cg와 CgGl 헤더파일을 포합시킵니다.  
 

```cpp
#include <cg\cg.h>            // 새롭게 추가된 부분: Cg 헤더
#include <cg\cggl.h>            // 새롭게 추가된 부분: Cg OpenGL 헤더
```

이제 프로젝트 환경설정을 해서 작업을 진행할 준비가 되었을 것입니다. 시작하기 전에 비쥬얼 스튜디오에게 정확히 라이브리가 어디에 있는지를 알려줍시다. 메뉴-옵션 부분에서 라이브러리 경로명을 직접 설정해도 좋지만 아시다시피 다음과 같이 파일내에 경로명을 잡을 수도 있습니다.  
 

```cpp
#pragma comment( lib, "cg.lib" )   // 링크시에 Cg.lib를 검색
#pragma comment( lib, "cggl.lib" ) // 링크시에 CgGL.lib를 검색
```

다음은 메쉬용 전역변수와 Cg프로그램의 on/off 토글용 전역변수를 만듭니다.  
 

```cpp
#define        SIZE    64               // 메쉬의 X/Z 축 크기 정의
bool           cg_enable = TRUE, sp;    // Cg 프로그램 On / Off 토클, 스페이스 바가 눌렸는가?
GLfloat        mesh[SIZE][SIZE][3];     // 메쉬
GLfloat        wave_movement = 0.0f;    // 메쉬 사이로 파형을 이동시키기 위한 변수
```

메쉬의 각 에지(X와 Z축)에 64개의 점이 들어가도록 크기를 정의합니다. 그리고 나서 메쉬의 각 정점을 저장할 배열을 생성합니다. 마지막 변수는 메쉬를 통과해 가는 sin 파형을 만들기 위해 필요합니다.

이제는 Cg 전용의 전역변수 몇 개를 정의합니다.  
   

```cpp
CGcontext    cgContext;        // Cg Program을 저장하기 위한 컨텍스트
```

제일 먼저 나오는 변수는 cgContext입니다. 이 변수는 여러 개의 Cg프로그램을 위한 컨테이너입니다. 보통 정점 및 픽셀 쉐이더의 수에 상관없이 단지 하나의 cgContext 변수만이 필요합니다. cgGetFirstProgram과 cgGetNextProram 함수를 이용하면 같은 cgContext로부터 다른 프로그램들을 선택할 수 있습니다.

다음으로 저희의 정점 프로그램용 CGprogram 변수를 정의합니다.  
   

```cpp
CGprogram    cgProgram;    // Cg 정점프로그램
```

CGprogram 변수는 정점 프로그램을 저장하는데 사용합니다. 이 변수는 정점 프로그램의 핸들입니다. CGcontext에 이 CGprogram 변수가 추가됩니다.

다음에 나오는 CGprofile 변수는 정점 프로파일을 저장하는 변수입니다.  
   

```cpp
CGprofile    cgVertexProfile;    // 정점 쉐이더를 위해 사용되는 프로파일
```

우리의 CGprofile 은 가장 알맞은 프로파일을 정의합니다. 그 다음으로 메인 프로그램내의 변수와 쉐이더 내의 변수들 사이를 연결해 주는 변수들이 필요합니다.  
   

```cpp
CGparameter    position, color, modelViewMatrix, wave; // 쉐이더를 위해서 필요한 변수들
```

각 CGparameter는 본질적으로 우리의 쉐이더 안에 있는 매개변수의 핸들입니다.

지금까지 전역변수를 살펴보았습니다. 이제는 메쉬와 정점 프로그램을 설정할 시간입니다.

Initialize 함수에서 “return TRUE;"를 하기 전에 우리가 만든 코드를 추가시킵니다.  
   

```cpp
   glPolygonMode(GL_FRONT_AND_BACK, GL_LINE);// 와이어프레임 모드에서 메쉬를 그린다.

   // 메쉬 생성
   for (int x = 0; x < SIZE; x++)
   {
       for (int z = 0; z < SIZE; z++)
       {
           mesh[x][z][0] = (float) (SIZE / 2) - x;    //원점 주위로 메쉬 중심을 설정
           mesh[x][z][1] = 0.0f;                   //모든 점들의 y값을 0으로 설정
           mesh[x][z][2] = (float) (SIZE / 2) - z;    //원점 주위로 메쉬 중심을 설정
       }
   }
```

먼저 화면을 와이어 프레임으로 변경하기 위해서 glPolygonMode 함수를 호출합니다. 그리고 나서 메쉬 전체를 순회하면서 X와 Z값을 원점 주위에 놓이도록 설정합니다. Y값은 0.0f로 놓습니다. 이 단계에서 생성된 값들이 실행도중에 변경되는 모습을 살펴보는 것도 흥미로울 것입니다.

이제 메쉬 초기화가 끝났으니 Cg를 초기화할 준비가 된 것입니다.  
   

```cpp
   // Cg 설정
   cgContext = cgCreateContext();    // Cg 프로그램을 위한 새로운 컨텍스트 생성
   // Context 생성에 성공했는지 확인
   if (cgContext == NULL)
   {
       MessageBox(NULL, "Cg 컨텍스트 생성에 실패했습니다", "Error", MB_OK);
       return FALSE;    
   }
```

먼저 Cg 프로그램을 저장하기 위해서 새로운 CGContext를 생성합니다. 리턴값이 NULL이면 CGContext 생성에 실패한 것입니다. 이것은 대부분 메모리 할당이 실패하면서 일어납니다.

```cpp
   cgVertexProfile = cgGLGetLatestProfile(CG_GL_VERTEX);
       //가장 최신의 GL 정점 프로파일을 얻는다.
   if (cgVertexProfile == CG_PROFILE_UNKNOWN)
   {
       MessageBox(NULL, "Invalid profile type", "Error", MB_OK);
       return FALSE;        
   }

   cgGLSetOptimalOptions(cgVertexProfile);    //현재 프로파일을 설정
```

이제는 정점 프로파일을 결정할 차례입니다. 최신의 프래그먼트 프로파일을 결정하기 위해서 CG\_GL\_FRAGMENT 프로파일 타입을 cgGLGetLatestProfile 함수에 인자로 넣어 호출합니다. 이때 리턴값이 CG\_PROFILE\_UNKNOWN이 나오면 이용할수 있는 적당한 프로파일이 없는 것을 말합니다. 유효한 프로파일을 얻었다면 cgGLSetOptimalOptions()함수를 호출하여 현재 프로파일을 설정합니다. 이 함수는 사용가능한 컴파일러 인자, GPU, 드라이버에 기초하여 컴파일러 인자를 설정합니다. 이 함수들은 새로운 CG프로그램을 만들 때마다 사용합니다(본질적으로 현재 그래픽 하드웨어와 드라이버에 의존해서 쉐이더 컴파일을 최적화 시킵니다).  
 

```cpp
   // 파일로부터 정점 쉐이더를 로드하고 컴파일한다.
   cgProgram = cgCreateProgramFromFile(cgContext, CG_SOURCE, "CG/Wave.cg", cgVertexProfile, "main", 0);

   // 성공했는지 확인
   if (cgProgram == NULL)
   {
       // 무엇이 잘못됐는지를 알아내기 위해 필요함
       CGerror Error = cgGetError();
       // 무엇이 잘못됐는지를 설명하는 메시지 박스를 보여준다.
       MessageBox(NULL, cgGetErrorString(Error), "Error", MB_OK);
       return FALSE;    // 진행 실패
   }
```

이제는 소스 파일로부터 프로그램을 생성할 차례입니다. cgCreateProgramFromFile() 함수를 호출해서 지정된 파일로부터 Cg 프로그램을 불러와서 컴파일합니다. 첫번째 인자는 저희의 프로그램이 연결될 CGcontext 변수 입니다. 두번째 인자는 Cg코드가 Cg 소스코드(CG\_SOURCE)을 담고 있는 파일인지 아니면 미리 컴파일된 Cg 프로그램(CG\_OBJECT)으로부터 나온 오브젝트 코드로 된 파일인지를 정의하는 부분입니다. 세번째 인자는 Cg 프로그램을 담고 잇는 파일의 이름입니다. 네번째 인자는 프로그램의 특정 타입을 위한 최신의 프로파일입니다(정점 프로그램용으로는 정점 프로파일, 프래그먼트 프로그램용으로는 프로그래먼트 프로파일). 다섯번째 인자는 Cg프로그램의 진입(entry)함수를 의미합니다. 대부분 이 진입함수는 아무거나 올수 있고, 종종 main이 아닌 다른 이름도 쓰입니다. 마지막 인자는 Cg 컴파일러에게 넘겨주는 부가 인자들인데 종종 NULL로 남겨두는 경우가 많습니다.

만약 cgCreateProgramFromFile()함수가 실패한다면 cgGetError함수를 호출해서 마지막으로 에러가 난 부분의 정보를 얻어옵니다. 그러면 CgGetErrorString 함수를 호출해서 CGerror 변수에 담겨있는 에러를 인간이 읽을수 있는 문자열로 만들 수 있습니다.

초기화가 이제 거의 다끝났습니다.  
   

```cpp
   // 프로그램을 로딩한다
   cgGLLoadProgram(cgProgram);
```

다음으로 할 일은 실제로 우리의 프로그램을 로드해서 Cg프로그램에 연결하는 것입니다. 모든 프로그램들은 현재 상태에 연결되기 전에 로드되어야합니다.  
   

```cpp
   // 쉐이더 코드에서 마음대로 인자들을 바꿀수 있도록 각 인자에 대한 핸들을 얻는다. 
   position    = cgGetNamedParameter(cgProgram, "IN.position");
   color        = cgGetNamedParameter(cgProgram, "IN.color");
   wave        = cgGetNamedParameter(cgProgram, "IN.wave");
   modelViewMatrix    = cgGetNamedParameter(cgProgram, "ModelViewProj");

   return TRUE;   // Return TRUE (초기화 성공)
```

초기화의 마지막 단계는 Cg프로그램에서 조작하려고 하는 변수들의 핸들을 얻는 부분입니다. 각 CGparameter에 대해 그에 상응하는 Cg 프로그램 인자의 핸들을 얻습니다. 인자가 존재하지 않으면 cgGetNamedParameter()는 NULL을 리턴합니다.

Cg 프로그램의 인자가 unknown이면 CGprogram 의 인자를 순회하는데 cgGetFirstParameter와 cgGetNextParameter를 사용할 수 있습니다.

드디어 Cg프로그램의 초기화를 끝마쳤습니다. 이제부터는 쉐이더 프로그램을 제거하는 방법을 재빨리 살펴본 뒤 곧바로 화면에 그림을 그리는 부분을 시작하겠습니다.

Deinitialize 함수에서 Cg 프로그램을 해제합니다.  
   

```cpp
   cgDestroyContext(cgContext); //cgContext안에 있는 모든 것을 제거한다.
```

CGcontext 변수 각각에 대해 cgDestroyContext함수를 호출합니다(CGcontext 변수를 여러개 가질 수도 있지만 하나만 있는 것이 보통입니다.) cgDestoryProgram을 호출해서 CGprograms 모두를 개별적으로 제거할 수도 있습니다만 cgDestoryContext를 호출하면 CGcontext에 담겨 있는 모든 CGprogram이 지워집니다. 그 뒤 CGcontext 자신을 제거합니다.

이제 Update 함수에 약간의 코드를 추가할 것입니다. 다음의 코드는 사용자가 스페이스 바를 눌렀는지를 검사하여 그렇다면 불리언 변수 cg\_enable의 상태를 토글시킵니다. 하지만 사용자가 스페이스바를 누르고 있을 때 계속 하여 값을 토글시키고 싶지 않으므로 sp변수를 사용하여 한번만 토글이 되게 합니다.

```cpp
   if (g_keys->keyDown [' '] && !sp)
   {
       sp=TRUE;
       cg_enable=!cg_enable;
   }
```

코드의 마지막 부분은 스페이스 바가 더이상 눌리지 않는지를 검사하는 부분이며, 만약 그렇다면 스페스바 변수를 false로 설정합니다.

```cpp
   if (!g_keys->keyDown [' '])
       sp=FALSE;
```

이제 모든 잡일을 다 처리했으니 실제로 메쉬를 그려서 이것을 정점 프로그램에서 동작시킬 차례입니다.

저희가 수정해야할 마지막 함수는 Draw 입니다. glLoadIdentity()와 glFlush()함수호출 사이에 코드를 추가합니다.

```cpp
   // 멀리서 우리의 메시를 바라다볼 카메라를 위치시킨다.
   gluLookAt(0.0f, 25.0f, -45.0f, 0.0f, 0.0f, 0.0f, 0, 1, 0);
```

먼저 뷰포인트 위치를 메시를 볼 수 있을 정도로 원점에서 멀리 이동시킵니다. 카메라를 원점에서부터 수직으로 25 유닛만큼, 화면으로부터 45유닛만큼 이동시키고 초점을 원점에 맞춥니다.

```cpp
   // 세이더의 Modelview 행렬을 OpenGL의 Modelview 행렬로 설정
   cgGLSetStateMatrixParameter(modelViewMatrix, CG_GL_MODELVIEW_PROJECTION_MATRIX, CG_GL_MATRIX_IDENTITY);
```

다음은 정점 쉐이더의 모델 뷰 행렬을 현재 OpenGl의 모델 뷰 행렬로 설정하는 것입니다. 이것을 하는 이유는 정점 쉐이더가 변경하는 정점들을 동차 절단 공간으로 새로운 위치로 변환하여야 하기 때문입니다. 이 변환은 새로운 위치를 모델뷰 행렬로 곱하는 방법으로 행합니다.  
   

```cpp
   if (cg_enable)
   {
       cgGLEnableProfile(cgVertexProfile); // 정점 쉐이더 프로파일을 활설화 시킴

       // 정점 프로그램을 현재 상태에 연결시킴 
       cgGLBindProgram(cgProgram);
```

이제 정점 프로파일을 활성화 시켜야 합니다. cgGLEnableProfile 함수는 적당한 OpenGL 함수를 호출하여 주어진 프로파일을 활성화 시킵니다. cgGLBindProgram은 프로그램을 현재 상태에 연결시켜 줍니다. 이것은 본질적으로 프로그램을 활성화시켜 주며 그 결과 GPU에 전달되는 각 정점에서 우리의 프로그램이 실행시킵니다. 저희가 프로파일러를 비활성화시킬 때까지 동일한 프로그램이 각 정점상에서 동작할 것입니다.  
   

```cpp
       // 그려지는 색상을 밝은 녹색으로 설정(쉐이더에 의해서 변경될 수 있음)
       cgGLSetParameter4f(color, 0.5f, 1.0f, 0.5f, 1.0f);
   }
```

다음은 메쉬를 그릴 색상을 설정합니다. 멋진 순환 운동(cycling) 효과를 위해 메쉬가 그려지는 동안에 동적으로 색상값을 변경합니다.

cg\_enable이 true로 되었는지를 검사하는 부분을 눈여겨 보십시오. 만약 false로 되었다면 위의 Cg 명령어 중 어느 것도 처리하지 않습니다. cg\_enable이 false로 되는 것은 Cg코드가 실행되는 것을 막는 역할을 합니다.

이제 메쉬를 그릴 준비가 되었습니다!  
   

```cpp
   // 메시 그리기 시작
   for (int x = 0; x < SIZE - 1; x++)
   {
       // 메시의 각 Column 을 위해 Triangle Strip을 그린다.
       glBegin(GL_TRIANGLE_STRIP);
       for (int z = 0; z < SIZE - 1; z++)
       {
       // 쉐이더의 파형 인자를 메인 프로그램의 증감 파형 변수로 설정
           cgGLSetParameter3f(wave, wave_movement, 1.0f, 1.0f);
           glVertex3f(mesh[x][z][0], mesh[x][z][1], mesh[x][z][2]); //정점을 그림
           glVertex3f(mesh[x+1][z][0], mesh[x+1][z][1], mesh[x+1][z][2]);//정점을 그림
           wave_movement += 0.00001f;    //파형값을 증가시킴
           if (wave_movement > TWO_PI)    // 충돌을 막기 위해서 검사 
               wave_movement = 0.0f;
       }
       glEnd();
   }
```

메쉬를 그리기 위해 단순히 각 X축에 대해서 Z축을 따라서 루프를 돕니다(즉, 메쉬의 한쪽에서 다른 쪽으로 향하면서 각 열을 처리합니다). 저희는 각 열마다 새로운 삼각형 스트립을 시작합니다.

그려지는 각 정점마다 우리는 정점 프로그램의 파형 매개변수 값을 동적으로 넘겨줍니다. 이 값은 메인 프로그램에서 wave\_movement 변수에 따라 결정되고 이 변수의 값은 계속하여 증가하기 때문에 사인 파형이 메쉬를 가로질러 아래쪽으로 움직이는 것처럼 보일 것입니다.

그리고 나서 현재 그리고 있는 정점들을 GPU에 넘겨주고, 그러는 와중 GPU는 자동적으로 각 정점에 대해 정점 프로그램을 실행킵니다. wave\_movement 변수의 값이 조금씩 증가하므로 사인 파형도 느리면서도 부드러운 움직임을 보일 것입니다.

wave\_movement 값이 어느 정도 높아지면 그 값을 다시 0으로 리셋시켜서 크래시를 방지합니다. 이를 위해 TWO\_PI가 프로그램의 맨 위쪽에 정의되어 있습니다.

```cpp
   if (cg_enable)
       cgGLDisableProfile(cgVertexProfile);    // 정점 프로파일을 비활성화 시킴
```

렌더링을 끝낸 뒤에는 우리는 cg\_enable이 true인지를 검사해서 만약 그렇다면 정점 프로파일을 비활성화시키고 계속해서 다른 것들을 그려 나갑니다.

## 소스코드 다운로드 {#TOC--2}

원문 페이지에서 소스코드를 다운로드 받으실 수 있습니다.

## 원문 정보 {#TOC--3}

*   저자: Owen Bourne
*   원문보기: [Lesson 47](https://nehe.gamedev.net/tutorial/cg_vertex_shader/25002/)
