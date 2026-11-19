---
title: "06. 텍스처 매핑"
date: 2026-11-19
---

## 소개 {#TOC--}

[![06\. 텍스처 매핑 실행 화면](/ko/galexandria/nehe-opengl-tutorial/images/lesson06.jpg)](/ko/galexandria/nehe-opengl-tutorial/images/lesson06.jpg)

여러분의 열화와 같은 성원에 힘입어 이번에는 텍스처 매핑입니다! 이 튜토리얼에서는 비트맵 이미지를 육면체의 여섯면에 매핑하는 법을 다룰 것입니다. 저희는 첫번째 강좌의 GL 코드를 사용하여 이 프로젝트를 만들 것입니다. 전 강좌의 코드를 수정하는것보다 빈 GL창에서 시작하는게 더 쉽기 때문입니다.

신속하게 프로젝트를 개발하려고 할 때 강좌 1의 코드가 매우 유용할 것입니다. 강좌1의 코드는 여러분에게 필요한 모든 것들을 설정하고 있으며, 여러분이 해야할 일은 특수효과 프로그래밍에 집중하는 것 뿐입니다.

* * *

## 본문 {#TOC--1}

텍스처 매핑은 용도가 다양합니다. 미사일이 화면을 가로질러 날라가게 하고 싶다고 생각해봅시다. 저희가 현재 알고 있는 방법은 전체 미사일을 폴리곤들로 만들고 그럴듯한 색상을 입히는 것 입니다. 텍스처 매핑을 사용하면 실제 미사일 사진을 가져와서 그 그림이 화면을 가로질러 날라가게 만들수 있습니다. 실제 사진과 삼각형/사각형으로 구성된 물체중에 어떤 방법이 더 그럴듯 해 보일지는 분명한 일이죠? 텍스처 매핑을 사용하면 보기에도 나을뿐만 아니라 프로그램도 더 빠르게 실행될 것입니다. 텍스처를 입힌 마사일은 화면을 가로지르는 사각형 하나가 될테니까요. 폴리곤으로 만든 미사일은 수백 또는 수천개의 폴리곤들을 사용할 것입니다. 텍스처 맵을 입힌 사각형 하나는 이보다 훨씬 적은 처리능력을 사용합니다.

자, 그럼 시작해볼까요? 1강의 코드 윗부분에 새 코드를 다섯줄 추가합니다. 첫번째 새로운 라인은 #include <stdio.h>입니다. 이 헤더파일을 추가하면 파일을 조작할 수 있습니다. 이 코드의 뒷부분에서 fopen()함수를 사용할 것인데 stdio.h를 포함해야만 fopen()함수를 사용할 수 있습니다. 이제 3개의 부동소수점 변수로 xrot, yrot, zrot을 추가합니다. 이 변수들은 x축, y축, z축 상에서 육면체를 회전시키기 위해 사용합니다. 마지막 라인은 GLuint texture\[1\]로 텍스처 한개의 저장공간을 준비합니다. 하나 이상의 텍스처를 로딩하려면 \[1\] 대신에 로딩하려는 텍스처의 수\[?\]를 적으시면 됩니다. 

```cpp
#include    <windows.h>                          // 윈도우즈용 헤더파일
#include    <stdio.h>                            // 표준 입출력용 헤더파일( 새코드 )
#include    <gl\gl.h>                            // OpenGL32 라이브러리용 헤더파일
#include    <gl\glu.h>                           // GLu32 라이브러리용 헤더파일
#include    <gl\glaux.h>                         // GLaux 라이브러리용 헤더파일

HDC         hDC=NULL;                            // GDI 컨텍스트
HGLRC       hRC=NULL;                            // 렌더링 컨텍스트
HWND        hWnd=NULL;                           // 우리의 윈도우 핸들을 기억
HINSTANCE   hInstance;                           // 응용프로그램의 인스턴스를 기억

bool        keys[256];                           // 키보드 루틴에 사용하는 배열
bool        active=TRUE;                         // 윈도우 활성여부 플래그
bool        fullscreen=TRUE;                     // 전체화면 플래그

GLfloat     xrot;                                // X 회전 ( 새 코드 )
GLfloat     yrot;                                // Y 회전 ( 새 코드 )
GLfloat     zrot;                                // Z 회전 ( 새 코드 )

GLuint      texture[1];                          // 텍스처 하나용 저장공간 ( 새코드 )

LRESULT     CALLBACK WndProc(HWND, UINT, WPARAM, LPARAM);                // WndProc 선언
```

