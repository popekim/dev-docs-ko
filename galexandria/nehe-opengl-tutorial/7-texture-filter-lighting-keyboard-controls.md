---
title: "07. 텍스처 필터, 조명, 키보드 컨트롤"
date: 2026-11-19
---

## 소개 {#TOC--}

[![07\. 텍스처 필터, 조명, 키보드 컨트롤 실행 화면](/ko/galexandria/nehe-opengl-tutorial/images/lesson07.jpg)](/ko/galexandria/nehe-opengl-tutorial/images/lesson07.jpg)

휴~ 여러분들이 여태까지 모든 것을 잘 이해하셨길 바랍니다. 이번 튜토리얼은 정말 큰놈 이거든요. 저는 여러분에게 텍스처를 필터링 하는 2가지 새로운 방법과 간단한 조명, 그리고 키보드 컨트롤에 대해 가르쳐 드릴 것입니다. 뭐 기분이 좋다면 더 많은 것을 알려드릴수도 있지만요. ^^ 이전의 강좌에서 배운 내용에 대해 "다 이해했어요!"라고 말할 자신이 없으신 분들은 다시 한번 돌아가서 복습을 하시기 바랍니다. 다른 강좌에 있는 코드들을 이리저리 고쳐보는 것도 좋은 공부가 될 것입니다. 너무 서두르지 마세요. 천천히 시간을 가지고 각 강좌를 차분히 이해하는 것이 아무 생각없이 프로그램이 돌아가기만 하게 만드는 것보다 백배 낫습니다.

## 본문 {#TOC--1}

이 강좌에서 저는 3가지 다른 텍스처 필터를 사용하는 방법을 설명할 것입니다. 키보드의 키를 이용하여 물체를 움직이는 법과 간단한 조명을 OpenGL 장면에 적용시키는 법도 배워봅시다. 이 강좌는 많은 내용들을 다루니 이전 강좌들을 이해하는데 어려움을 겪으신 분들이 계시다면 다시 한번 복습하고 오시길 바랍니다. 앞으로 보게될 코드로 가기전에 기초를 잘 이해하는 것이 정말 중요합니다.

다시 한번 첫번째 강좌에 나온 코드를 수정하는것부터 시작할것입니다. 평소와 마찬가지로 큰 변화가 있는 곳이 있다면 저는 수정된 코드 부분 전체를 다 올리겠습니다. 우선 프로그램에 몇개의 변수를 추가하는것부터 시작합니다.

   

```cpp
#include    <windows.h>                            // 윈도우즈용 헤더파일
#include    <stdio.h>                              // 표준 입출력용 헤더파일 (새코드)
#include    <gl\gl.h>                              // OpenGL32 라이브러리용 헤더파일
#include    <gl\glu.h>                             // GLu32 라이브러리용 헤더파일
#include    <gl\glaux.h>                           // GLaux 라이브러리용 헤더파일

HDC         hDC=NULL;                              // GDI 장치 컨텍스트
HGLRC       hRC=NULL;                              // 렌더링 컨텍스트
HWND        hWnd=NULL;                             // 윈도우 핸들을 기억
HINSTANCE   hInstance;                             // 응용프로그램의 인스턴스를 기억

bool        keys[256];                             // 키보드 루틴에 사용하는 배열
bool        active=TRUE;                           // 윈도우 활성화 플래그
bool        fullscreen=TRUE;                       // 전체화면 플래그
```

다음의 코드는 새로운 코드들입니다. 3개의 불리언(boolean) 변수들을 추가합니다. BOOL은 그 변수의 값이 TRUE나 FALSE만 될 수 있음을 뜻합니다. light변수는 조명이 켜져 있는지 꺼져있는지를 판단하기 위한 변수입니다. lp와 fp는 'L'키나 'F'키가 눌려있는지를 확인하기 위한 변수입니다. 나중에 이 변수들이 필요한 이유를 설명드리겠습니다. 지금은 그저 저놈들이 중요하다고만 알아둡시다.

```cpp
BOOL    light;                                    // 조명 ON / OFF
BOOL    lp;                                       // L이 눌렸는가?
BOOL    fp;                                       // F가 눌렸는가?
```

이제 5개의 변수를 선언할 차례입니다. xrot은 x축 회전각, yrot은 y축 회전각, xspeed는 x축 주위로 회전하는 속도, yspeed는 y축 주위로 회전하는 속도를 나타냅니다. z라는 이름의 변수도 있는데 이 변수는 화면바깥쪽으로 얼마나 나오는지를(z축을 따라)를 나타내는 깊이값입니다.

```cpp
GLfloat    xrot;                                   // X 회전
GLfloat    yrot;                                   // Y 회전
GLfloat xspeed;                                    // X 회전속도
GLfloat yspeed;                                    // Y 회전속도
GLfloat    z=-5.0f;                                // 화면 안쪽으로 깊이
```

이제 조명을 생성하는 데 사용할 배열을 설정합니다. 우리는 두가지 다른 종류의 조명을 사용할 것입니다. 첫번째  조명은 주변광(ambient light)입니다. 주변광은 어떤 특정방향으로부터 오는 빛이 아닙니다. 장면 안에 있는 모든 물체들은 주변광에 의해 영향을 받습니다. 두번쨰 조명은 산란광(diffuse light)입니다. 산란광은 광원으로 부터 나온 빛이 장면안에 있는 물체의 표면에 반사됩니다. 이 조명이 직접 비추는 물체의 면은 매우 밝을 것이며 빛이 거이 닿지 않는 부분은 이보다 어두울 것입니다. 이것은 상자의 한쪽 측면에 그럴듯한 쉐이딩 효과를 만듭니다.

