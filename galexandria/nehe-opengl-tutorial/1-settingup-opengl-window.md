---
title: "1. OpenGL 윈도우 설정하기"
date: 2025-02-01
---

## 소개

이 튜토리얼은 윈도우즈 환경에서 OpenGL을 설정 및 사용하는 방법을 설명합니다. 이 튜토리얼에서 만들 프로그램은 아무 것도 없는 빈 OpenGL 창을 출력하고, 전체화면-창 모드 전환기능을 가지고 있습니다. 또한 ESC키가 눌리거나 창이 닫힐 때 프로그램을 종료합니다. 별로 중요하게 들리지 않는다구요? 이 프로그램은 앞으로 여기에 올라올 모든 강좌의 프레임워크(기초구조)가 될 것입니다.

OpenGL이 작동하는 방법과 OpenGL 윈도우를 생성하는 방법, 그리고 이해하기 쉬운 코드를 작성하는 방법을 이해하는 것이 매우 중요합니다. 이 튜토리얼의 맨 아래쪽을 보시면 코드를 다운받으실 수 있습니다. 하지만 최소한 이 강좌를 한 번 정도는 정독하신 후에 OpenGL 프로그래밍을 시작하시라고 권해드리고 싶습니다

## 본문

저의 OpenGL 튜토리얼에 오신 여러분을 환영합니다. 저는 OpenGL에 열정을 가지고 있는 보통 사람입니다\! 제가 OpenGL을 처음 접하게 된 시기는 3Dfx사에서 부두 카드 1의 하드웨어 가속 OpenGL 드라이버를 발표할 때였습니다. 이 때 저는 곧바로 OpenGL을 배워야 겠다고 생각했습니다. 하지만 \-.- 인터넷에서 OpenGL에 관련된 책이나 정보를 찾기란 매우 어려운 일이었습니다. 저는 코드를 짜는데 대부분의 시간을 소비했으며 심지어 이메일이나 IRC상에서 여러사람들의 도움을 구하는데 많은 시간을 보냈습니다. 저는 그 때 OpenGL을 이해하고 있던 사람들이 자신들을 전문가라고 하면서도 자기들의 지식을 공유하는 데 전혀 관심이 없다는 사실을 알았습니다. 정말 실망스러웠습니다\!

그래서 저는 OpenGL을 배우고 싶은 분들이 도움을 얻을 수 있을 수 있는 웹사이트를 만들었습니다. 저는 앞으로 진행할 강좌에서 코드 한줄 한줄의 의미와 용도를 최대한 자세히 설명할 것입니다. 저는 최대한 간단한 코드를 사용할 것입니다(즉, MFC코드는 사용하지 않습니다\!). Visual C++과 OpenGL에 완전 초보인 분들도 저의 코드를 보시고 이해하시는 데 어려움이 없을 것입니다. 제 사이트는 그저 OpenGL 튜토리얼을 제공하는 많은 사이트 중의 하나일 뿐입니다. OpenGL에 도가 트신 분들이 보시기엔 너무 쉬울지도 모르겠습니다만 처음 OpenGL을 시작하시는 분들에게는 많은 도움이 될 것이라 생각합니다\!

이 튜토리얼은 2000년 1월에 완전히 다시 작성되었습니다. 이 튜토리얼은 OpenGL 윈도우를 설정하는 방법에 대해 설명하고 있습니다. 이 윈도우는 여러분이 설정한 크기, 해상도, 색상수를 가지는 창모드 또는 전체화면 윈도우가 될 것입니다. 이 코드는 여러분이 앞으로 작성하실 모든 OpenL 프로젝트에서 사용하실 수 있을 정도로 매우 유연합니다. 저 역시 이 코드를 기반으로 모든 강좌를 작성할 것입니다. 저는 유연하고도 강력한 코드를 만들었습니다. 많은 분들이 다양한 에러를 저에게 보고해주셨고 제가 그걸 고쳤기 때문에 이 코드에 메모리 누수 등의 문제는 없을 것입니다. 물론 읽기도 쉽고 수정하기도 쉽습니다. ^^ 이 자리를 빌어서 저의 코드를 수정해주신 Fredric Echols님께 감사의 말을 드립니다\!

자, 이만 각설하고 튜토리얼을 시작해봅시다\! 곧바로 코드부터 설명하겠습니다. 여러분이 해야 하는 첫번째 일은 Visual C++에서 새 프로젝트를 만드는 것입니다. 만약 프로젝트를 생성하는 법을 모르신다면 음.. 아직 OpenGL을 배울 차례가 아니군요. 먼저 Visual C++ 부터 배우셔야 합니다. 이 강좌의 맨 아래쪽을 보시면 Visual C++ 6.0 코드를 다운로드 받으실 수 있습니다. 몇몇 VC++ 버전에서는 bool을 BOOL로, true를 TRUE로, false를 FALSE로 바꿔야 제대로 컴파일 될 것입니다. 저는 이렇게 바꿈으로써 Visual C++ 4.0과 Visual C++ 5.0에서 아무런 문제없이 컴파일할 수 있었습니다. 

Visual C++에서 새 Win32 Application(Console Application이 아닙니다)을 생성하였다면 이제 OpenGL 라이브러리들을 연결해야 합니다. Visual C++에서 OpenGL 라이브러리를 연결시키려면 Project 메뉴를 클릭하시고 Setting 부분을 클릭한후에 Link 탭을 선택합니다. 화면에 보이는 Object/Libary Modules의 kernel32.lib 앞부분에 OpenGL32.lib GLu32.lib ,GLaux.lib 을 추가합니다. 이제 OK 버튼을 클릭하면 OpenGL 윈도우즈 프로그램을 작성할 준비가 된 것입니다.

NOTE 1 : 대부분의 컴파일러들은 CDS\_FULLSCREEN이 정의하지 않습니다. CDS\_FULLSCREEN에 관련된 에러메시지가 등장한다면 프로그램의 맨위에 \#define CDS\_FULLSCREEN 4를 추가시켜 주세요.

NOTE 2: 이 튜토리얼을 처음 작성했을 때 GLAUX를 사용한 방법이 대세였지만 시간이 지남에 따라서 GLAUX를 사용하지 않게 되었습니다. 이 사이트에 있는 대부분의 튜토리얼들은 아직도 오래된 GLAUX 코드를 사용하고 있습니다.  
여러분의 컴파일러가 GLAUX를 지원하지 않거나 이것을 사용하기를 꺼리신다면 메인 페이지(좌측메뉴)  
에서 GLAUX 대체 코드를 다운로드해 주세요.

맨 먼저 저희가 사용할 라이브러리의 헤더파일을 포함시킵니다. 다음의 4줄이 그 일을 합니다.

```cpp
#include <windows.h>              	// 윈도우즈용 헤더파일
#include <gl\gl.h>                	// OpenGL32 라이브러리용 헤더파일  
#include <gl\glu.h>               	// GLu32 라이브러리용 헤더파일  
#include <gl\glaux.h>             	// GLaux 라이브러리용 헤더파일
```

다음으로 프로그램에 사용할 모든 변수들을 정의합니다. 이 프로그램은 빈 OpenGL 윈도우를 생성할 것입니다. 따라서 아직은 많은 변수들을 정의할 필요가 없습니다. 여기서 정의하는 몇 개 안되는 변수들은 매우 중요한 변수들로 앞으로 이 코드를 사용해 작성할 모든 OpenGL 프로그램에서 사용될 것입니다.

첫번째 줄은 렌더링 컨텍스트를 정의합니다. 모든 OpenGL 프로그램은 렌더링 컨텍스트와 연결됩니다. 렌더링 컨텍스트는 OpenGL 호출을 장치 컨텍스트에 연결시키는 존재입니다. OpenGL 렌더링 컨텍스트는 hRC 변수로 정의됩니다. 프로그램이 윈도우를 그릴 수 있기 위해서는 반드시 장치 컨텍스트를 만들어야 합니다. 이것이 두번째 줄입니다. 윈도우즈 장치 컨텍스트는 hDC변수입니다. DC는 윈도우를 GDI(그래픽 장치 인터페이스)에 연결시킵니다. RC는 OpenGL을 DC에 연결시킵니다.

세번째 줄은 hWnd변수로 운영체제가 저희의 창에 할당한 핸들값을 가집니다. 마지막으로 네번째 줄은 프로그램의 인스턴스를 생성합니다.

```cpp
HGLRC       	hRC=NULL;            	// 렌더링 컨텍스트 
HDC         	hDC=NULL;            	// GDI 장치 컨텍스트  
HWND        	hWnd=NULL;           	// 윈도우 핸들
HINSTANCE   	hInstance;           	// 응용프로그램 인스턴스
```

아래의 첫째 줄은 키보드의 키가 눌리는 것을 모니터링하기 위해 사용할 배열을 설정하고 있습니다. 키보드의 키눌림을 처리하는 방법은 몇가지가 있지만 여기서는 배열을 이용했습니다. 이 방법은 믿을만하며 동시에 하나 이상의 키가 눌리고 있는지를 판별할 수 있습니다.