이제 위의 코드블럭과 ReSizeGLScene() 사이에 다음의 코드블럭을 추가합니다. 이 코드가 하는일은 비트맵 파일을 로딩하는 것입니다. 만약 그 파일이 존재하지 않으면 NULL을 반환하여 텍스처를 읽을 수 없다고 알립니다. 코드를 설명드리기 전에 여러분이 **반드시** 알아두셔야할 것들이 있습니다. 이미지의 높이와 폭이 **반드시** 2의 승수여야 한다는 것입니다. 호환성 때문에 폭과 높이는 최소한 64픽셀은 되어야 하며 256픽셀이 넘어서는 안됩니다. 여러분이 사용하려고 하는 이미지의 폭과 높이가 64, 128 또는 256 픽셀이 아니라면 포토샵 같은 이미지 편집 프로그램에서 크기를 바꾸시기 바랍니다. 이 제한을 극복할 수 있는 꽁수가 있긴 하지만 일단은 표준 텍스처 크기만을 사용하도록 합시다.

첫번째로 하는 일은 파일 핸들을 만드는 것입니다. 핸들은 리소스를 식별하기 위한 값입니다. 이 값은 저희 프로그램이 리소스를 액세스할 때 필요로 합니다. 일단 핸들을 NULL로 놓고 시작합니다. 

```cpp
AUX_RGBImageRec *LoadBMP(char *Filename)                    // 비트맵 이미지를 읽어옴
{
   FILE *File=NULL;                            // 파일 핸들
```

다음은 파일이름이 실제로 주어졌는지를 확인합니다. 사용자가 로딩할 파일을 지정하지 않은채 LoadBMP()함수를 호출할 수도 있으므로 이를 반드시 검사해야합니다. 존재하지 않는 것을 로딩하고 싶진 않으시죠? :)  

```cpp
  if (!Filename)                                // 파일이름이 전달되었는지 확인
   {
       return NULL;                            // 그렇지 않다면 NULL을 반환
   }
```

파일이름이 주어졌다면 파일이 존재하는지를 검사합니다. 아래의 라인은 파일을 열려고 시도하는 코드입니다.    `File=fopen(Filename,"r");                        // 파일이 존재하는지 확인`파일을 열수 있었다면 그 파일은 분명히 존재하는 것입니다. fclose(File)로 파일을 닫은 뒤 이미지 데이터를 반환합니다. auxDIBImageLoad(Filename)은 이미지 데이터를 읽어들이는 함수입니다. 

```cpp
   if (File)                                // 파일이 존재하는가?
   {
       fclose(File);                            // 핸들을 닫음
       return auxDIBImageLoad(Filename);                // 비트맵을 읽어들이고 포인터를 반환
   }
```

파일을 열 수 없었다면 NULL을 반환합니다. NULL은 파일을 로딩할 수 없었다는 의미입니다. 이 프로그램의 뒷부분에서 여기서 반환한 값을 확인할 것입니다. 만약 NULL이 반환되면 -- 즉, 파일이 로딩되지 않았다면 -- 오류메시지와 함께 프로그램을 종료시킬 것입니다. 

```cpp
   return NULL;                                // 로딩이 실패하면 NULL을 반환
}
```

다음은 위의 코드를 호출하여 비트맵을 로딩하고 비트맵을 텍스처로 바꾸는 코드입니다 

```cpp
int LoadGLTextures()                                // 비트맵을 로드하고 텍스처로 변환
{
```