조명을 만드는 법은 색을 만드는 법과 동일합니다. 첫번째 수가 1.0f이고 다음 두 수가 0.0f라면 이는 밝은 빨강색 조명이 될 것입니다. 만약 세번째 값이 1.0f이고 처음 두 값이 0.0f이면 이는 밝은 파랑색 입니다. 마지막 숫자는 알파 값입니다. 일단 이 값을 1.0f로 나둡시다.

아래 라인에서 볼 수 있듯이 절반의 강도(0.5f)를 가진 흰색 주변광을 위한 값을 저장합니다. 모든 값이 0.5f이므로 완전히 꺼진것(검정)과 완전히 밝은(흰색)의 중간정도의 빛을 만들게 될 것입니다. 동일한 값의 빨강, 파랑, 녹색을 혼합하면 검정(0.0f)에서 흰색(1.0f) 사이의 무채색을 만들 수 있습니다. 주변광을 사용하지 않으면 산란광이이 닿지 않는 부분이 매우 어둡게 보일 것입니다.

   

```cpp
GLfloat LightAmbient[]= { 0.5f, 0.5f, 0.5f, 1.0f };                // 주변광 값(새 코드)
```

다음 라인에서는 매우 밝고 최고의 강도를 가진 산란광 값을 설정합니다. 모든 값은 1.0f입니다. 이 값은 우리가 가질 수 있는 최대의 밝은 빛을 의미하며 이 산란광 값은 궤짝의 앞면을 근사하게 비쳐줄것입니다.

   

```cpp
GLfloat LightDiffuse[]= { 1.0f, 1.0f, 1.0f, 1.0f };                 // 산란광 값(새 코드)
```

드디어 빛의 위치를 저장할 차례입니다. 처음 3개의 수는 glTranslate에 들어가는 수와 동일합니다. 첫번째 수는 x평면상에서 좌우로 움직이는 것이고, 두번째 수는 y평면상에서 위아래로 움직이는 것입니다. 그리고 세번째 수는 z평면상에서 화면 안팎으로 이동하는 것입니다. 저희가 원하는 것은 빛이 궤짝의 앞면을 정면으로 비추는 것이므로 좌우로는 움직이지는 않겠습니다. 따라서 첫번째 값은 0.0f이며(x축 상에서 움직이지 않음) 위아래로도 움직이고 싶지 않으므로 두번째 값도 역시 0.0f입니다. 세번째 값은 빛이 언제나 궤짝의 앞면을 비춰야 하므로 관찰자 쪽으로 움직입니다. 여러분의 모니터에 있는 유리가 z평면상에 0.0f라고 하면 저희는 빛을 z평면상에 2.0f에 위치시킬 것입니다. 실제로 빛을 볼 수 있다면 이것은 모니터에 있는 유리 앞부분에 떠있을 것입니다. 모니터 유리 앞부분에 빛을 놓이게 하면 빛이 궤짝 뒤에 있을 수 있는 경우는 궤짝이 모니터 유리 앞쪽에 놓여 있는 경우일 뿐입니다. 물론 궤짝이 모니터에 있는 유리 뒤쪽에 있지 않다면 여러분은 궤짝을 더 이상 볼 수 없습니다. 그래서 빛이 어디에 있는지는 중요치 않습니다. 왜냐하면 빛이 어디에 있던지간에 모니터 앞에 괘짝이 놓여져 있으면 볼수가 없기 떄문이져! 이해가 되시나요?

세번째 매개변수를 간단히 설명할 수 있는 방법은 없습니다. -5.0f보다 -2.0f이 여러분에게 더 가깝다는 정도는 아시고 계실것이고, -100.0f는 화면 안쪽으로 멀리 있습니다. 0.0f를 사용하면 이미지가 전체 화면을 다 덮을정도로 너무 클 것입니다. 양수의 값을 사용하면 이미지가 이미 화면을 지나쳐 사라졌기에 더이상 화면에 보이지 않습니다. 이것이 제가 화면밖에 있다고 말하는 경우입니다. 그 물체는 여전히 화면밖에 있지만 여러분은 더 이상 볼 수 없습니다.

마지막 숫자는 1.0f으로 놓습니다. 이것은 앞의 세수에 의해 지정된 좌표가 광원의 위치라는 것을 나타내는 것으로 점광원을 의미합니다. 만약 0.0으로 한다면 앞의 세수로 이루어진 벡터 방향으로 무한대로 떨어진 곳에 광원이 위치한다는 것으로 평행광원을 의미합니다. 이에 대해 자세한 내용은 다른 튜토리얼에서 자세히 설명하겠습니다.

```cpp
GLfloat LightPosition[]= { 0.0f, 0.0f, 2.0f, 1.0f };                 // 광원의 위치(새코드)
```

아래의 filter 변수는 출력할 텍스처를 기억합니다. 첫번째 텍스처(0번 텍스처)는 gl\_nearest(smoothing 없음)을 사용하여 만듭니다. 두번째 텍스처(1번 텍스처)는 이미지를 어느정도 부드럽게 만드는 gl\_linear필터링을 사용합니다. 세번째 텍스처(2번 텍스처)는 비트맵 텍스처를 사용하여 매우 그럴싸해 보이는 텍스처를 만들어냅니다. filter 변수는 저희가 사용하고자 하는 텍스처에 따라 0, 1, 2의 값을 가질 것입니다. 우선 첫번째 텍스처부터 시작해봅시다.

GLuint texture\[3\]는 3개의 상이한 텍스처를 담을 수 있는 저장공간을 만듭니다. 텍스처들은 texture\[0\], texture\[1\], texture\[2\]에 저장될 것입니다.