`active` 변수는 프로그램에게 윈도우가 최소화 되었는지 여부를 알려주는데 사용됩니다. 윈도우가 최소화 상태인 동안에는 프로그램의 실행을 잠시 멈춰야 합니다. 다시 말해 윈도우가 최소화 되었을 때는 백그라운드에서 계속 실행되지 않도록 하기 위해서 `active` 변수를 정의한 것입니다.

`fullscreen` 변수는 이름에서 딱 알 수 있듯이 프로그램이 전체 화면(fullscreen)모드로 실행되고 있다면 TRUE값을, 창 모드에서 실행되고 있다면 FALSE 값을 가질 것입니다. 다른 함수 안에서 현재 프로그램이 전체화면 모드 또는 창 모드로 실행되는지를 알아야 하므로 이 변수를 반드시 전역 변수로 선언해야 합니다.

```cpp
bool	keys[256];                            	// 키보드 루틴에 사용하는 배열  
bool	active=TRUE;                          	// 윈도우 활성화 플래그, 디폴트값은 TRUE  
bool	fullscreen=TRUE;                      	// 전체화면 플래그, 디폴트값은 TRUE
```

이제 WndProc()를 선언해야 합니다. WndProc()를 선언하는 이유는 CreateGLWindow()에서 WndProc()를 참조하기 때문입니다. 하지만 WndProc() 위치는 CreateGLWindow() 뒤에 있습니다. C언어에서 A 함수가 있고 그 다음에 나오는 함수 B가 있을 때 여러분이 A함수 내에서 B함수를 호출하는 경우에는 반드시 컴파일러가 정확한 위치를 알 수 있도록 프로그램 맨 위쪽에 함수의 원형을 선언하셔야 합니다. 그래서 저도 CreateGLWindow()에서 WndProc()을 참조할 수 있도록 다음과 같이 WndProc()를 선언했습니다.

```cpp
LRESULT	CALLBACK WndProc(HWND, UINT, WPARAM, LPARAM);            	// WndProc의 선언
```

다음에 나오는 코드는 윈도우의 크기가 바뀔 때마다 OpenGL 장면의 크기를 재조정합니다. 전체모드인 경우는 이 부분이 필요 없지 않냐고 저에게 항의하실지도 모르겠습니다. 하지만 여전히 이 부분이 필요합니다. 왜냐하면 이 루틴은 프로그램이 처음시작할 때 원근감 있는 뷰를 설정하기 위해서 적어도 한번은 호출되기 때문입니다. OpenGL은 이 루틴을 통해 화면에 떠 있는 윈도우의 너비와 폭에 기초해서 창크기를 재조정합니다.  

```cpp    
GLvoid ReSizeGLScene(GLsizei width, GLsizei height)  // GL 윈도우를 초기화하고 크기를 조정한다  
{
    if (height==0)                           // 0으로 나누기를 방지
    {  
   	    height=1;                        	 // 높이를 1로 만듬
    }

    glViewport(0, 0, width, height);         // 현재 뷰포트를 리셋
```

다음은 원근 뷰를 위해 화면을 설정하는 부분입니다. 원근 뷰에서는 거리가 증가함에 따라 물체가 작게 보이므로 사실적으로 보이는 장면을 생성해 줍니다. 원근감은 윈도우의 너비와 폭을 기초로 해서 45도 시각(viewing angle)으로 계산됩니다. 0.1f, 100.0f는 화면에서 어느 깊이에 있는 물체를 그릴 것인지를 나타내는 깊이의 상한과 하한값입니다. 

처음 glMatrixMode(GL\_PROJECTION)를 보시면 '다음의 2줄이 투영행렬에 영향을 주겠군\~' 하고 이해하시면 됩니다. 투영 행렬은 장면에 원근감을 더해 줍니다. glLoadIdentity()는 리셋과 비슷합니다. GlLoadIdentity()는 선택된 행렬을 원래의 상태로 되돌려 놓는 역할을 합니다. 따라서 glLoadIdentity()이 호출되고 나면 장면에 원근 뷰를 설정하게 됩니다. glMatrixMode(GL\_MODELVIEW)는 새로운 변환이 모델뷰 행렬에 영향을 준다는 사실을 나타내고 있습니다. 모델뷰 행렬은 물체들의 정보를 저장하는 곳입니다. 마지막으로 모델뷰 행렬을 리셋합니다. 지금 설명한 내용을 이해하지 못하시더라도 너무 걱정하지 마시길 바랍니다. 나중에 이것에 대해서 자세히 설명해드리겠습니다. 지금은 그냥 원근감을 가지는 장면을 원할 때에 "아, 이렇게 사용하면 되는구나" 라고 이해하시기만 하면 됩니다.

```cpp
   glMatrixMode(GL_PROJECTION);             	// 투영 행렬을 선택  
   glLoadIdentity();                        	// 투영행렬을 리셋한다

   // 창의 비율을 계산`  
   gluPerspective(45.0f,(GLfloat)width/(GLfloat)height,0.1f,100.0f);

   glMatrixMode(GL_MODELVIEW);              	// 모델뷰 행렬을 선택  
   glLoadIdentity();                        	// 모델뷰 행렬을 리셋한다  
}
```

다음은 OpenGL설정에 관한 부분입니다. 먼저 화면을 어떤 색상으로 지울 것인지 설정하고, 그 다음 깊이 버퍼를 켜고, 부드러운 쉐이딩을 설정하는 등의 일을 합니다. 이 루틴은 OpenGL 윈도우가 생성되기 전까지는 호출되지 않습니다. 이 함수는 값을 리턴합니다만 우리의 초기화가 별로 복잡하지 않으므로 현재 이 값에 대해서 신경쓰지 않으셔도 됩니다.

   
`int InitGL(GLvoid)               	       	// OpenGL용 모든 설정은 여기에`  
`{`

다음 줄은 부드러운 쉐이딩을 설정하는 부분입니다. 부드러운 쉐이딩은 폴리곤과 폴리곤 사이의 색상들을 그럴듯하게 블렌드시켜 조명을 부드럽게 만들어 줍니다. 부드러운 쉐이딩에 대해서는 다른 튜토리얼에서 좀더 자세히 설명드리겠습니다.

   `glShadeModel(GL_SMOOTH);           	      // 부드러운 쉐이딩을 설정한다`

다음 줄은 화면이 지울 때 사용할 색상을 설정하는 부분입니다. OpenGL에서 색상의 개념을 잘 모르시는 분들을 위해 짧게 설명드리겠습니다. 색상은 0.0f에서 1.0f까지의 범위값을 가집니다. 여기서 0.0은 가장 어두운 값이며 1.0f은 가장 밝은 값입니다. glClearColor에서 첫번째 인자는 빨강색의 세기, 두번째 인자는 초록, 세번째 인자는 파랑색의 세기입니다. 그 값이 1.0f에 가까울수록 해당 색상이 점점 밝아집니다. 마지막 값은 알파 값입니다. 화면을 지우는 경우에는 이 4번째 값이 별 의미가 없으므로 일단 지금은 알파 값을 1.0f로 놓습니다. 나중에 이부분에 대해서도 다른 튜토리얼에서 설명 드리겠습니다.

빨강, 초록, 파랑의 삼원색을 섞으면 어떤 색상이라도 표현할 수 있다는 거 아시죠? 모르셨다면 초등학교 수업시간에 졸으신 것입니다. 따라서 glClearColor(0.0f,0.0f,1.0f,0.0f)은 화면을 밝은 파랑색으로 지우겠다는 것을 의미합니다. glClearColor(0.5f,0.0f,0.0f,0.0f)이면 중간정도의 빨강색으로 화면을 지우는 것입니다. 0.5f는 밝은 값(1.0f)도 아니고 어두운 값(0.0f)도 아닙니다. 흰색으로 배경을 지우려면 모든 색상요소를 가장 높은 값(1.0f)으로 설정하세요.

   `glClearColor(0.0f, 0.0f, 0.0f, 0.0f);  	// 검정색 배경`

다음 세 줄의 코드는 깊이 버퍼에 관한 것입니다. 깊이버퍼는 화면 속에 존재하는 여러 개의 층(layer)으로 생각하시면 됩니다. 깊이 버퍼는 화면 안에 존재하는 각 물체들의 깊이를 기억합니다. 현재 이 프로그램에서는 깊이버퍼를 사용하지 않지만 3D 화면에 물체들을 그리는 모든 OpenGL 프로그램은 깊이 버퍼를 사용할 것입니다. 깊이 버퍼는 사각형 하나를 원 뒤에 그린 경우 그 사각형이 원 위에 그려지는 일이 없도록 물체들을 정렬합니다. 따라서 깊이 버퍼는 OpenGL에서 매우 중요한 부분입니다.

   `glClearDepth(1.0f);                        	// 깊이버퍼 설정`  
   `glEnable(GL_DEPTH_TEST);                   	// 깊이테스트를 킴`  
   `glDepthFunc(GL_LEQUAL);                    	// 수행할 깊이테스트의 종류`