저희는 Status란 변수를 선언할 것입니다. 이 변수는 비트맵을 읽어들이고 텍스처를 만드는 작업이 성공했는지 실패했는지를 저장하는데 사용합니다. 디폴트 값을 FALSE로 설정하여 아직 아무것도 읽거나 만들지 않았다고 표시합니다.     `int Status=FALSE;                            // 상태 표시기`이제 비트맵을 저장할 수 있는 이미지 레코드를 만듭니다. 이 레코드는 비트맵의 폭과 높이 및 데이터를 저장합니다. 

```cpp
   AUX_RGBImageRec *TextureImage[1];                    // 텍스처용 저장공간을 만듬
```

이미지 레코드를 지워 그것이 확실히 비워있게 만듭니다.

   `memset(TextureImage,0,sizeof(void *)*1);                // 포인터를 NULL로 설정`이제 비트맵을 읽어들여 비트맵을 텍스처로 변환합니다. TextureImage\[0\] = LoadBMP("Data/NeHe.bmp")로 하면 저희의 LoadBMP() 코드로 이동합니다. Data 디렉터리에 있는 NeHe.bmp란 이름을 가진 파일이 로딩될 것입니다. 만약 모든것이 다 잘 진행되면 TextureImage\[0\]에 이미지 데이터가 저장되며, Status가 TRUE가 될것입니다. 이제 텍스처를 만들기 시작합니다. 

```cpp
   // 비트맵을 로딩하고 에러를 검사한다. 비트맵이 발견되지 않았다면 종료한다.
   if (TextureImage[0]=LoadBMP("Data/NeHe.bmp"))
   {
       Status=TRUE;                            // Status를 TRUE로 설정
```

이제 이미지 데이터를 TextureImage\[0\]에 읽어들였으니 이 데이터를 이용하여 텍스처를 만들 차례입니다. 첫번째 라인은 glGenTextures(1, &texture\[0\])으로 OpenGL에게 저희가 텍스처 이름 하나를 얻고 싶다고 말해줍니다(한 개 이상의 텍스처를 로딩할 떄는 이 숫자를 증가시켜주세요). 이 튜토리얼의 젤 앞부분에서 GLuint texture\[1\] 라인을 사용하여 텍스처 하나를 위한 공간을 만들었다는 사실을 기억하십시요. 첫번째 텍스처가 &texture\[0\]이 아니라 &texture\[1\]안에 저장될것이라고 생각하는 독자분들이 계실지도 모르겠지만 그건 안될 말이죠. C/C++의 배열 인덱스는 0부터 시작합니다. 따라서 첫번째 저장공간은 0이랍니다. 만약 저희가 2개의 텍스처를 원한다면 GLuint texture\[2\]를 사용하고 두번째 텍스처는 texture\[1\]안에 저장될 것입니다.

두번째 라인은 glBindTexture(GL\_TEXTURE\_2D, texture\[0\])로 OpenGL에게 texture\[0\]를 텍스처 타겟에 연결시키라고 말해줍니다. 2D 텍스처는 높이(Y축)와 폭(X축)을 가지고 있습니다. glBindTexture의 주요 기능은 텍스처 이름을 텍스처 데이터에 할당하는 것입니다. 이 경우 저희는 &texture\[0\]에 사용가능한 메모리가 있다고 OpenGL에게 알려줍니다. 따라서 텍스처를 만들면 &texture\[0\]가 참조하는 메모리에 그 텍스처가 저장될 것입니다. 

```cpp
       glGenTextures(1, &texture[0]);                    // 텍스처를 만듬

       // 비트맵으로부터 가져온 데이터를 사용한 일반적인 텍스처 생성
       glBindTexture(GL_TEXTURE_2D, texture[0]);
```