```cpp
GLuint    filter;                                    // 사용할 필터
GLuint    texture[3];                                // 텍스처 3개용 저장공간

LRESULT    CALLBACK WndProc(HWND, UINT, WPARAM, LPARAM);                // WndProc 선언
```

이제 비트맵 하나를 불러와서 비트맵으로부터 3개의 다른 텍스처를 생성합니다. 이 튜토리얼은 glaux 라이브러리를 사용하여 비트맵을 불러오니 코드를 컴파일하시기전에 glaux 라이브러리를 포함시키는 것을 잊지 마십시요. 델파이와 Visual C++은 모두 glaux 라이브러리를 가지고 있는 것이 확실하지만 다른 프로그래밍 언어에 대해서는 아는 바 없습니다. 아래의 코드 중 새로운 코드라인들에 대해서만 설명하겠습니다. 주석이 없는 코드 부분에 대한 설명은 제6강을 참조해주십시요. 6강은 비트맵 이미지를 불러와서 텍스처 맵을 만드는 방법에 대해 상세히 설명하고 있습니다.

위의 코드에 바로 이어서 다음의 코드를 더합니다. 물론 이 코드는 ReSizeGLScene()보다는 위쪽에 위치합니다. 이는 강좌 6에서 비트맵 파일을 불러오기 위해 사용했던 코드입니다. 아무것도 바뀐 것이 없지요. 다음 라인 중에 이해가 안되는 부분이 있다면 튜토리얼 6강을 읽어주십시요. 거기에 자세한 설명이 있습니다.

```cpp
AUX_RGBImageRec *LoadBMP(char *Filename)                    // 비트맵 이미지를 읽어들임
{
   FILE *File=NULL;                            // 파일 핸들

   if (!Filename)                                // 파일명이 매개변수로 전달되었는지 검사
   {
       return NULL;                            // 파일명이 전달되지 않았다면 NULL을 반환함
   }

   File=fopen(Filename,"r");                        // 파일이 존재하는지 검사

   if (File)                                // 파일이 존재하는가?
   {
       fclose(File);                            // 핸들을 닫음
       return auxDIBImageLoad(Filename);                // 비트맵을 읽어와 그 포인터를 반환
   }
   return NULL;                                // 읽기에 실패하였다면 NULL을 반환
}
```

    
이것이 바로위의 코드를 호출하여 비트맵을 로딩한 뒤 3개의 텍스처로 바꾸는 부분입니다. Status 변수를 사용하여 텍스처가 로딩되거나 생성되었는지 아닌지를 기억합니다.

   

```cpp
int LoadGLTextures()                                // 비트맵 로딩 및 텍스처로 변환
{
   int Status=FALSE;                            // 상태를 나타냄

   AUX_RGBImageRec *TextureImage[1];                    // 텍스처용 저장공간을 만듬

   memset(TextureImage,0,sizeof(void *)*1);                // 포인터를 NULL로 만듬
```

이제 비트맵을 불러와서 비트맵을 텍스처로 변환합니다. TextureImage\[0\]=LoadBMP("Data/Crate.bmp")은 LoadBMP() 코드로 점프합니다. data 디렉터리에 위치한 Crate.bmp 파일이 읽혀질 것입니다. 모든 일이 잘되면 이미지 데이터가 TextureImage\[0\]에 저장될 것이며, Status는 TRUE가 되며 이제 텍스쳐를 만들 차례입니다.

   

```cpp
   // 비트맵을 로딩. 오류 검사. 비트맵을 찾을 수 없다면 종료
   if (TextureImage[0]=LoadBMP("Data/Crate.bmp"))
   {
       Status=TRUE;                            // 상태를 TRUE로 설정
```

이제 TextureImage\[0\]에 이미지 데이터를 읽어왔으니 이 데이터를 이용하여 3개의 텍스처를 만듭니다. 아래의 코드는 OpenGL에게 이제부터 3개의 텍스처를 만들어서 그 텍스처들을 차례대로 texture\[0\], texture\[1\], texture\[2\]에 저장하라고 명령합니다.

```cpp
       glGenTextures(3, &texture[0]);                    // 3개의 텍스처를 만든다
```

제6강에서 저희는 선형필터 텍스쳐 맵을 사용했었습니다. 선형필터는 상당한 양의 CPU 시간을 필요로 하긴 하지만 정말 멋지게 보입니다. 여기서 만들어 낼 첫번째 텍스처는 GL\_NEAREST를 사용합니다. 간단히 말해 이 텍스처 종류는 필터링을 전혀 가지지 않습니다. 이는 매우 약간의 CPU시간만을 사용하지만 정말 형편없어 보입니다. 텍스처가 각이져 보이는 게임을 플레이해본적이 있으시다면 아마 그 게임은 이런 타입의 텍스처를 사용하고 있을 것입니다. 이 텍스처 타입의 유일한 장점은 느린 컴퓨터에서도 빠르게 실행된다는 것입니다.

MIN과 MAG에 모두 GL\_NEAREST를 사용하는 것 보이시죠? GL\_NEAREST를 GL\_LINEAR와 혼합할 수도 있으며, 그러면 텍스처가 조금은 더 나아 보일 것입니다. 하지만 저희는 속도에 관심이 있으므로 양쪽에 모두 저품질을 사용합니다. MIN\_FILTER는 이미지가 실제 텍스처 사이즈보다 더 작게 그려질때 사용되는 필터이며, MAG\_FILTER는 이미지가 원래 텍스처 크기보다 더 클 때 사용됩니다.