다음으로 우리는 최선의 원근보정을 수행하라고 OpenGL에게 알립니다. 이것은 약간의 성능 저하를 가져올 수 있지만  
좀더 좋게 보이는 원근 뷰를 만들어 줍니다.

   
   `glHint(GL_PERSPECTIVE_CORRECTION_HINT, GL_NICEST);        	// 정말로 근사한 원근 계산`

이제 마지막으로 TRUE를 반환합니다. 초기화가 제대로 되었는지 알고 싶다면 반환된 값이 TRUE인지 FALSE인지만 보면 됩니다. 여러분의 자신의 코드를 추가하실때는 오류가 발생할 때마다 FALSE를 반환해 주세요. 그러면 에러가 났었다는 사실을 함수밖에서 알아챌 수 있을 것입니다. 지금 당장은 이것에 대해 신경 쓰지 않으셔도 됩니다. 

   `return TRUE;                            	// 초기화가 무사히 끝났음`  
`}`

다음 절은 화면상에 오브젝트를 그리는 코드에 관한 것입니다. 여러분이 화면에 보여주려고 하는 모든 것은 이 부분에 다 포함될 것입니다. 앞으로 진행할 모든 튜토리얼에서 이 부분에 코드들을 추가할 것입니다. 이미 OpenGL을 잘 이해하고 계시는 독자분들은 glLoadIdentity()와 return TRUE사이에 OpenGL 코드를 추가해서 기본도형(기본형상)을 만들어보셔도 좋을듯 싶습니다. 하지만 OpenGL을 처음 접하시는 독자분들은 다음 튜토리얼까지 기다려주세요. 현재 저희가 할 일은 단순히 앞에서 설정한 색상으로 화면을 지우고, 깊이 버퍼를 지우고, 장면을 재설정하는 것 뿐입니다. 즉, 아직 아무것도 그리지 않고 있습니다.

return TRUE는 프로그램에 아무런 문제가 없다는 것을 알려줍니다. 어떤 이유로 프로그램을 멈추고 싶으시다면 return TRUE이전에 아무 라인에나 return FALSE를 추가해서 그리기 코드가 실패했다는 사실을 프로그램에게 알려주면 됩니다. 그러면 프로그램이 종료할 것입니다.  
  

`int DrawGLScene(GLvoid)                           	    // 모든 드로잉을 처리하는 곳`  
`{`  
   `glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);	// 화면과 깊이버퍼를 비움`  
   `glLoadIdentity();                       	           // 현재 모델뷰 행렬을 리셋`  
   `return TRUE;                          	             // 아무 문제가 없음`  
`}`

다음 코드는 프로그램이 종료되기 바로 직전에 호출됩니다. KillGLWindow()의 역할은 렌더링 컨텍스트와   
장치 컨텍스트 및 윈도우 핸들을 해제시키는 것입니다. 저는 여기에 많은 에러 검사 루틴을 추가시켰습니다. 프로그램에서 윈도우를 파괴시킬수 없다면 에러메시지를 담고 있는 메시지 상자가 화면에 팍\~ 하고 뜨면서 어떤 부분이 실패했는지 알려줄 것입니다. 따라서 코드에서 문제가 있는 부분을 찾기가 보다 쉬울 것입니다.

`GLvoid KillGLWindow(GLvoid)                        	// 적절하기 윈도우를 죽인다!`  
`{`

KillGLWindow()에서 제일 처음 하는 일은 전체화면 모드인지를 검사하는 것입니다. 만약 전체화면 모드이면 데스크탑으로 돌아갑니다. 전체화면 모드를 끄기 전에 먼저 윈도우를 파괴해야만 합니다. 하지만 몇몇 비디오 카드인 경우에는 전체화면 모드를 끄기 전에 윈도우를 파괴시킬려고 하면 데스크탑이 맛이 가게 됩니다. 그래서 우리는 먼저 전체화면 모드를 먼저 꺼야 합니다. 이렇게 함으로써 데스크탑이 맛이 가는것을 막을수 있으며 Nvidia와 3dfx 비디오 카드 양쪽에서 정상적으로 작동할 수 있게 됩니다.

   `if (fullscreen)                            	// 현재 전체화면 모드인지 확인`  
   `{`

우리는 원래의 데스크탑 상태로 돌아가기 위해 ChangeDisplaySettings(NULL,0)를 사용합니다. 첫번째 인자에 NULL, 두번째 인자에 0을 전달해 주면 윈도우즈에게 윈도우즈 레지스트리에 현재 저장된 값(기본값: 해상도, 깊이버퍼, 주파수, 기타)을 사용하라고 알려주게 됩니다. 이는 효과적으로 원래의 데스크탑 상태를 회복합니다. 데스크탑으로 돌아간 다음에는 다시 커서를 보이게 해야 합니다. 

   
   	`ChangeDisplaySettings(NULL,0);                	// 그렇다면 데스크탑으로 돌아감`  
   	`ShowCursor(TRUE);                    	// 마우스 포인터를 표시함`  
   `}`

아래의 코드는 렌더링 컨텍스트(hRC)를 가지고 있는지 검사합니다. 만약 렌더링 컨텍스트가 존재하지 않으면 프로그램은 장치 컨텍스트의 존재유무를 검사하는 루틴으로 가게 됩니다.

    
   `if (hRC)                            	// 렌더링 컨텍스트를 가지고 있는지 검사`  
   `{`

만약 렌더링 컨텍스트가 존재한다면 아래의 코드가 렌더링 컨텍스트를 해제(hDC에서부터 hRC분리)할 수 있는지 검사할 것입니다. 여기서 에러를 검사하는 방법을 유심히 살펴봐 주십시오. wglMakeCurrent(NULL,NULL)를 사용하는 방법으로 렌더링 컨텍스트를 해제하려고 합니다. 그런 뒤 해제가 성공적이었는지를 확인합니다. 이는 몇 줄의 코드를 그럴듯하게 한줄로 합친 것입니다.

   `if (!wglMakeCurrent(NULL,NULL))        // DC와 RC 컨텍스트를 해제할 수 있는지 확인`  
   `{`

만약 DC와 RC 컨텍스트를 해제할 수 없다면 MessageBox()를 통해 DC와 RC를 해제할 수 없다는 오류 메시지를  
화면상에 뜨게 할 것입니다. MessageBox()함수에서 첫번째 인자로 NULL을 주는 것은 부모 윈도우가 없다는 것을  
의미합니다. NULL 인자 다음에 텍스트를 써주면 이것이 메시지 상자에 나타납니다. 다음 인자로 "종료 에러"  
는 메시지 상자 맨 위 제목으로 나타나는 문장입니다. 다음 인자에 MB\_OK로 넣는데 이것은 메시지 상자에  
"확인"("OK") 버튼 하나만 나오게 합니다. 

       	MessageBox(NULL,"DC와 RC의 해제가 실패함","종료 에러",MB\_OK | MB\_ICONINFORMATION);  
   	}

다음은 렌더링 컨텍스트를 지워버리는 부분입니다. 만약 실패한다면 에러 메시지가 화면에 나타날 것입니다.

    
   	if (\!wglDeleteContext(hRC))                	// RC를 지울수 있는지 확인  
   	{

만약 렌더링 컨텍스트를 지울 수 없다면 아래 코드에서 보는 것처럼  메시지상자를 띄어 RC 지우기에  
실패했다는 사실을 알려야 합니다. 이부분이 실패했건 성공했건간에 hRC를 NULL로 놓습니다.

    
       	MessageBox(NULL,"렌더링 컨텍스트 지우기에 실패함","종료에러",MB\_OK | MB\_ICONINFORMATION);  
   	}  
   	hRC=NULL;                        	// RC를 NULL로 설정  
   }

이제는 프로그램이 장치 컨텍스트를 가지고 있는지 검사하고 만약 그렇다면 장치 컨텍스트를 해제합니다. 만약 디바이스 컨텍스를 해제하지 못하면 에러메시지를 띄우고 hDC를 NULL로 놓습니다.

    
   if (hDC && \!ReleaseDC(hWnd,hDC))                	// DC를 해제할수 있는지 확인  
   {  
   	MessageBox(NULL,"장치 컨텍스트 해제에 실패함","종료에러",MB\_OK | MB\_ICONINFORMATION);  
   	hDC=NULL;                        	// DC를 NULL로 만듬  
   }

이제 윈도우 핸들이 있는지 검사해서 그렇다면 DestroyWindow(hWnd)를 호출하여 윈도우 핸들을 제거합니다.  
만약 윈도우 핸들을 제거할 수 없다면 에러 메시지를 보여주고 hWnd를 NULL로 바꿉니다. 

   if (hWnd && \!DestroyWindow(hWnd))                	// 창을 파괴할 수 있는지 확인  
   {  
   	MessageBox(NULL,"hWnd 해제에 실패함","종료에러",MB\_OK | MB\_ICONINFORMATION);  
   	hWnd=NULL;                        	// hWnd을 NULL로 만듬  
   }