이제 실제 텍스처를 만듭니다. 다음 라인은 OpenGL에게 텍스처가 2D 텍스처(GL\_TEXTURE\_2D)가 될것이라고 알려줍니다. 0은 이미지의 디테일정도를 나타내며 0로 주는게 보통입니다. 3은 데이터 컴포넌트의 수입니다. 이 이미지가 빨강 데이터, 녹색 데이터, 파랑 데이터로 구성되어 있으므로 3개의 컴포넌트가 있는 것입니다. TextureImage\[0\]->sizeX는 텍스처의 폭입니다. 이 폭을 이미 알고 있다면 직접 숫자를 쳐 넣으셔도 되지만 컴퓨터가 알아서 찾아내게 만드는 것이 좀더 쉬운 방법입니다. TextureImage\[0\]->sizeY는 텍스처의 높이입니다. 0이 경계선입니다. 이것은 보통 0으로 남겨둡니다. GL\_RGB는 저희가 사용할 이미지 데이터가 빨강,녹색,파랑 데이터 순서로 구성되어 있음을 OpenGL에게 알려줍니다. GL\_UNSIGNED\_BYTE는 이미지를 구성하는 데이터가 unsigned byte라는 것입니다. 그리고 마지막으로 TextureImage\[0\]->data는 OpenGL에게 텍스처 데이터를 얻을 수 있는 위치를 알려줍니다. 저희의 경우 이것은 TextureImage\[0\] 레코드 안에 저장된 데이터를 가르키고 있습니다. 

```cpp
       // 텍스처를 만든다
       glTexImage2D(GL_TEXTURE_2D, 0, 3, TextureImage[0]->sizeX, TextureImage[0]->sizeY, 0, GL_RGB, GL_UNSIGNED_BYTE, TextureImage[0]->data);
```

다음의 두 라인은 이미지가 원래 텍스처의 크기보다 크거나(GL\_TEXTURE\_MAG\_FILTER) 작게(GL\_TEXTURE\_MIN\_FILTER) 뿌려질 때 OpenGL이 어떤 종류의 필터링을 사용할 것인지를 지정합니다. 저는 양쪽에 모두 GL\_LINEAR를 사용하는 것이 보통입니다. 이는 멀리 떨어지거나 화면에 가까이 있는 텍스처들을 부드럽게 보이게 해줍니다. GL\_LINEAR는 프로세서/비디오 카드의 처리시간을 많이 요구합니다. 따라서 여러분의 시스템이 느리다면 GL\_NEAREST를 사용하시는것이 좋을 수도 있습니다. GL\_NEAREST로 필터링된 텍스처를 잡아 늘리면 각이 져 보입니다. 두개를 조합해서 사용해 보실 수도 있습니다. 멀리 있는것보단 가까이에 있는 것들을 필터링 하는것이 더 보기 좋을 것입니다. 

```cpp
       glTexParameteri(GL_TEXTURE_2D,GL_TEXTURE_MIN_FILTER,GL_LINEAR);    // 선형 필터링
       glTexParameteri(GL_TEXTURE_2D,GL_TEXTURE_MAG_FILTER,GL_LINEAR);    // 선형 필터링
   }
```

이제 비트맵 데이터를 저장하기 위해 사용했던 램을 풀어줍니다. TextureImage\[0\]에 비트맵 데이터가 저장되었었는지를 검사합니다. 만약 그랬다면 데이터가 저장되었는지를 검사합니다. 데이터가 저장되었다면 데이터를 지웁니다. 그리고 이미지 구조체를 해제하여 사용했던 메모리들을 모두 풀어줍니다. 

```cpp
   if (TextureImage[0])                            // 텍스처가 존재하는지 확인
   {
       if (TextureImage[0]->data)                    // 텍스처 이미지가 존재하는지 확인
       {
           free(TextureImage[0]->data);                // 텍스처 이미지 메모리를 해제
       }

       free(TextureImage[0]);                        // 이미지 구조체를 해제
   }
```

마지막으로 상태를 반환합니다. 모든것이 잘 진행되었다면 Status는 TRUE가 될것입니다. 만약 하나라도 잘못된 것이 있다면 Status가 FALSE가 됩니다.

```cpp
   return Status;                                // Status를 반환
}
```