```cpp
       // NEAREST 필터링을 사용한 텍스처를 만든다
       glBindTexture(GL_TEXTURE_2D, texture[0]);
       glTexParameteri(GL_TEXTURE_2D,GL_TEXTURE_MAG_FILTER,GL_NEAREST); // ( 새코드 )
       glTexParameteri(GL_TEXTURE_2D,GL_TEXTURE_MIN_FILTER,GL_NEAREST); // ( 새코드 )
       glTexImage2D(GL_TEXTURE_2D, 0, 3, TextureImage[0]->sizeX, TextureImage[0]->sizeY, 0, GL_RGB, GL_UNSIGNED_BYTE, TextureImage[0]->data);
```

다음으로 만들 텍스처는 제6강에서 사용했던 것과 동일한 선형 필터링을 적용한 텍스처입니다. 여기서 바뀐 점은 texture\[0\]이 아닌 texture\[1\]에 텍스처를 저장한다는 것입니다. 이것이 두번째 텍스처이기 때문입니다. 위처럼 이 텍스처도 texture\[0\]에 저장해버리면 GL\_NEAREST 텍스처(첫번째 텍스처)를 덮어써 버릴 것입니다.  
   

```cpp
       // 선형필터를 사용하는 텍스처를 만든다
       glBindTexture(GL_TEXTURE_2D, texture[1]);
       glTexParameteri(GL_TEXTURE_2D,GL_TEXTURE_MAG_FILTER,GL_LINEAR);
       glTexParameteri(GL_TEXTURE_2D,GL_TEXTURE_MIN_FILTER,GL_LINEAR);
       glTexImage2D(GL_TEXTURE_2D, 0, 3, TextureImage[0]->sizeX, TextureImage[0]->sizeY, 0, GL_RGB, GL_UNSIGNED_BYTE, TextureImage[0]->data);
```

이제 텍스처를 만드는 새로운 방법을 살펴봅시다. 바로 밉매핑(Mipmapping)입니다! 화면상에서 이미지를 매우 작게 만들어보면 세세한 디테일들이 사라지는 현상을 보신적이 있으실 것입니다. 처음에는 멋지게 보이던 패턴들이 점차 이상하게 보이기 시작하지요. OpenGL에게 밉맵을 이용한 텍스처를 구축하라고 말해주면, OpenGL은 다른 크기의 고품질 텍스처를 만듭니다. 밉맵을 사용한 텍스처를 화면상에 그리면 OpenGL은 자신이 만들었던 텍스쳐중 가장 그럴듯해 보이는 녀석(최고의 디테일을 가진 텍스처)을 선택하여 화면에 그릴 것입니다. 즉, 원래 이미지의 크기를 변경하는 일을 하지 않습니다(이러면 디테일을 잃어버리기 때문이지요).

제6강에서 64, 128, 256 등 OpenGL 텍스처의 폭 및 높이제한을 피할 수 있는 방법이 있다고 말씀드린 바 있습니다. gluBuild2DMipmaps가 바로 그 해결방법입니다. 제가 알아낸 바로는 밉맵 텍스처를 구축할 때는 어떤 높이와 너비의 비트맵 이미지라도 사용할 수 있습니다. OpenGL이 그 이미지를 알맞은 너비 및 높이로 자동적으로 변경해줄 것입니다.

이 텍스처는 세번째이므로 texture\[2\]에 저장합니다. 이제 texture\[0\]에는 필터링을 사용하지 않는 텍스처가, texture\[1\]에는 선형 필터링을 사용하는 텍스처가, texture\[2\]에는 밉맵을 이용하는 텍스처가 저장되어 있습니다. 이쯤되면 본 튜토리얼에 필요한 텍스처를 모두 다 구축한 것이 되는군요.

```cpp
       // 밉맵 텍스처를 만든다
       glBindTexture(GL_TEXTURE_2D, texture[2]);
       glTexParameteri(GL_TEXTURE_2D,GL_TEXTURE_MAG_FILTER,GL_LINEAR);
       glTexParameteri(GL_TEXTURE_2D,GL_TEXTURE_MIN_FILTER,GL_LINEAR_MIPMAP_NEAREST); // ( 새코드 )
```

다음 라인은 밉맵 텍스처를 만듭니다. 저희는 3가지 색(빨강, 녹색, 파랑)을 이용해서 2D 텍스처를 만들 것입니다. TextureImage\[0\]->sizeX는 비트맵의 너비이고 TextureImage\[0\]->sizeY는 비트맵의 높이입니다. GL\_RGB는 저희가 빨강, 녹색, 파랑의 순서로 색을 사용한다는 의미이고, GL\_UNSIGNED\_BYTE는  텍스쳐가 바이트로 구성되어 만들어졌다는 의미입니다. 마지막으로 TextureImage\[0\]->Data는 저희가 텍스쳐를 만드는데 사용했던 비트맵 데이터를 가르킵니다.

   

```cpp
       gluBuild2DMipmaps(GL_TEXTURE_2D, 3, TextureImage[0]->sizeX, TextureImage[0]->sizeY, GL_RGB, GL_UNSIGNED_BYTE, TextureImage[0]->data); // ( 새코드 )
   }
```

이제 비트맵 데이터를 저장하기 위해 사용했었던 메모리 공간을 해제합니다. TextureImage\[0\]에 텍스처가 저장되어 있는지를 검사합니다. 만약 그렇다면 텍스처 이미지가 저장되어 있는지 검사합니다. 만약 그렇다면 이를 지웁니다. 그리고 이미지 구조체를 해제하여 사용중인 메모리를 모두 해제시킵니다.  
   

```cpp
   if (TextureImage[0])                            // 텍스처가 존재하는지 확인
   {
       if (TextureImage[0]->data)                    // 텍스처 이미지가 존재하는지 확인
       {
           free(TextureImage[0]->data);                // 텍스처 이미지 메모리를 해제함
       }

       free(TextureImage[0]);                        // 이미지 구조체를 해제함
   }
```