마지막으로 할 일은 프로그램 시작시에 등록했던 윈도우즈 클래스를 지우는 것입니다. 이는 우리가 윈도우를 적절하게 죽일 수 있게 해줘 그 뒤에 프로그램을 재실행했을 때 "윈도우즈 클래스가 이미 등록되었습니다"라는 에러 메시지가 등장하지 않도록 해줍니다.

   if (\!UnregisterClass("OpenGL",hInstance))            	// 등록된 클래스를 지울수 있는지 확인  
   {  
   	MessageBox(NULL,"윈도우즈 클래스의 등록을 해제할 수 없음","종료에러",MB\_OK | MB\_ICONINFORMATION);  
   	hInstance=NULL;                        	// hInstance를 NULL로 바꿈  
   }  
}

다음은 OpenGL 창을 생성하는 부분입니다. 저는 코드가 짧은 전체화면 윈도우와 코드가 길지만 화면전환이 가능한 윈도우 사이에서 어떤 것을 만들어야 할 것인지 심사숙고했습니다. 그래서 최종 선택은 코드가 좀 길긴 하지만 사용자들에게 친숙한 창모드로 했습니다.

많은 분들이 저에게 이메일로 다음과 같은 질문들을 항상 보내 주십니다.  
" 전체화면모드 말고 창모드를 어떻게 생성할 수 있나요?"  
" 윈도우 제목을 어떻게 바꿀 수 있나요?"  
" 윈도우 픽셀 포맷이나 해상도를 어떻게 바꿀 수 있나요?

다음에 나오는 코드들이 이것에 대한 모든 해답을 담고 있습니다. 따라서 다음의 코드는 훌륭한 교육자료가 될 것이며, OpenGL 프로그램을 작성하는 것을 훨씬 쉽게 만들어 줄 것입니다.