저는 InitGL에 몇줄의 코드를 추가했습니다. 따라서 이 코드전체를 여기에 다시 보여드리겠습니다. 이러는 것이 제가 추가한 코드의 위치를 알아보기가 편하겠죠? 첫번째 라인인 if (!LoadGLTextures())는 비트맵을 로딩하고 그로부터 텍스처를 만드는 위의 루틴으로 점프합니다. 만약 LoadGLTextures()가 실패한다면 다음 코드라인이 FALSE를 반환할 것입니다. 모든것이 제대로 진행되었다면 텍스처가 만들어진 것이고, 저희는 2D 텍스처 매핑을 활성화시킵니다. 깜박잊고 텍스처 매핑을 활성화시키지 않으면 여러분의 개체가 완전한 흰색으로 보일 것입니다.

```cpp
int InitGL(GLvoid)                                // 모든 OpenGL 설정을 여기서 함
{
   if (!LoadGLTextures())                            // 텍스처 로딩 루틴으로 점프함( 새코드 )
   {
       return FALSE;                            // 텍스처가 로딩되지 않았다면 FALSE를 반환 ( 새코드 )
   }

   glEnable(GL_TEXTURE_2D);                        // 텍스처 매핑을 활성화시킴 ( 새코드 )
   glShadeModel(GL_SMOOTH);                        // 부드러운 쉐이딩을 활성화시킴
   glClearColor(0.0f, 0.0f, 0.0f, 0.5f);                    // 검은색 배경
   glClearDepth(1.0f);                            // 깊이버퍼 설정
   glEnable(GL_DEPTH_TEST);                        // 깊이테스트를 켬
   glDepthFunc(GL_LEQUAL);                            // 깊이테스트 종류
   glHint(GL_PERSPECTIVE_CORRECTION_HINT, GL_NICEST);            // 정말로 멋진 원근 계산
   return TRUE;                                // 초기화 성공
}
```

이제 텍스처를 입힌 육면체를 그릴 차례입니다. DrawGLScene을 아래의 코드로 교체하시던가 새로 추가된 코드만 찾아서 1강의 코드에 추가하십시요. 이 부분에 주석을 자세히 달아놓았으니 이해하시기 쉬울 것입니다. 첫번째 두 라인의 코드는 glClear()와 glLoadIdentity()로 강좌 1의 코드에도 있는 부분입니다. glClear(GL\_COLOR\_BUFFER\_BIT | GL\_DEPTH\_BUFFER\_BIT)은 InitGL()에서 선택했던 색상으로 화면을 지웁니다. 저희의 경우에 화면은 검정색으로 지워질 것입니다. 깊이 버퍼도 또한 지워집니다. 그리고 glLoadIdentity()가 뷰를 리셋합니다. 

```cpp
int DrawGLScene(GLvoid)                                // 모든 드로잉을 처리하는 부분
{
   glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);            // 화면과 깊이버퍼를 지움
   glLoadIdentity();                            // 현재 행렬ㅇ르 리셋
   glTranslatef(0.0f,0.0f,-5.0f);                        // 화면안쪽으로 5단위만큼 이동
```

다음의 세 라인의 코드는 육면체를 x축->y축->z축 순으로 회전시킵니다. 각 축 주위로 회전시키는 양은 xrot, yrot, zrot 변수안에 저장된 값에 따라 다릅니다.

```cpp
   glRotatef(xrot,1.0f,0.0f,0.0f);                        // X축 주변으로 회전
   glRotatef(yrot,0.0f,1.0f,0.0f);                        // Y축 주변으로 회전
   glRotatef(zrot,0.0f,0.0f,1.0f);                        // Z축 주변으로 회전
```

다음 코드라인은 어떤 텍스처를 사용할 것인지를 선택합니다. 둘 이상의 텍스처를 장면에 사용하려면 glBindTexture(GL\_TEXTURE\_2D, texture\[사용하고자 하는 텍스처의 개수\])를 사용하여 텍스처를 선택해야할 것입니다. 텍스쳐를 변경하고자 한다면 새로운 텍스처에 바인딩해야 합니다. 참고로 한가지 알아두셔야 할 점은 glBegin()과 glEnd()사이에서는 텍스처를 바인딩 할 수 **없다**는 것입니다. glBegin() 전이나 glEnd()이후에 그것을 하셔야 합니다. 저희가 glBindTextures를 사용하여 어떤 텍스처를 만들고 특정 텍스처를 선택하는 방법을 잘 살펴봐주십시오. 