마지막으로 할 일은 상태를 반환하는 것입니다. 만약 모든 일이 제대로 되엇다면 Status 변수가 TRUE가 될 것입니다. 만약 뭔가 잘못되었다면 Status는 FALSE가 될 것입니다.

```cpp
   return Status;                                // 상태를 반환한다
}
```

이제 텍스처를 로딩했으니 OpenGL 세팅을 초기화 해봅시다. InitGL의 첫번째 줄은 위의 코드를 이용하여 텍스쳐들을 읽어옵니다. 텍스처를 생성한 다음에는 glEnable(GL\_TEXTURE\_2D)를 이용하여 2D 텍스처 매핑을 활성화시킵니다. 쉐이드 모드는 부드러운 세이딩(smooth shading)으로 설정하며, 배경색은 검정으로 설정합니다. 깊이 테스팅도 활성화 시키고 멋드러진 원근 계산도 활성화 시킵니다.

```cpp
int InitGL(GLvoid)                                // 모든 OpenGL 설정을 여기서 함
{
   if (!LoadGLTextures())                            // 텍스처 로딩 루틴으로 점프
   {
       return FALSE;                            // 텍스처를 로딩하지 못했다면 FALSE를 반환
   }

   glEnable(GL_TEXTURE_2D);                        // 텍스처 매핑을 활성화
   glShadeModel(GL_SMOOTH);                        // 부드러운 셰이딩을 활성화
   glClearColor(0.0f, 0.0f, 0.0f, 0.5f);                    // 검정 배경색
   glClearDepth(1.0f);                            // 깊이버퍼 설정
   glEnable(GL_DEPTH_TEST);                        // 깊이테스트 설정
   glDepthFunc(GL_LEQUAL);                            // 깊이테스트의 종류
   glHint(GL_PERSPECTIVE_CORRECTION_HINT, GL_NICEST);            // 매우 멋드러진 원근계산
```

이제 조명을 설치합니다. 아래의 코드는 light1이 주는 주변광의 양을 설정합니다. 이 강좌의 시작부분에서 LightAmbient에 주변광의 양을 저장해놨던거 기억하시나요? 이 배열에 저장해놨던 값을 사용할 것입니다(중간 정도 세기의 주변광이 되겠습니다).  
   
   

```cpp
   glLightfv(GL_LIGHT1, GL_AMBIENT, LightAmbient);                // 분산광을 설정
```

다음은 조명 번호 1번이 내뿜는 산란광의 양을 설정할 차례입니다. LightDiffuse에 미리 산란광의 양을 저장해놨었습니다. 이 배열에 저장해놓은 값을 사용할 것입니다(최고로 강한 흰색 빛 입니다).

 

```cpp
   glLightfv(GL_LIGHT1, GL_DIFFUSE, LightDiffuse);                // 산란광을 설정
```

이제 빛의 위치를 설정합니다. LightPosition에 이 위치를 저장해놓았습니다. 이 배열에 저장해놓은 값을 사용할 것입니다(앞면의 중앙으로부터 오른쪽에 위치합니다. 좌표로 말하면 x = 0.0f, y = 0.0f, z = 관찰자 쪽으로 2 유닛입니다. 즉 z 평면상에서 화면 앞쪽으로 튀어나온 것이지요).

```cpp
   glLightfv(GL_LIGHT1, GL_POSITION,LightPosition);            // 조명을 위치시킨다
```

이제 마지막으로 조명 번호 1번을 활성화 시킵니다. 우리는 GL\_LIGHTING을 아직 활성화를 하지않았기 떄문에 아직 어떤 조명도 보이지 않을 것입니다. 조명의 설정 및 배치를 끝냈으며 이것을 활성화 시키기도 했지만 GL\_LIGHTING을 활성화시키기 전까진 그 조명이 동작하지 않을 것입니다.

```cpp
   glEnable(GL_LIGHT1);                            // 조명 1번을 활성화 시킨다
   return TRUE;                                // 초기화가 잘 끝났음
}
```

다음에 나오는 코드에서는 텍스처 매핑을 입힌 육면체를 그릴 것입니다. 새롭게 추가된 라인들에만 주석을 달겠습니다. 주석이 달리지 않은 코드들이 모를 의미하는건지 잘 모르겠다면 제6강을 살펴봐주세요.

```cpp
int DrawGLScene(GLvoid)                                // 여기가 모든 드로잉을 하는 곳이다
{
   glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);            // 장면과 깊이 버펄르 지움
   glLoadIdentity();                            // 뷰를 리셋함
```

다음의 세 라인의 코드는 텍스처 매핑을 입힌 육면체를 위치 및 회전시킵니다. glTranslatef(0.0f, 0.0f, z)는 육면체를 z평면상에서 z의 값만큼 이동시킵니다(관찰자쪽 또는 화면안쪽으로). glRotatef(xrot, 1.0f, 0.0f, 0.0f)는 xrot변수를 사용하여 육면체를 x축 상에서 회전시킵니다. glRotatef(yrot, 0.0f, 1.0f, 0.0f)는 yrot변수를 이용하여 y축 상에서 육면체를 회전시킵니다.

```cpp
   glTranslatef(0.0f,0.0f,z);                        // 화면 안쪽 또는 바깥쪽으로 Z만큼 평행이동

   glRotatef(xrot,1.0f,0.0f,0.0f);                        // X축상에서 xrot만큼 회전
   glRotatef(yrot,0.0f,1.0f,0.0f);                        // Y축상에서 yrot만큼 회전
```