다음의 CreateGLWindow 함수는 BOOL TRUE 또는 FALSE)값을 리턴하며 5개의 매개변수를 가집니다. 이 5개의 인자들은 윈도우 제목, 윈도우 너비, 윈도우 높이, 비트(16/24/32) 및 fullscreenflag입니다. fullscreenflag는 전체화면 모드인 경우 TRUE, 창모드인 경우 FALSE을 가집니다. 윈도우가 성공적으로 생성됐는지 아닌지를 알려주기 위해 불리언 값을 리턴합니다.

   
BOOL CreateGLWindow(char\* title, int width, int height, int bits, bool fullscreenflag)  
{

윈도우즈에게 우리가 원하는 포맷과 일치하는 픽셀포맷을 찾아줄 것을 요청하면 윈도우즈가 찾아낸 모드의 숫자가 PixelFormat 변수에 저장됩니다.  
    
   GLuint    	PixelFormat;                    	// 일치하는 포맷을 찾은 결과를 가진다

wc 변수는 윈도우 클래스 구조체를 저장하는데 사용합니다. 윈도우 클래스 구조체는 윈도우에 대한 정보를 저장합니다. 이 클래스에서 필드값을 변경하면 윈도우의 모습과 동작방법을 변경할 수 있습니다. 모든 윈도우는 윈도우 클래스에 속합니다. 윈도우를 생성하기 전에 '''반드시''' 그 윈도우의 클래스를 등록해야 합니다.

   WNDCLASS	wc;                        	// 윈도우즈 클래스 구조체

dwExStyle와 dwStyle은 차례로 확장 스타일과 일반 스타일 정보를 저장합니다. 저는 생성하려는 윈도우의 형태에 따라 스타일을 변경할 수 있도록 윈도우의 스타일을 저장하는 변수들을 만들었습니다. 전체화면 윈도우용으로는 팝업윈도우를 창모드 윈도우용으로는 테두리를 가진 창을 사용합니다.

   DWORD    	dwExStyle;                    	// 윈도우 확장 스타일  
   DWORD    	dwStyle;               	   	// 윈도우 스타일

다음 5줄의 코드는 사각형의 좌상단, 우하단 값을 나타냅니다. 우리가 드로잉에 사용할 영역이 윈도우 크기에 정확히 일치하도록 조율하기 위해 이 값들을 사용할 것입니다. 보통 640x480 윈도우를 생성하면 윈도우의 경계가 화면의 일부분을 차지할 것입니다. 따라서 실제 그림이 그려지는 공간은 640x480보다 작을 것입니다.  
    
   RECT WindowRect;                        	// 사각형의 좌상단과 / 우하단 값을 저장함  
   WindowRect.left=(long)0;                	// 왼쪽 값을 0으로 지정  
   WindowRect.right=(long)width;           	// 오른쪽 값을 요청된 너비로 지정  
   WindowRect.top=(long)0;      		       // 위쪽 값을 0으로 지정  
   WindowRect.bottom=(long)height;   	      // 아래쪽 값을 요청된 높이로 지정

다음 라인은 전체화면을 나타내는 전역변수 fullscreen를 fullscreenflag과 일치시킵니다.

    
   fullscreen=fullscreenflag;                    	// 전역변수 fullscreen 플래그 값을 지정

다음 코드부분은 윈도우 인스턴스를 얻은 뒤 윈도우 클래스를 선언합니다. 

CS\_HREDRAW와 CS\_VREDRAW의 스타일 옵션은 창의 크기가 변할 때마다 창을 다시 그리는 옵션입니다. CS\_OWNDC는 윈도우 전용의 DC를 생성합니다. '전용'이라는 뜻은 다른 응용프로그램들이 이 DC를 공유하지 않는다는 뜻입니다. WndProc은 프로그램에서 메시지를 감시하는 함수입니다. 추가적인 윈도우 데이터가 없으므로 이 두개의 Extra 필드는 0으로 놓습니다. 다음으로 우리는 윈도우에 ICON을 원하지 않기 때문에 hIcon을  NULL로 놓습니다. 그리고 마우스 포인터로 표준 화살표를 사용하도록 합니다. 배경색은 여기서 중요하지 않습니다. 왜냐하면 GL에서 배경색을 설정해 놓았기 때문입니다. 그리고 메뉴도 사용하지 않을 것이므로 이것도 NULL로 놓습니다. 클래스 이름은 여러분이 아무거나 원하는거으로 넣어주십시오. 저는 간단하게 하기 위해 "OpenGL"로 했습니다.

   hInstance    	\= GetModuleHandle(NULL);           	// 창의 인스턴스를 얻는다  
   wc.style     	\= CS\_HREDRAW | CS\_VREDRAW | CS\_OWNDC;  // 이동시에 다시그리며, 윈도우전용 DC를 사용함  
   wc.lpfnWndProc   \= (WNDPROC) WndProc;           	    // WndProc이 메시지를 처리함  
   wc.cbClsExtra	\= 0;                               	// 추가 윈도우 데이터 없음  
   wc.cbWndExtra	\= 0;                               	// 추가 윈도우 데이터 없음  
   wc.hInstance 	\= hInstance;               	        // 인스턴스를 대입  
   wc.hIcon     	\= LoadIcon(NULL, IDI\_WINLOGO);     	// 디폴트 아이콘을 로딩함  
   wc.hCursor 	  \= LoadCursor(NULL, IDC\_ARROW);     	// 화살표 포인터를 로딩함  
   wc.hbrBackground \= NULL;                            	// GL에 필요한 배경색 없음  
   wc.lpszMenuName  \= NULL;                   	         // 메뉴를 사용하지 않음  
   wc.lpszClassName \= "OpenGL";                  		  // 클래스 이름을 정함

이제 클래스를 등록해 봅시다. 클래스 등록이 잘못되면 에러 메시지상자가 화면에 나오며 이 때 확인(OK)버튼을 클릭하면 프로그램이 종료합니다. 

   if (\!RegisterClass(\&wc))                    	// 윈도우 클래스의 등록을 시도함  
   {  
   	MessageBox(NULL,"윈도우 클래스를 등록하는데 실패했음","오류",MB\_OK|MB\_ICONEXCLAMATION);  
   	return FALSE;                        	// 탈출, 그리고 FALSE를 반환  
   }

이제는 프로그램이 전체화면 모드인지 윈도우 모드에서 실행되는지를 검사합니다. 만약 전체화면 모드이면 우리는   
전체화면 모드로 설정합니다.

   if (fullscreen)                            	// 전체화면 모드를 시도  
   {

다음 코드부분은 많은 분들이 문제를 호소하는 전체화면 모드로 전환하는 부분입니다. 전체화면 모드로 전환시 명심해야 할 중요한 것이 몇가지 있습니다. 전체화면에서 사용하려고 하는 폭과 너비가 창모드에서 사용하려고 하는 폭과 너비와 일치하도록 만드셔야 합니다. 특히 더 중요한 것은 창을 만들기 전에 전체화면 모드를 설정해야 한다는 것입니다. 이 코드에서는 폭과 높이에 대해 신경쓰지 않으셔도 됩니다. 왜냐하면 여기서는 그냥 전체화면과   
윈도우 크기 둘다 인자로 들어온 동일한 크기로 설정되기 때문입니다. 

   	DEVMODE dmScreenSettings;                	// 장치모드  
   	memset(\&dmScreenSettings,0,sizeof(dmScreenSettings));    	// 메모리를 반드시 지우자  
   	dmScreenSettings.dmSize=sizeof(dmScreenSettings);    	// Devmode 구조체의 크기  
   	dmScreenSettings.dmPelsWidth	\= width;        	// 선택된 화면너비  
   	dmScreenSettings.dmPelsHeight	\= height;        	// 선택된 화면높이  
   	dmScreenSettings.dmBitsPerPel	\= bits;            	// 선택된 픽셀당 비트  
   	dmScreenSettings.dmFields=DM\_BITSPERPEL|DM\_PELSWIDTH|DM\_PELSHEIGHT;

위 코드에서 우리는 비디오설정을 저장할 구조체를 0으로 초기화 했습니다. 그런 다음 여러분이 원하는 화면의 폭, 높이, 비트값을 설정합니다. 아래 코드는 이 구조체를 통해 전체화면 모드를 설정하는 부분입니다. 우리는 dmScreenSettings에 폭, 높이, 비트값을 저장했습니다. ChangeDisplaySettings는 dmScreenSettings에 저장된 것과 동일한 모드로 변환하는 부분입니다. 저는 전체화면 모드 변경시 CDS\_FULLSCREEN 인자를 사용했습니다. 왜냐하면 이것은 화면하단의 시작바를 없애주며, 여러분이 전체화면모드와 윈도우 모드를 변경할 때에 바탕화면에 떠있는 창들을 움직이거나 크기를 바꿔놓지 않기 떄문입니다. 

   	// 선택된 모드를 선택하고 결과를 얻어온다. NOTE: CDS\_FULLSCREEN은 시작바를 제거함  
   	if (ChangeDisplaySettings(\&dmScreenSettings,CDS\_FULLSCREEN)\!=DISP\_CHANGE\_SUCCESSFUL)  
   	{

만약 전체화면 모드를 설정할수 없으면 아래 코드가 실행됩니다. 이 부분은 여러분이 설정한 값의 전체화면 모드가 존재하지 않으면 두가지 선택사항을 제시하는 팝업 대화상자가 뜹니다. "창모드로 실행"과 "프로그램 종료"가 그 2가지 선택사항입니다.

    
       	// 지정한 모드가 실패하면 2개의 옵션을 제공한다. (종료 또는 창모드 실행)  
       	if (MessageBox(NULL,"사용중인 비디오 카드가 요청하신 전체화면 모드를 지원\\n하지 않습니다. 대신 창모드를 사용할까요?","NeHe GL",MB\_YESNO|MB\_ICONEXCLAMATION)==IDYES)  
       	{

창모드로 사용하겠다고 결정하면 fullscreen변수를 FALSE로 놓고 프로그램을 계속 진행합니다

    
           	fullscreen=FALSE;            	// 창모드(fullscreen=FALSE)를 선택  
       	}  
       	else  
       	{

사용자가 종료하기로 결정했다면 메시지상자가 화면에 뜨면서 종료하는 중이라고 알려줍니다. 이것은 전체화면 윈도우가 성공적으로 생성되지 않았다고 알려주는 의미로 FALSE를 리턴할 것입니다. 그러면 프로그램은 종료합니다.

    
           	// 메시지상자를 띄워 사용자에게 프로그램이 종료중이라고 알려줌  
           	MessageBox(NULL,"프로그램이 종료될 것입니다","오류",MB\_OK|MB\_ICONSTOP);  
           	return FALSE;                	// 종료 및 FALSE반환  
       	}  
   	}  
   }

위의 전체화면 모드 코드가 실패할 수도 있고, 사용자가 창모드에서 프로그램을 실행하기로 결정할 수도 있기 때문에 화면 및 창 종류를 설정하기 전에 fullscreen값을 다시 확인합니다.

    
   if (fullscreen)                            	// 여전히 전체화면 모드인지 확인  
   {

fullscreen 변수 값이 전체화면 모드이면 윈도우 확장스타일을 WS\_EX\_APPWINDOW로 설정합니다. 이 스타일로 설정하면 현재 화면에 떠 있는 창을 작업바로 내려보냅니다. 또한 WS\_POPUP 윈도우로 설정합니다. 이 값으로 설정하면 윈도우 주변에 테두리를 그리지 않으므로 완벽한 전체화면을 만들 수 있습니다. 

그 다음 할일은 마우스 포인터를 숨기는 것입니다. 마우스로 사용자의 입력을 받아야 하는 프로그램이 아니라면 일반적으로 마우스 포인터를 숨기는 것이 좋습니다. 뭐, 사실 여러분 맘대로지만요.

   	dwExStyle=WS\_EX\_APPWINDOW;                	// 윈도우 확장 스타일  
   	dwStyle=WS\_POPUP;                    	// 윈도우즈 스타일  
   	ShowCursor(FALSE);                    	// 마우스 포인터를 숨김  
   }  
   else  
   {

전체화면모드가 아닌 윈도우 모드를 사용하고 있다면 확장스타일에 WS\_EX\_WINDOWEDGE를 추가합니다. 이것은 윈도우를 좀더 3D틱하게 보여줍니다. 스타일은 WS\_POPUP대신에 WS\_OVERLAPPEDWINDOW를 사용합니다. WS\_OVERLAPPEDWINDOW는 타이틀 바, 크기조정 바, 윈도우 메뉴, 최대/최소화 버튼을 가진 윈도우를 생성해 줍니다.

   	dwExStyle=WS\_EX\_APPWINDOW | WS\_EX\_WINDOWEDGE;        	// 윈도우 확장 스타일  
   	dwStyle=WS\_OVERLAPPEDWINDOW;                	// 윈도우즈 스타일  
   }

다음의 코드는 만드려고 하는 윈도우 스타일에 따라서 윈도우의 크기를 다시 재조정하는 부분입니다. 조정이라는 말은 윈도우의 크기를 실제 요청된 해상도에 일치하게 만든다는 뜻입니다. 일반적으로 테두리선이 창 크기의 일부분을 차지합니다. AdjustWindowRectEx 명령어를 사용하면 이 테두리를 그리는 데 필요한 픽셀들까지도 고려하여 창의 크기를 키우므로 더이상 테두리선이 실제 드로잉 영역을 차지하는 일은 없을 것입니다. 전체화면모드인 경우 이 명령어는 아무런 영향을 주지 않습니다.

    
   AdjustWindowRectEx(\&WindowRect, dwStyle, FALSE, dwExStyle);    	// 실제 요청된 크기로 윈도우 크기를 조정한다

다음은 윈도우를 만들고 이것이 잘 생성됐는지를 보는 코드입니다. 우리는 CreateWindowEx()에 필요한 모든 매개변수를 전달합니다. 우리가 사용하기로 결정한 확장스타일, 클래스 이름(윈도우 클래스를 등록할 때 사용했던 이름과 동일해야 합니다), 윈도우 타이틀, 윈도우 스타일, 윈도우 좌상단 위치(0,0), 윈도우의 폭과 높이 등이 그것입니다. 그리고 부모 윈도우가 없으므로 NULL, 메뉴가 필요하지 않으므로 NULL로 설정하고, 윈도우 인스턴스를 전달해줍니다. 최종적으로 마지막 인자를 NULL로 설정합니다.

윈도우 스타일에 WS\_CLIPSIBLINGS와 WS\_CLIPCHILDREN를 포함시켰다는 사실에 주목해 주세요. WS\_CLIPSIBLINGS와 WS\_CLIPCHILDREN은 모두 OpenGL이 제대로 작동하기 위해 반드시 필요한 스타일들입니다. 이 스타일은 다른 윈도우가 OpenGL 윈도우에 겹쳐서 그려지는 것을 막아줍니다.

   if (\!(hWnd=CreateWindowEx(	dwExStyle,            	// 본 윈도우용 확장스타일  
               	"OpenGL",            	// 클래스 이름  
               	title,                	// 윈도우 타이틀  
               	WS\_CLIPSIBLINGS |        	// 필수 윈도우 스타일  
               	WS\_CLIPCHILDREN |        	// 필수 윈도우 스타일  
               	dwStyle,            	// 선택된 윈도우 스타일  
               	0, 0,                	// 창 위치  
               	WindowRect.right-WindowRect.left,	// 조정된 창 너비를 계산함  
               	WindowRect.bottom-WindowRect.top,	// 조정된 창 높이를 계산함  
               	NULL,                	// 부모 윈도우 없음  
               	NULL,                	// 메뉴 없음  
               	hInstance,            	// 인스턴스  
               	NULL)))                	// WM\_CREATE에 아무것도 전달하지 않음

다음은 윈도우가 적절히 생성됐는지 검사합니다. 윈도우가 제대로 만들어 졌다면 hWnd가 윈도우 핸들값을  
가지고 있을 것입니다. 윈도우가 생성되지 않았다면 아래의 코드가 에러메시지가 띄우고 프로그램을 종료할 것입니다.

   {  
   	KillGLWindow();                        	// 디스플레이를 리셋함  
   	MessageBox(NULL,"윈도우 생성 오류","오류",MB\_OK|MB\_ICONEXCLAMATION);  
   	return FALSE;                        	// FALSE를 반환  
   }

다음은 픽셀 포맷에 대해서 설명할 차례입니다. 우리는 OpenGL과 더블버퍼링, RGBA(빨강,초록,파랑,알파채널)을 지원하는 포맷을 선택한 뒤, 우리가 선택한 z-버퍼 비트(16비트, 24비트, 32비트)에 맞는 픽셀 포맷을 찾으려고 합니다. 마지막으로 16비트 z-버퍼를 설정합니다. 나머지 인자들은 사용되지 않거나 중요하지 않은 것(여기서 스텐실 버퍼와 (느린) accumulation 버퍼는 제외)입니다.

   static	PIXELFORMATDESCRIPTOR pfd=                	// pfd는 윈도우즈에게 우리가 원하는 픽셀포맷을 알려준다  
   {  
   	sizeof(PIXELFORMATDESCRIPTOR),                	// 이 픽셀포맷 설명자의 크기  
   	1,                            	// 버전  
   	PFD\_DRAW\_TO\_WINDOW |                    	// 포맷이 반드시 윈도우를 지원해야 함  
   	PFD\_SUPPORT\_OPENGL |                    	// 포맷이 반드시 OpenGL을 지원해야 함  
   	PFD\_DOUBLEBUFFER,                    	// 반드시 더블 버퍼링을 지원해야함  
   	PFD\_TYPE\_RGBA,                        	// RGBA 포맷을 요청  
   	bits,                            	// 색상깊이를 선택  
   	0, 0, 0, 0, 0, 0,                    	// 색상비트 무시  
   	0,                            	// 알파버퍼 없음  
   	0,                            	// 쉬프트 비트 무시  
   	0,                            	// Accumulation 버퍼 없음  
   	0, 0, 0, 0,                        	// Accumulation 비트 무시  
   	16,                            	// 16비트 Z-버퍼 (깊이 버퍼)  
   	0,                            	// 스텐실 버퍼 없음  
   	0,                            	// Auxiliary 버퍼 없음  
   	PFD\_MAIN\_PLANE,                        	// 메인 드로잉 레이어  
   	0,                            	// 예약됨  
   	0, 0, 0                            	// 레이어 마스크 무시  
   };

윈도우 생성에 아무 문제가 없었다면 이제 OpenGL 장치 컨텍스트를 얻을 차례입니다. 장치 컨텍스트를 얻을 수 없다면 화면에 에러 메시지가 뜰 것이며 프로그램은 종료합니다(FALSE를 반환).

   if (\!(hDC=GetDC(hWnd)))                        	// 장치 컨텍스트를 얻었는지 확인  
   {  
   	KillGLWindow();                        	// 디스플레이를 리셋  
   	MessageBox(NULL,"GL 장치 컨텍스트를 만들 수 없음","오류",MB\_OK|MB\_ICONEXCLAMATION);  
   	return FALSE;                        	// FALSE를 반환  
   }

OpenGL 윈도우용 장치 컨텍스를 얻을 수 있었다면 위에서 설정한 값들과 일치하는 픽셀포맷을 찾을 차례입니다. 알맞은 픽셀 포맷이 없다면 화면에 에러 메시지가 뜰 것이며 프로그램은 종료합니다(FALSE를 반환).

    
   if (\!(PixelFormat=ChoosePixelFormat(hDC,\&pfd)))            	// 일치하는 픽셀 포맷을 찾았는지 확인  
   {  
   	KillGLWindow();                        	// 디스플레이를 리셋함  
   	MessageBox(NULL,"적절한 PixelFormat을 찾을 수 없음","오류",MB\_OK|MB\_ICONEXCLAMATION);  
   	return FALSE;                        	// FALSE를 반환  
   }

알맞은 픽셀 포맷을 발견했다면 픽셀 포맷을 설정합니다. 픽셀 포맷이 설정할수 없다면 에러 메시지가 화면에 에러 메시지가 뜰것이며 프로그램은 종료합니다(FALSE를 반환).

   
   if(\!SetPixelFormat(hDC,PixelFormat,\&pfd))            	// 픽셀 포맷을 설정할 수 있는지 확인  
   {  
   	KillGLWindow();                        	// 디스플레이를 리셋한다  
   	MessageBox(NULL,"PixelFormat을 설정할 수 없습니다.","오류",MB\_OK|MB\_ICONEXCLAMATION);  
   	return FALSE;                        	// FALSE를 반환  
   }

이제 픽셀 포맷을 적절하게 설정하였으니 렌더링 컨텍스트를 얻을 차례입니다. 렌더링 컨텍스트를 가져올 수 없다면 화면에 오류메시지가 출력될 것이고 프로그램이 종료합니다(FALSE를 반환).

    
   if (\!(hRC=wglCreateContext(hDC)))                	// 렌더링 컨텍스트를 가져올 수 있는지 확인  
   {  
   	KillGLWindow();                        	// 디스플레이를 리셋한다  
   	MessageBox(NULL,"GL 렌더링 컨텍스트를 생성할 수 없습니다.","오류",MB\_OK|MB\_ICONEXCLAMATION);  
   	return FALSE;                        	// FALSE를 반환  
   }

여기까지 에러가 없었다면 장치컨텍스트와 렌더링 컨텍스트를 만들 수 있었을 것입니다. 이제 우리가 해야할 일은 렌더링 컨텍스트를 활성화 상태로 만드는 것 뿐입니다. 렌더링 컨텍스트를 활성화시킬 수 없다면 화면에 오류메시지가 등장하고 프로그램이 종료될 것입니다(FALSE를 반환).

   
    
   if(\!wglMakeCurrent(hDC,hRC))                    	// 렌더링 컨텍스트를 활성화하려고 시도  
   {  
   	KillGLWindow();                        	// 디스플레이를 리셋한다  
   	MessageBox(NULL,"GL 렌더링 컨텍스트를 활성화할 수 없습니다.","오류",MB\_OK|MB\_ICONEXCLAMATION);  
   	return FALSE;                        	// FALSE를 반환  
   }

모든 것이 순조롭게 진행되었다면 OpenGL 윈도우가 이미 생성되었을 것입니다. 이제 이 창을 보여줘 봅시다. 이를 위해서는 이 창을 전경(foreground) 창으로 만들고(약간 높은 우선권을 줍니다) 그 창에 포커스를 맞춰야 합니다. 그 뒤, ResizeGLScene함수에 화면 너비와 높이를 매개변수로 전달하여 원근감 있는 OpenGL 창을 설정합니다.

   
    
   ShowWindow(hWnd,SW\_SHOW);                    	// 창을 보여준다  
   SetForegroundWindow(hWnd);                    	// 약간 높은 우선권  
   SetFocus(hWnd);                            	// 키보드 포커스를 현재 윈도우에 맞춘다  
   ReSizeGLScene(width, height);                    	// 원근감 있는 GL화면을 설정한다

마지막으로 InitGL()을 호출하여 조명, 텍스처, 기타 설정이 필요한 것들을 설정합니다. InitGL()에서 여러분 자신만에 오류검사를 할 수도 있습니다. 만약 아무 문제가 없다면 TRUE를 그렇지 않다면 FALSE를 반환하십시요. 예를 들어 InitGL()에서 텍스처를 로딩하던 도중 에러가 발생하였다면 프로그램을 멈추는 것이 좋을 때도 있을 것입니다. InitGL()에서 FLASE를 반환하면 아래의 코드가 FALSE를 에러메시지로 판단하여 프로그램이 종료하도록 만들 것입니다.

   if (\!InitGL())                            	// 새로 생성된 GL창을 초기화한다  
   {  
   	KillGLWindow();                        	// 디스플레이를 리셋한다  
   	MessageBox(NULL,"초기화 실패","에러",MB\_OK|MB\_ICONEXCLAMATION);  
   	return FALSE;                        	// FALSE를 반환  
   }

여기까지 코드가 실행되었다면 윈도우를 성공적으로 만들었다고 안심하셔도 됩니다. 따라서 WinMain()에 TRUE를 반환하여 에러가 없었다고 알려줍니다. 이는 프로그램이 종료되는 것을 방지합니다.

    
   return TRUE;                            	// 성공  
}