```cpp
   glBindTexture(GL_TEXTURE_2D, texture[0]);                // 우리의 텍스처를 선택함
```

텍스처를 쿼드 위에 적절히 입히려면 텍스처의 오른쪽 윗부분을 쿼드의 오른쪽 윗부분에 매핑해야 합니다. 텍스처의 왼쪽 위 역시 사각형의 왼쪽위에, 오른쪽 아래는 역시 오른쪽 아래에, 왼쪽 아래도 역시 왼쪽아래에 매핑합니다. 텍스쳐의 코너가 쿼드의 동일한 코너에 대응되지 않으면 이미지의 위아래가 뒤집히거나 좌우가 바뀌거나... 뭐 그런식이 될 것입니다.

glTexCoord2f의 첫번째 값은 X좌표입니다. 0.0f는 텍스처의 왼쪽입니다. 0.5f는 텍스처의 중앙입니다. 그리고 1.0f는 텍스처의 오른쪽입니다. glTexCoord2f의 두번째 값은 Y 좌표입니다. 0.0f는 텍스처의 아랫쪽입니다. 0.5f는 텍스처의 중앙입니다. 1.0f는 텍스처의 꼭대기입니다.

자, 이제 텍스처의 왼쪽 꼭대기가 X = 0.0f, Y = 0.0f이고, 쿼드의 왼쪽 꼭대기 정점이 X = -1.0f, Y = 1.0f인것을 알고 있습니다. 이제 여러분이 해야하는 일은 다른 세 텍스처 정점들을 쿼드의 나머지 세 코너에 매치시키는 것 뿐입니다.

glTexCoord2f의 x와 y값을 가지고 놀아보십시요. 1.0f를 0,5f로 바꾸면 텍스처의 왼쪽 반쪽만이 그려질 것입니다. 즉 0.0f(텍스처의 왼쪽) 부터 0.5f(텍스처의 중앙)까지이죠. 0.0f를 0.5f로 바꾸면 텍스처의 오른쪽 반쪽만이 그려질 것입니다. 0.5f(텍스처의 중앙)부터 1.0(텍스처의 오른쪽)까지입니다.