다음 라인은 제6강에서 사용했던 라인과 비슷하지만 텍스처\[0\]을 바인딩 하는대신 texture\[filter\]를 바인딩 합니다. 저희가 'F'키를 누를때마다 filter에 저장된 값이 증가할 것입니다. 만약 이 값이 2보다 크면 filter변수가 다시 0으로 재설정됩니다. 프로그램이 시작될 때도 filter는 0일 것입니다. 즉, 처음에는 glBindTexture(GL\_TEXTURE\_2D, texture\[0\])과 같을 것이고, 'F'를 한번 누르면 filter변수가 1이되므로 glBindTexture(GL\_TEXTURE\_2D, texture\[1\])과 같을 것입니다. filter변수를 사용함으로 해서 저희가 만들었던 세개의 텍스처중 아무거나 임의로 골라서 사용할 수 있습니다.

```cpp
   glBindTexture(GL_TEXTURE_2D, texture[filter]);                // filter값에 따라 텍스처를 선택

   glBegin(GL_QUADS);                            // 쿼드를 그리기 시작함
```

glNormal3f가 등장하는 것은 처음이죠? 법선(normal)이란 한 폴리곤의 중앙에서 90도 각도로 위를 가르키는 직선입니다. 조명을 사용할 때는 법선을 지정해줘야 합니다. 법선은 OpenGL에게 폴리곤이 어떤 방향을 향하고 있는지, 즉 어느 쪽이 위인지를 알려줍니다. 법선을 지정해주지 않으면 별 기괴한 일들이 일어납니다. 조명을 받지 말아야 하는 면이 조명을 받고, 폴리곤의 잘못된 면이 빛을 받는 등 이상한 일이 일어납니다. 법선은 폴리곤에서 바깥쪽을 가르켜야 합니다.

앞면을 보면 법선이 z축 상의 양수라는 것을 알아채실 수 있으실 것입니다. 이것은 법선이 관찰자를 가르킨다는 말입니다. 이것이 바로 우리가 가르키려고 하는 방향입니다. 뒷면을 보면 법선이 관찰자로부터 반대방향으로, 즉 화면속으로 향합니다. 다시한번 이것은 저희가 바라는 그대로입니다. 만약 이 육면체를 x나 y축 상에서 180도 회전시키면 앞면이 화면안쪽을 향할 것이고 뒷면이 관찰자를 향할 것입니다. 어떤 면이 관찰자를 향하던 간에 그 면의 법선은 언제나 관찰자를 향하게 됩니다. 조명이 관찰자의 근처에 있기 때문에 법선이 관찰자를 향할 때에는 언제나 이것이 조명을 향하게 됩니다. 이런 일이 일어나면 그 면은 빛을 받을 것입니다. 법선이 조명에 가까이 갈 수록 그 면이 더욱 밝아집니다. 이 육면체의 중심쪽으로 들어가보시면 그 속이 어둡다는 사실을 밝견하실 겁니다. 법선이 밝을 향하지 않고 안으로 들어가기 때문입니다. 따라서 상자속에는 조명이 없습니다. 자, 저희가 바라는 대로죠?

   
 

```cpp
      // 앞면
       glNormal3f( 0.0f, 0.0f, 1.0f);                    // 관찰자 쪽으로 향하는 법선
       glTexCoord2f(0.0f, 0.0f); glVertex3f(-1.0f, -1.0f,  1.0f);    // 점 1 (앞면)
       glTexCoord2f(1.0f, 0.0f); glVertex3f( 1.0f, -1.0f,  1.0f);    // 점 2 (앞면)
       glTexCoord2f(1.0f, 1.0f); glVertex3f( 1.0f,  1.0f,  1.0f);    // 점 3 (앞면)
       glTexCoord2f(0.0f, 1.0f); glVertex3f(-1.0f,  1.0f,  1.0f);    // 점 4 (앞면)
       // 뒷면
       glNormal3f( 0.0f, 0.0f,-1.0f);                    // 관찰자 반대쪽으로 향하는 법선
       glTexCoord2f(1.0f, 0.0f); glVertex3f(-1.0f, -1.0f, -1.0f);    // 점 1 (뒷면)
       glTexCoord2f(1.0f, 1.0f); glVertex3f(-1.0f,  1.0f, -1.0f);    // 점 2 (뒷면)
       glTexCoord2f(0.0f, 1.0f); glVertex3f( 1.0f,  1.0f, -1.0f);    // 점 3 (뒷면)
       glTexCoord2f(0.0f, 0.0f); glVertex3f( 1.0f, -1.0f, -1.0f);    // 점 4 (뒷면)
       // 윗면
       glNormal3f( 0.0f, 1.0f, 0.0f);                    // 위를 향하는 법선
       glTexCoord2f(0.0f, 1.0f); glVertex3f(-1.0f,  1.0f, -1.0f);    // 점 1 (윗면)
       glTexCoord2f(0.0f, 0.0f); glVertex3f(-1.0f,  1.0f,  1.0f);    // 점 2 (윗면)
       glTexCoord2f(1.0f, 0.0f); glVertex3f( 1.0f,  1.0f,  1.0f);    // 점 3 (윗면)
       glTexCoord2f(1.0f, 1.0f); glVertex3f( 1.0f,  1.0f, -1.0f);    // 점 4 (윗면)
       // 아랫면
       glNormal3f( 0.0f,-1.0f, 0.0f);                    // 아래를 향하는 법선
       glTexCoord2f(1.0f, 1.0f); glVertex3f(-1.0f, -1.0f, -1.0f);    // 점 1 (아랫면)
       glTexCoord2f(0.0f, 1.0f); glVertex3f( 1.0f, -1.0f, -1.0f);    // 점 2 (아랫면)
       glTexCoord2f(0.0f, 0.0f); glVertex3f( 1.0f, -1.0f,  1.0f);    // 점 3 (아랫면)
       glTexCoord2f(1.0f, 0.0f); glVertex3f(-1.0f, -1.0f,  1.0f);    // 점 4 (아랫면)
       // 오른면
       glNormal3f( 1.0f, 0.0f, 0.0f);                    // 오른쪽을 향하는 법선
       glTexCoord2f(1.0f, 0.0f); glVertex3f( 1.0f, -1.0f, -1.0f);    // 점 1 (오른면)
       glTexCoord2f(1.0f, 1.0f); glVertex3f( 1.0f,  1.0f, -1.0f);    // 점 2 (오른면)
       glTexCoord2f(0.0f, 1.0f); glVertex3f( 1.0f,  1.0f,  1.0f);    // 점 3 (오른면)
       glTexCoord2f(0.0f, 0.0f); glVertex3f( 1.0f, -1.0f,  1.0f);    // 점 4 (오른면)
       // 왼쪽면
       glNormal3f(-1.0f, 0.0f, 0.0f);                    // 왼쪽을 향하는 법선
       glTexCoord2f(0.0f, 0.0f); glVertex3f(-1.0f, -1.0f, -1.0f);    // 점 1 (왼쪽면)
       glTexCoord2f(1.0f, 0.0f); glVertex3f(-1.0f, -1.0f,  1.0f);    // 점 2 (왼쪽면)
       glTexCoord2f(1.0f, 1.0f); glVertex3f(-1.0f,  1.0f,  1.0f);    // 점 3 (왼쪽면)
       glTexCoord2f(0.0f, 1.0f); glVertex3f(-1.0f,  1.0f, -1.0f);    // 점 4 (왼쪽면)
   glEnd();                                // 사각형들을 모두 다 그렸음
```