다음은 모든 윈도우 메시지를 처리하는 부분입니다. 윈도우 클래스를 등록할 때 윈도우 메시지를 처리하려면 이 콜백 함수로 가라고 지정해줬던거 기억하시죠?

LRESULT CALLBACK WndProc(	HWND	hWnd,                	// 이 창의 핸들  
           	UINT	uMsg,                	// 이 창의 메시지  
           	WPARAM	wParam,                	// 추가 메시지 정보  
           	LPARAM	lParam)                	// 추가 메시지 정보  
{

다음의 코드는 uMsg의 값을 모든 case문에 대해 비교합니다. uMsg는 우리가 처리하고자 하는 메시지의 이름을 가지고 있습니다.

   switch (uMsg)                            	// 윈도우 메시지를 체크한다  
   {

uMsg 변수가 WM\_ACTIVE라면  윈도우가 여전히 활성화 상태인지를 검사합니다. 만약 창이 최소화되었다면 active변수를 FALSE로 지정하고, 활성상태라면 active변수를 TRUE로 지정합니다.

   	case WM\_ACTIVATE:                    	// 윈도우 활성 메시지의 경우  
   	{  
       	if (\!HIWORD(wParam))                	// 최소화 상태를 체크한다  
       	{  
           	active=TRUE;                	// 프로그램이 활성화 상태임  
       	}  
       	else  
       	{  
           	active=FALSE;                	// 프로그램이 더이상 활성화 상태가 아님  
       	}

       	return 0;                    	// 메시지 루프로 돌아감  
   	}

메시지가 WM\_SYSCOMMAND(시스템 명령)라면 case문을 사용하여 wParam을 비교합니다. 만약 wParam이 SC\_SCREENSAVE나 SC\_MONITORPOWER라면 이는 화면보호기가 시작하려고 하거나 모니터가 절전모드에 들어가려고 하는 것입니다. 우리는 0을 반환함으로써 이 두가지 일이 일어나지 않게 만듭니다.

   	case WM\_SYSCOMMAND:                    	// 시스템 명령어를 가로챈다  
   	{  
       	switch (wParam)                    	// 시스템 호출을 체크한다  
       	{  
           	case SC\_SCREENSAVE:            	// 화면보호기가 시작하려고 할 경우  
           	case SC\_MONITORPOWER:            	// 모니터가 절전모드에 들어가려고 할 경우  
           	return 0;                	// 두 경우를 모두 발생시키지 않는다  
       	}  
       	break;                        	// 탈출  
   	}

uMsg가 WM\_CLOSE이라면 창이 닫혔다는 의미입니다. 우리는 종료메시지를 보내고 메인 루프가 이 메시지를 처리할 것입니다. done변수가 TRUE로 설정될 것이고 WinMain()의 메인루프가 종료할 것입니다. 그리고 프로그램이 종료됩니다.

    
   	case WM\_CLOSE:                        	// 닫기 메시지를 받은 경우  
   	{  
       	PostQuitMessage(0);                	// 종료 메시지를 보냄  
       	return 0;                    	// 되돌아감  
   	}

만약 사용자가 키를 하나 누른다면 wParam을 읽어들임으로써 그 키를 찾을 수 있습니다. 그 후엔 keys\[\] 배열에서 그에 해당하는 요소를 TRUE로 설정합니다. 이런 방법을 사용하면 나중에 그 배열을 읽어서 어떤 키들이 현재 눌리고 있는지 알아낼 수 있습니다. 이 방법은 한번에 여러개의 키가 눌리는 것도 허용합니다.

    
   	case WM\_KEYDOWN:                    	// 키가 눌리고 있는 경우  
   	{  
       	keys\[wParam\] \= TRUE;                	// 그렇다면 TRUE로 표시  
       	return 0;                    	// 되돌아감  
   	}

만약 사용자가 누르고 있던 키를 놓는다면 wParam 값을 읽어 그 키를 찾아내야 합니다. 그리고 그에 해당하는 keys\[\] 배열의 요소를 FALSE로 바꿉니다. 이런 방법을 사용하면 나중에 그 요소를 읽을 때 여전히 키가 눌리고 있는지 아닌지를 알아챌 수 있을 것입니다. 키보드에 있는 모든 키는 0\~255 중에 한 값으로 나타낼 수 있습니다. 예를 들어 한 키를 누르고, 그 키를 나타내는 값이 40이라면 keys\[40\]이 TRUE가 될 것입니다. 나중에 이 키를 놓으면 FALSE가 됩니다. 이것이 이 프로그램에서 키눌림을 저장하기 위해 사용하는 방법입니다.

   	case WM\_KEYUP:                        	// 키가 더이상 눌리지 않는지 확인  
   	{  
       	keys\[wParam\] \= FALSE;                	// 그렇다면, FALSE로 설정함  
       	return 0;                    	// 되돌아감  
   	}

창의 크기가 변경될 때마다 uMsg가 WM\_SIZE 메시지가 됩니다. 이런 경우 lParam의 LOWORD와 HIWORD를 읽어 새로운 너비와 높이를 찾아내야 합니다. 새로운 너비와 높이를 ReSizeGLScene()로 전달합니다. 그러면 OpenGL 장면의 크기가 새로운 너비와 폭에 맞게 변경됩니다.

    
   	case WM\_SIZE:                        	// OpenGL 윈도우의 크기를 변경한다.  
   	{  
       	ReSizeGLScene(LOWORD(lParam),HIWORD(lParam));    	// LoWord=너비, HiWord=높이  
       	return 0;                    	// 되돌아감  
   	}  
   }

직접 처리하지 않을 메시지는 DefWindowProc으로 전달하여 윈도우즈가 이를 처리할 수 있도록 합니다.

    
   // 처리되지 않은 모든 메시지를 DefWindowProc으로 전달한다.  
   return DefWindowProc(hWnd,uMsg,wParam,lParam);  
}