```cpp
   glBegin(GL_QUADS);
       // 앞면
       glTexCoord2f(0.0f, 0.0f); glVertex3f(-1.0f, -1.0f,  1.0f);    // 텍스처와 쿼드의 왼쪽아래
       glTexCoord2f(1.0f, 0.0f); glVertex3f( 1.0f, -1.0f,  1.0f);    // 텍스처와 쿼드의 오른쪽아래
       glTexCoord2f(1.0f, 1.0f); glVertex3f( 1.0f,  1.0f,  1.0f);    // 텍스처와 쿼드의 오른쪽위
       glTexCoord2f(0.0f, 1.0f); glVertex3f(-1.0f,  1.0f,  1.0f);    // 텍스처와 쿼드의 왼쪽위
       // 뒷면
       glTexCoord2f(1.0f, 0.0f); glVertex3f(-1.0f, -1.0f, -1.0f);    // 텍스처와 쿼드의 오른쪽아래
       glTexCoord2f(1.0f, 1.0f); glVertex3f(-1.0f,  1.0f, -1.0f);    // 텍스처와 쿼드의 오른쪽위
       glTexCoord2f(0.0f, 1.0f); glVertex3f( 1.0f,  1.0f, -1.0f);    // 텍스처와 쿼드의 왼쪽위
       glTexCoord2f(0.0f, 0.0f); glVertex3f( 1.0f, -1.0f, -1.0f);    // 텍스처와 쿼드의 왼쪽아래
       // 윗면
       glTexCoord2f(0.0f, 1.0f); glVertex3f(-1.0f,  1.0f, -1.0f);    // 텍스처와 쿼드의 왼쪽위
       glTexCoord2f(0.0f, 0.0f); glVertex3f(-1.0f,  1.0f,  1.0f);    // 텍스처와 쿼드의 왼쪽아래
       glTexCoord2f(1.0f, 0.0f); glVertex3f( 1.0f,  1.0f,  1.0f);    // 텍스처와 쿼드의 오른쪽아래
       glTexCoord2f(1.0f, 1.0f); glVertex3f( 1.0f,  1.0f, -1.0f);    // 텍스처와 쿼드의 오른쪽위
       // 아랫면
       glTexCoord2f(1.0f, 1.0f); glVertex3f(-1.0f, -1.0f, -1.0f);    // 텍스처와 쿼드의 오른쪽위
       glTexCoord2f(0.0f, 1.0f); glVertex3f( 1.0f, -1.0f, -1.0f);    // 텍스처와 쿼드의 왼쪽위
       glTexCoord2f(0.0f, 0.0f); glVertex3f( 1.0f, -1.0f,  1.0f);    // 텍스처와 쿼드의 왼쪽아래
       glTexCoord2f(1.0f, 0.0f); glVertex3f(-1.0f, -1.0f,  1.0f);    // 텍스처와 쿼드의 오른쪽아래
       // 오른면
       glTexCoord2f(1.0f, 0.0f); glVertex3f( 1.0f, -1.0f, -1.0f);    // 텍스처와 쿼드의 오른쪽아래
       glTexCoord2f(1.0f, 1.0f); glVertex3f( 1.0f,  1.0f, -1.0f);    // 텍스처와 쿼드의 오른쪽위
       glTexCoord2f(0.0f, 1.0f); glVertex3f( 1.0f,  1.0f,  1.0f);    // 텍스처와 쿼드의 왼쪽위
       glTexCoord2f(0.0f, 0.0f); glVertex3f( 1.0f, -1.0f,  1.0f);    // 텍스처와 쿼드의 왼쪽아래
       // 왼면
       glTexCoord2f(0.0f, 0.0f); glVertex3f(-1.0f, -1.0f, -1.0f);    // 텍스처와 쿼드의 왼쪽아래
       glTexCoord2f(1.0f, 0.0f); glVertex3f(-1.0f, -1.0f,  1.0f);    // 텍스처와 쿼드의 오른쪽아래
       glTexCoord2f(1.0f, 1.0f); glVertex3f(-1.0f,  1.0f,  1.0f);    // 텍스처와 쿼드의 오른쪽위
       glTexCoord2f(0.0f, 1.0f); glVertex3f(-1.0f,  1.0f, -1.0f);    // 텍스처와 쿼드의 왼쪽위
   glEnd();
```

이제 xrot, yrot, zrot의 값들을 증가시켜봅시다. 각 변수가 증가되는 양을 변경하여 육면체가 보다 빠르거나 보다 느리게 회전되도록 만들어 봅시다. 아니면 +를 -로 변경하여 다른 방향으로 회전시켜보는 것도 재밌겠네요. 

```cpp
   xrot+=0.3f;                                // X 축 회전
   yrot+=0.2f;                                // Y 축 회전
   zrot+=0.4f;                                // Z 축 회전
   return true;                               // 계속 진행
}
```

이제 여러분은 텍스처 매핑을 예전보다 잘 이해하고 계실 것입니다. 자신이 원하는 이미지를 쿼드의 표면에 텍스처 매핑하실 수 있으시죠? 2D 텍스처 매핑에 대해 자신감을 가지셨다면 육면체에 6개의 다른 텍스처를 추가해보세여.

일단 텍스처 좌표를 이해하면 텍스처 매핑은 더이상 이해하기 어려운 것이 아닙니다. 이 튜토리얼 중에 이해가 어려운 부분이 있다면 제게 알려주십시요. 그 부분을 다시 작성하거나 email로 답변을 드리겠습니다. 그럼, 텍스처 매핑으로 멋드러진 장면을 만들어보세요~ ^^

## 소스코드 다운로드 {#TOC--2}

원문 페이지에서 소스코드를 다운로드 받으실 수 있습니다.

## 원문 정보 {#TOC--3}

*   저자: Jeff Molofee (NeHe)
*   원문보기: [Lesson 06](https://nehe.gamedev.net/tutorial/texture_mapping/12038/)