다음의 두 라인은 xrot과 yrot을 xspeed와 yspeed에 저장된 값 만큼 증가시킵니다. xspeed나 yspeed안에 있는 값이 높다면 xrot과 yrot이 빠르게 증가할 것입니다. xrot이나 yrot이 빠르게 증가할수록 육면체가 해당 축상에서 빠르게 움직입니다.

   

```cpp
   xrot+=xspeed;                                // xspeed를 xrot에 더함
   yrot+=yspeed;                                // yspeed를 yrot에 더함
   return TRUE;                                // 계속 진행할 것
}
```

이제 WinMain()을 살펴봅시다. 여기에 조명을 켜고 끄고, 상자를 회전 및 이동하며, 필터를 변경하는 코드를 추가할 것입니다. WinMain()의 마지막 부분에 SwapBuffer(hDC) 명령어가 있을 것입니다. 여기 바로 아래에 다음의 코드를 추가합니다.

이 코드는 키보드의 'L'키가 눌렸는지를 검사합니다. 처음 라인은 'L'키가 눌렸는지를 검사합니다. 'L'키가 눌렸으나 lp가 false가 아니라면 'L'이 이미 한번 눌렸거나 계속해 눌려져 있는 경우이므로 아무일도 일어나지 않습니다.

   

```cpp
               SwapBuffers(hDC);                // 버퍼를 스왑한다(이중 버퍼링)
               if (keys['L'] && !lp)                // L키가 처음 눌렸는가?
               {
```

만약 lp가 false라면 'L'키가 이번에 처음 눌린 것입니다. 만약 이 키를 더이상 누르지 않으면 lp가 true가 됩니다. 이는 'L'키를 한번 누를 때 이 코드가 여러번 실행되는 것을 막기 위한 것입니다. 키가 계속 눌려있는지를 검사하지 않는다면 프로그램은 이 코드를 실행할 때마다 사용자가 계속 'L'키를 누른다고 생각할 것이므로 조명이 계속적으로 깜박이게 될 것입니다.

일단 lp를 true로 만들어 컴퓨터가 'L'키가 계속 눌러져 있다는 사실을 알게 만들었다면 이제 조명을 끄고 켭니다. light 변수는 오직 true나 false가 될 수 있습니다. 따라서 light=!light라고 하면 "light는 light가 아닌 것과 같다"라는 뜻입니다. 따라서 light가 true라면 light를 true가 아니게(즉 false로) 만들고, light가 false라면 light를 false가 아니게(즉 true로) 만듭니다. 다시 간단히 말해 light가 true였다면 false가 되고 false였다면 true가 됩니다.

```cpp
                   lp=TRUE;                // lp가 TRUE가 됨
                   light=!light;                // 빛을 TRUE/FALSE로 토글한다
```

이제 조명이 존재하는지 검사합니다. 첫번째 줄을 한국말로 번역하면 "만약(if) light가 거짓(false)과 같다면" 입니다. 따라서 이 모두를 다 설명하면 "만약 light가 거짓과 같다면 조명을 끈다"가 됩니다. 이것은 모든 조명을 다 끕니다. 'else' 명령어는 "만약 이것이 거짓(false)가 아니었다면"으로 설명할 수 있습니다. 따라서 light가 false가 아니었다면 true임이 분명하므로 조명을 켭니다.

```cpp
                   if (!light)                // 조명을 꺼야 한다면
                   {
                       glDisable(GL_LIGHTING);        // 조명을 비활성화 시키고
                   }
                   else                    // 그렇지 않다면
                   {
                       glEnable(GL_LIGHTING);        // 조명을 활성화 시킨다.
                   }
               }
```