다음 코드는 윈도우즈 응용프로그램의 진입부분입니다. 이는 창 생성 루틴 호출, 윈도우 메시지 처리, 사용자 입출력을 처리하는 곳입니다.

    
int WINAPI WinMain(	HINSTANCE	hInstance,            	// 인스턴스  
       	HINSTANCE	hPrevInstance,            	// 이전 인스턴스  
       	LPSTR    	lpCmdLine,            	// 명령행 매개변수  
       	int    	nCmdShow)            	// 윈도우 보여주기 상태  
{

여기서 두 변수를 선언합니다. msg는 처리를 기다리는 메시지가 있는지를 확인하기 위해 사용할 것이고, done 변수는 FALSE값으로 초기화됩니다. 이는 프로그램의 실행이 끝나지 않았다는 사실을 뜻합니다. done이 FALSE로 남아있는 동안 프로그램은 계속 실행될 것입니다. done이 FALSE에서 TRUE로 바뀌면 즉시 프로그램이 종료합니다.

    
   MSG	msg;                            	// 윈도우즈 메시지 구조체  
   BOOL	done=FALSE;                        	// 루프에서 탈출하기 위한 불리언 변수

다음의 코드는 완전히 선택사항입니다. 이는 메시지상자를 출력하여 프로그램을 전체창모드에서 실행할 것인지 물어봅니다. 만약 사용자가 아니오 버튼을 클릭하면 fullscreen 변수가 TRUE(디폴트값)에서 FALSE로 변경되고 프로그램이 전체화면 모드 대신 창모드에서 실행될 것입니다.

  

   // 어떤 창모드를 선호하는지 물어본다.  
   if (MessageBox(NULL,"전체화면 모드에서 실행하시겠습니까?", "전체화면 모드에서 시작?",MB\_YESNO|MB\_ICONQUESTION)==IDNO)  
   {  
   	fullscreen=FALSE;                    	// Windowed Mode  
   }

다음은 OpenGL창을 만드는 방법입니다. 제목, 너비, 폭, 색상깊이, TRUE(전체화면)/FALSE(창모드)를 CreateGLWindow에 전달합니다. 이것이 전부입니다\! 이 코드의 간결함이 절 너무 행복하게 만듭니다. 만약 어떤 이유로 인해 윈도우가 생성되지 않았다면 FALSE가 반환될 것이고, 우리의 프로그램은 즉시 종료될 것입니다(return 0).

    
   // OpenGL 윈도우를 만든다.  
   if (\!CreateGLWindow("NeHe's OpenGL Framework",640,480,16,fullscreen))  
   {  
   	return 0;                        	// 윈도우가 생성되지 않았다면 종료한다.  
   }

다음은 반복문의 시작부분입니다. done이 FALSE인 동안 본 루프가 계속 반복될 것입니다.

    
   while(\!done)                            	// done=TRUE일떄까지 반복한다.  
   {

처음으로 해야할 일은 대기중인 윈도우 메시지가 있는지 살펴보는 일입니다. PeekMessage()를 사용하면 프로그램을 중지시키지 않고도 메시지들을 확인할 수 있습니다. 많은 수의 프로그램이 GetMessage()를 사용합니다. 이것도 잘 작동하나 GetMessage()를 사용하면 WM\_PAINT메시지나 다른 윈도우 메시지를 받을 때까지 여러분의 프로그램은 아무일도 하지 않습니다.

    
   	if (PeekMessage(\&msg,NULL,0,0,PM\_REMOVE))        	// 처리를 기다리고 있는 메시지가 있는지 확인  
   	{

다음의 코드에서는 종료 메시지가 있는지를 확인합니다. 만약 현재 메시지가 PostQuitMessage(0)이 발생시킨 WM\_QUIT 메시지라면 done 변수를 TRUE로 설정하여 프로그램이 종료할 수 있게 합니다.

    
       	if (msg.message==WM\_QUIT)            	// 종료 메시지를 수신했는지 검사한다.  
       	{  
           	done=TRUE;                	// 그렇다면 done=TRUE  
       	}  
       	else                        	// 그렇지 않다면 윈도우 메시지를 처리한다.  
       	{

종료 메시지가 아니라면 이 메시지를 해석한 뒤 WndProc()이나 윈도우즈가 이를 처리하도록 전송(디스패치) 합니다.

    
           	TranslateMessage(\&msg);            	// 메시지를 전송한다.  
           	DispatchMessage(\&msg);            	// 메시지를 전송한다.  
       	}  
   	}  
   	else                            	// 메시지가 없는 경우  
   	{

처리해야할 메시지가 없다면 OpenGL 장면을 그립니다. 아래 코드의 첫째줄은 윈도우가 활성화상태인지 확인합니다. 만약 ESC키가 눌려있다면 done 변수를 TRUE로 설정하여 프로그램이 종료하게끔 만듭니다.

    
       	// 장면을 그린다. ESC키와 DrawGLScene()으로부터의 종료 메시지를 살펴본다.  
       	if (active)                    	// 프로그램이 동장죽인가?  
       	{  
           	if (keys\[VK\_ESCAPE\])            	// ESC키가 눌렸는가?  
           	{  
               	done=TRUE;            	// ESC는 끝마치라고 알려준다  
           	}  
           	else                    	// 아직 끝마칠 시간이 아니다. 화면 업데이트  
           	{

프로그램이 활동중이고 esc키가 눌리지 않았다면 장면을 그리고 버퍼를 스와핑해야 합니다(더블 버퍼링을 이용하여 애니메이션이 깜빡이지 않도록 합니다). 더블 버퍼링이란 보이지 않는 숨겨진 화면에 모든 것을 그리는 방법입니다. 이 버퍼를 바꿔치기(스와핑)하면 현재 우리가 보고 있는 화면에 숨겨진 화면이 되어버리고 숨겨져 있던 화면이 보이게 됩니다. 이 방법을 통해 화면이 그려지는 과정을 볼 수 없게 되는 것입니다. 따라서 숨겨진 화면에 있는것들이 짠\~하고 화면에 나오게 됩니다.

  
```cpp
               	DrawGLScene();            	// 장면을 그린다  
               	SwapBuffers(hDC);        	// 버퍼를 스와핑한다 (더블 버퍼링)  
           	}  
       	}
```

다음의 코드는 2000년 5월 1일에 추가된 코드로서 F1키를 통해 전체화면-윈도우창 모드를 전환할 수 있게 해줍니다.

```cpp
       	if (keys\[VK\_F1\])                	// F1이 눌렸는지 검사  
       	{  
           	keys\[VK\_F1\]=FALSE;            	// 그렇다면 FALSE로 설정  
           	KillGLWindow();                	// 현재 창을 죽인다.  
           	fullscreen=\!fullscreen;            	// 전체화면/창모드 전환  
           	// OpenGL 창을 다시 만든다  
           	if (\!CreateGLWindow("NeHe's OpenGL Framework",640,480,16,fullscreen))  
           	{  
               	return 0;            	// 창이 만들어지지 않았다면 종료한다  
           	}  
       	}  
   	}  
}
```

done 변수가 더이상 FALSE가 아니면 프로그램이 종료합니다. 우리는 OpenGL 윈도우를 적절히 제거하여 모든 것을 해제하고, 프로그램을 종료합니다.

```cpp
    // Shutdown  
    KillGLWindow();                            	// 윈도우를 죽임  
    return (msg.wParam);                   		// 프로그램 종료  
}
```cpp

저는 이 튜토리얼에서 전체화면 OpenGL 프로그램을 설정 및 생성하는 것에 관련된 모든 단계를 가능한 자세히 설명하려고 노력했습니다. ESC를 누르면 종료하고 창의 활성화 여부까지도 관찰하는 과정까지 말입니다. 이 코드를 작성하는 데는 2주의 시간을 들였고, 버그를 수정하고 다른 프로그래머들과 의견을 교환하는 데는 1주가 소요되었습니다. 그리고 2일동안(약 22시간) 이 HTML 파일을 작성하였습니다. 의견이나 질문이 있으신분들은 이메일을 보내주십시오. 제가 잘못 설명한 것이 있거나 코드를 향상시킬 묘안을 가지고 계시다면 저에게 알려주세요. 저는 최고의 OpenGL 튜토리얼을 만들고 싶고, 따라서 여러분의 피드백에 아주 관심이 많답니다.

## 소스코드 다운로드

* [Visual C++](http://nehe.gamedev.net/data/lessons/vc/lesson01.zip)  
* [ASM](http://nehe.gamedev.net/data/lessons/asm/lesson01.zip) ( 제공: Foolman )  
* [Borland C++ Builder 6](http://nehe.gamedev.net/data/lessons/bcb6/lesson01_bcb6.zip) ( 제공: Christian Kindahl )  
* [BeOS](http://nehe.gamedev.net/data/lessons/beos/lesson01.zip) ( 제공: Rene Manqueros )  
* [C\#](http://nehe.gamedev.net/data/lessons/c_sharp/lesson01.zip) ( 제공: Joachim Rohde )  
* [VB.Net CsGL](http://nehe.gamedev.net/data/lessons/csgl/lesson01.zip) ( 제공: X )  
* [Code Warrior 5.3](http://nehe.gamedev.net/data/lessons/cwarrior/lesson01.zip) ( 제공: Scott Lupton )  
* [Cygwin](http://nehe.gamedev.net/data/lessons/cygwin/lesson01.tar.gz) ( 제공: Stephan Ferraro )  
* [D Language](http://nehe.gamedev.net/data/lessons/d/lesson01.zip) ( 제공: Familia Pineda Garcia )  
* [Delphi](http://nehe.gamedev.net/data/lessons/delphi/lesson01.zip) ( 제공: Michal Tucek )  
* [Dev C++](http://nehe.gamedev.net/data/lessons/devc/lesson01.zip) ( 제공: Dan )  
* [Game GLUT](http://nehe.gamedev.net/data/lessons/gameglut/lesson01.zip) ( 제공: Milikas Anastasios )  
* [Irix](http://nehe.gamedev.net/data/lessons/irix/lesson01.zip) ( 제공: Lakmal Gunasekara )  
* [Java](http://nehe.gamedev.net/data/lessons/java/lesson01.zip) ( 제공: Jeff Kirby )  
* [Java/SWT](http://nehe.gamedev.net/data/lessons/java_swt/lesson01.zip) ( 제공: Victor Gonzalez )  
* [JoGL](http://nehe.gamedev.net/data/lessons/jogl/lesson01.jar) ( 제공: Kevin J. Duling )  
* [LCC Win32](http://nehe.gamedev.net/data/lessons/lccwin32/lccwin32_lesson01.zip) ( 제공: Robert Wishlaw )  
* [Linux](http://nehe.gamedev.net/data/lessons/linux/lesson01.tar.gz) ( 제공: Richard Campbell )  
* [Linux/GLX](http://nehe.gamedev.net/data/lessons/linuxglx/lesson01.tar.gz) ( 제공: Mihael Vrbanec )  
* [Linux/SDL](http://nehe.gamedev.net/data/lessons/linuxsdl/lesson01.tar.gz) ( 제공: Ti Leggett )  
* [LWJGL](http://nehe.gamedev.net/data/lessons/lwjgl/lesson01.jar) ( 제공: Mark Bernard )  
* [Mac OS](http://nehe.gamedev.net/data/lessons/mac/lesson01.sit) ( 제공: Anthony Parker )  
* [Mac OS X/Cocoa](http://nehe.gamedev.net/data/lessons/macosxcocoa/lesson01.zip) ( 제공: Bryan Blackburn )  
* [MASM](http://nehe.gamedev.net/data/lessons/masm/lesson01.zip) ( 제공: Nico (Scalp) )  
* [Power Basic](http://nehe.gamedev.net/data/lessons/pbasic/lesson01.zip) ( 제공: Angus Law )  
* [Pelles C](http://nehe.gamedev.net/data/lessons/pelles_c/lesson01.zip) ( 제공: Pelle Orinius )  
* [Perl](http://nehe.gamedev.net/data/lessons/perl/lesson01.zip) ( 제공: Cora Hussey )  
* [Python](http://nehe.gamedev.net/data/lessons/python/lesson01.tar.gz) ( 제공: John Ferguson )  
* [Scheme](http://nehe.gamedev.net/data/lessons/scheme/lesson01.zip) ( 제공: Jon DuBois )  
* [Solaris](http://nehe.gamedev.net/data/lessons/solaris/lesson01.zip) ( 제공: Lakmal Gunasekara )  
* [Visual Basic](http://nehe.gamedev.net/data/lessons/vb/lesson01.zip) ( 제공: Ross Dawson )  
* [Visual Fortran](http://nehe.gamedev.net/data/lessons/vfortran/lesson01.zip) ( 제공: Jean-Philippe Perois )  
* [Visual Studio .NET](http://nehe.gamedev.net/data/lessons/vs_net/lesson01.zip) (제공: Grant James )

## 원문 정보

* 저자: Jeff Molofee (NeHe)  
* 원문보기: [Lesson 01](https://nehe.gamedev.net/tutorial/creating_an_opengl_window_(win32)/13001/)