다음 라인은 'L'키 누르기를 멈췄는지를 검사합니다. 만약 그랬다면 lp 변수를 false로 만듭니다('L'키가 더이상 안눌린다는 뜻). 이 키를 더이상 누르고 있지 않은지를 검사하지 않는다면 조명을 한 번 밖에 켤 수 없을 것이며 컴퓨터는 언제나 'L'키가 눌린다고 생각하므로 다시 조명을 끌 수가 없을 것입니다.

```cpp
               if (!keys['L'])                    // L키가 더이상 눌리지 않는다면
               {
                   lp=FALSE;                // lp가 FALSE로 된다
               }
```

이제 'F'키에 비슷한 일을 합니다. 만약 이 키가 눌렸고 그전에 눌리고 있던 자국이 없다면 이번에 처음 눌리는 것입니다. 따라서 fp변수가 true가 되어 이 키가 이제 눌리고 있다는 사실을 나타냅니다. 그리고 이제 filter라 불리는 변수의 값을 증가시킵니다. 만약 filter의 값이 2보다 크다면(texture\[3\]가 되겠고, 이 텍스처는 존재하지 않습니다) filter변수를 0으로 다시 리셋합니다.

```cpp
               if (keys['F'] && !fp)                // F키가 눌렸다면
               {
                   fp=TRUE;                // fp가 TRUE로 되고
                   filter+=1;                // filter값을 하나 증가시킨다.
                   if (filter>2)                // 만약 이 값이 2 보다 크다면
                   {
                       filter=0;            // fitler를 0으로 만든다
                   }
               }
               if (!keys['F'])                    // F키가 더이상 눌리지 않는다면
               {
                   fp=FALSE;                // fp가 FALSE로 된다
               }
```

다음의 네 줄은 'Page Up'키를 누르고 있는지를 검사하는 라인입니다. 만약 그렇다면 z 변수의 값을 감소시킵니다. 이 변수값이 감소하면 화면에 있는 육면체가 멀리 이동할 것입니다. DrawGLScene 프로시저 안에서 사용한 glTranslatef(0.0f, 0.0f, z) 명령 때문이지요.

```cpp
               if (keys[VK_PRIOR])                // PageUp이 눌렸다면
               {
                   z-=0.02f;                // 화면 안쪽으로 이동한다
               }
```

이 네 줄은 'Page Down'키룰 누르고 있는지를 검사합니다. 만약 그렇다면 z 변수값을 증가시켜 육면체를 관찰자 쪽으로 이동시킵니다. 역시 DrawGLScene 프로시저안에서 사용한 glTranslatef(0.0f, 0.0f, z) 명령어 때문입니다.

```cpp
               if (keys[VK_NEXT])                // PageDown이 눌렸다면
               {
                   z+=0.02f;                // 관찰자를 향해 이동한다
               }
```

이제 저희가 검사해야 할 것은 방향키 밖에 없습니다. 왼쪽이나 오른쪽을 누르면 xspeed가 증가되거나 감소됩니다. 위쪽이나 아래쪽은 yspeed를 증가 및 감소시킵니다. 이 강좌의 저 위편에서 xspeed의 값이나 yspeed의 값이 크면 육면체가 빨리 회전할 것이라고 말씀드렸던거 기억하시나요? 화살표키를 오래 누르고 있으면 있을수록 해당 방향으로 육면체거 더 빨리 회전합니다.

```cpp
               if (keys[VK_UP])                // 위쪽 화살표키가 눌렸다면
               {
                   xspeed-=0.01f;                // xspeed를 감소시킨다
               }
               if (keys[VK_DOWN])                // 아래쪽 화살표키가 눌렸다면
               {
                   xspeed+=0.01f;                // xspeed를 증가시킨다
               }
               if (keys[VK_RIGHT])                // 오른쪽 화살표키가 눌렸다면
               {
                   yspeed+=0.01f;                // yspeed를 증가시킨다
               }
               if (keys[VK_LEFT])                // 왼쪽 화살표키가 눌렸다면
               {
                   yspeed-=0.01f;                // yspeed를 감소시킨다
               }
```

이전에 다뤘던 모든 강좌와 마찬가지로 창위 위쪽에 있는 타이틀을 제대로 나오게 합니다.

```cpp
 
               if (keys[VK_F1])                // F1키가 눌린다면
               {
                   keys[VK_F1]=FALSE;            // 이 키를 FALSE로 만들고
                   KillGLWindow();                // 현재 창을 죽인다
                   fullscreen=!fullscreen;            // 전체화면/창 모드를 토글한다
                   // OpenGL 창을 다시 만든다
                   if (!CreateGLWindow("NeHe의 텍스처, 조명, 키보드 튜토리얼",640,480,16,fullscreen))
                   {
                       return 0;            // 창을 만드는데 실패했으면 종료한다
                   }
               }
           }
       }
   }

   // 종료부분
   KillGLWindow();                                // 창을 죽인다
   return (msg.wParam);                            // 프로그램에서 탈출
}
```

이 강좌를 여기까지 정독하신 독자분들이시라면 쿼드로 구성된 고품질, 사실적인 텍스처 매핑된 물체들을 만들고 움직일 수 있을 것입니다. 키보드 상에 있는 특정 키를 누름으로써 화면상에 있는 물체들과 상호작용할 수 있으며, 마지막으로 장면에 간단한 조명을 적용하여 보다 사실적인 장면을 표현하는 법을 아시고 계실 것입니다.

## 소스코드 다운로드 {#TOC--2}

원문 페이지에서 소스코드를 다운로드 받으실 수 있습니다.

## 원문 정보 {#TOC--3}

*   저자: Jeff Molofee (NeHe)
*   원문보기: [Lesson 07](https://nehe.gamedev.net/tutorial/texture_filters%2C_lighting_%26_keyboard_control/15002/)
