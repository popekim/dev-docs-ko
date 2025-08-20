---
title: "2. 첫 폴리곤 만들기"
date: 2025-08-20
---

## 소개

안녕하세요? 두번째 튜토리얼입니다. 이 튜토리얼에서 우리가 할 일은 첫번째 튜토리얼의 소스코드에 코드를 추가해서 화면에 삼각형과 사각형을 그리는 것입니다. 물론 각각 하나씩입니다. "삼각형과 사각형? 그게 뭐야\!"라고 생각하실 독자분들이 계시다는 걸 압니다. 하지만 진정하세요. 이것은 정말 중요한 단계입니다. OpenGL에서 만드는 모든 것은 삼각형과 사각형들을 구성되어 있습니다. 3차원 세계에서 조그만 삼각형 하나를 만드는 법을 이해하지 못한다면 이후의 튜토리얼을 이해하기는 더욱 힘들 것입니다. 따라서 이 튜토리얼을 찬찬히 읽으면서 배워보십시오.

이 튜토리얼을 다 읽으신 후에는 X축, Y축, Z축의 개념을 이해하실 수 있을 것입니다. 또한 화면의 안쪽과 바깥쪽,상하좌우로 물체를 이동시키는 방법에 대해서도 배울 것입니다. 이 외에도 화면상의 원하는 위치에 정확히 물체를 위치시키는 법도 이해하실 것이며, 깊이 버퍼(화면안에 물체 놓이기)에 대해서도 약간의 지식을 쌓게 될 것입니다.

## 본문

첫번째 튜토리얼에서 배워봤던 내용은 OpenGL 윈도우를 만드는 법이었습니다. 이 튜토리얼의 내용은 삼각형 및 사각형을 만드는 법입니다. 삼각형을 만들 때는 GL\_TRIANGLES를, 사각형은 GL\_QUADS를 사용합니다.

첫번째 튜토리얼의 코드에 있는 DrawGLScene() 함수에 몇 줄을 더해봅시다. 여기 새로운 코드가 추가된 DrawGLScene() 함수의 전체코드를 보입니다. 지난 강좌의 코드를 수정하려고 하신다면 아래의 DrawGLScene() 함수 전체를 아래의 코드로 대체하시거나 새로 추가된 줄을 찾아서 더해 주십시요.

```cpp
int DrawGLScene(GLvoid)                    	// 모든 드로잉을 처리하는 함수
{
    glClear(GL_COLOR_BUFFER_BIT | GL_DEPTH_BUFFER_BIT);   // 화면과 깊이 버퍼를 비운다  
    glLoadIdentity();                                     // 뷰를 리셋한다
```

여러분이 glLoadIdentity() 함수를 호출했다면 이것은 화면의 중심을 기준으로 화면의 좌우로 움직이는 X축, 상하로 움직이는 Y축,화면의 안쪽과 바깥쪽으로 움직이는 Z축을 가지는 최초의 상태로 되돌리는 역할을 해줍니다.

OpenGL 화면의 중점은 X \= 0.0f, Y \= 0.0f 입니다. 중점으로부터 왼쪽은 음수이며, 오른쪽은 양수입니다. 마찬가지로 화면 위쪽으로의 이동은 양수이고, 아랫쪽으로의 이동은 음수입니다. Z축의 경우 화면 깊숙이 이동하는 것은 음수, 여러분 쪽으로 이동하는 것은 양수입니다.

glTranslatef(x, y, z)는 순서에 의해 X, Y, Z축을 따라서 x, y, z 단위만큼 이동합니다. 아래의 코드는 X축 왼쪽으로 1.5 단위만큼 이동합니다. Y축은 전혀 이동하지 않으며(0.0) 화면 안쪽으로 6.0 단위만큼 이동합니다. 이동의 경우 화면의 중앙에서부터 주어진 수치만큼 이동을 하는 것이 아니라 화면상의 현재 위치로부터 주어진 수치만큼 이동합니다.  

```cpp
    glTranslatef(-1.5f,0.0f,-6.0f);    	// 왼쪽으로 1.5, 화면안쪽으로 6.0 단위만큼 이동한다.
```

이제 우리는 화면의 좌측으로 \-1.5 만큼 이동하였고 뷰를 화면 안쪽으로 충분히 깊이(-6, 0\) 이동시켰으므로 곧 생성할 삼각형의 전체장면을 볼 수 있습니다. glBegin(GL\_TRIANGLES)는 삼각형을 그리기 시작할 것이라는 사실을 OpenGL에게 알려주며, glEnd()는 삼각형을 다 만들었다는 사실을 OpenGL에게 알려줍니다. 3개의 점을 사용할 것이라면 GL\_TRIANGLES를 사용하십시오. 대부분의 비디오 카드는 매우 빨리 삼각형을 그릴 수 있습니다. 4개의 점을 사용할 것이라면 GL\_QUADS를 사용하십시오. 하지만 제가 들은 바에 의하면 대부분의 비디오 카드는 이런 경우에도 삼각형을 사용해 물체를 렌더링한다고 합니다. 4개 이상의 점을 사용할 것이라면 GL\_POLYGON을 사용하십시오.

이 강좌의 프로그램은 삼각형을 하나만 그립니다. 만약 삼각형을 하나 더 추가하고 싶으시다면 첫번째 세줄의 코드(3개의 점) 아래에 다른 3줄을 추가하면 됩니다. 모든 여섯 줄의 코드라인을 glBegin(GL\_TRIANGLES)와 glEnd() 사이에 위치시키세요. 3개의 점마다 glBegin(GL\_TRIANGLES)와 glEnd()로 감쌀 필요는 없습니다. 이는 사각형의 경우에도 마찬가지입니다. 모든 사각형들을 그릴 예정이라면 두번째 사각형을 구성하는 4줄의 코드를 첫번째 4줄 아래에 삽입하세요. 한편 폴리곤(GL\_POLYGON)을 구성하는 정점의 수에는 구성이 없으므로 glBegin(GL\_POLYGON)과 glEnd()사이에 몇줄의 코드를 삽입하던지 상관없습니다.

glBegin 아래에 오는 첫번째 코드라인은 폴리곤의 첫번째 점을 정의합니다. glVertex3f의 인자로 전하는 숫자들은 순서대로 X축, Y축, Z축을 나타냅니다. 따라서 첫번째 점은 X축과 Z축으로는 전혀 이동하지 않으며 Y축을 따라 위로 1단위 만큼 이동할 뿐입니다. 이는 삼각형의 꼭지점입니다. 두번째 glVertex는 X축을 따라 왼쪽으로 1단위, Y축을 따라 아래로 1단위 이동합니다. 이는 삼각형의 좌측 밑점입니다. 세번째 glVertex는 오른쪽으로 1단위 아래쪽으로 1단위 이동합니다. 이는 삼각형의 오른쪽 밑점입니다. glEnd()는 OpenGL에게 더 이상의 점이 없다는 사실을 알려줍니다. 화면에 나오는 삼각형은 속이 채워진 삼각형일 것입니다.  

```cpp
    glBegin(GL_TRIANGLES);            	  // 삼각형을 이용해 그린다
   	    glVertex3f( 0.0f, 1.0f, 0.0f);    // 꼭지점
   	    glVertex3f(-1.0f,-1.0f, 0.0f);    // 좌측 밑점
   	    glVertex3f( 1.0f,-1.0f, 0.0f);    // 우측 밑점
    glEnd();                              // 삼각형 그리기를 마쳤음
```

지금 화면의 왼쪽에 삼각형을 출력했습니다. 이제는 화면의 오른쪽에 사각형을 그려보도록 하겠습니다. 이를 위해 glTranslate를 다시 사용합니다. 이번에는 오른쪽으로 이동해야 하므로 X의 값이 양수가 될 것입니다. 아까 왼쪽으로 1.5단위 만큼 이동한 거 기억하시나요? 따라서 중점으로 가려면 오른쪽으로 1.5 단위만큼 이동해야 합니다. 중점에 도착한 후에는 다시 오른쪽으로 1.5단위만큼 이동해야 합므로 총 3.0 단위만큼 오른쪽으로 이동하면 되는 것입니다.  
   
```cpp
    glTranslatef(3.0f,0.0f,0.0f);        	// 오른쪽으로 3단위 만큼 이동한다.
```
   
이제 사각형을 만들어 봅시다. GL\_QUADS를 사용해야 합니다. 사각형은 기본적으로 4개의 변을 가진 다각형 입니다. 사각형을 만드는 코드는 삼각형을 만들기 위해 사용했던 코드와 매우 유사합니다. 유일한 차이점들은 GL\_TRIANGLES대신 GL\_QUADS를 사용한다는 것과 glVertex3f를 한번 더 사용하여 사각형의 4번째 점을 표현한다는 것 뿐입니다. 우리는 사각형의 왼쪽 위, 오른쪽 위, 오른쪽 아래, 왼쪽 아래에 있는 점 순서(시계방향)로 사각형을 그릴 것입니다. 시계방향으로 사각형을 그리면 화면에 그 사각형의 뒷면이 보일 것입니다. 시계반대방향 순서로 그려진 물체들은 앞면을 보여줍니다. 당장은 크게 중요한 내용이 아니지만 나중에 이것을 잘 알아 두셔야 할 것입니다.

```cpp
    glBegin(GL_QUADS);            	     // 사각형을 그린다
   	    glVertex3f(-1.0f, 1.0f, 0.0f);   // 왼쪽 위
   	    glVertex3f( 1.0f, 1.0f, 0.0f);   // 오른쪽 위
   	    glVertex3f( 1.0f,-1.0f, 0.0f);   // 오른쪽 아래
   	    glVertex3f(-1.0f,-1.0f, 0.0f);   // 오른쪽 왼쪽
    glEnd();                             // 사각형 그리기를 마침
    return TRUE;                         // 계속 진행할 것
}
```

자, 마지막으로 윈도우 윗부분에 제대로 타이틀이 나오도록 창모드와 전체화면 모드 사이를 전환하는 코드를 바꿔봅시다.

// OpenGL 윈도우 생성하기 (이 코드 바로 위) 주석문 아래에 있는 타이틀을 변경하는 것도 잊지맙시다. 그렇지 않으면 전체화면 창에 있는 타이틀이 윈도우 모드에 있는 타이틀과 다르게 보일것입니다.

```cpp
       	if (keys[VK_F1])                    // F1 키가 눌렸는지 검사  
       	{  
           	keys[VK_F1]=FALSE;              // 그렇다면 False로 변경  
           	KillGLWindow();                 // 현재 창을 삭제  
           	fullscreen=!fullscreen;        	// 전체화면/윈도우 모드 전환  
           	// OpenGL 윈도우를 다시 만든다 ( 수정됨 )  
           	if (!CreateGLWindow("NeHe의 첫번째 폴리곤 튜토리얼",640,480,16,fullscreen))  
           	{  
               	return 0;                  // 윈도우가 생성되지 않은 경우 빠져나감`  
           	}
       	}
```
   
Markus Knauer님이 추가한 내용: "OpenGL Programming Guide: The Official Guide to Learning OpenGL, Release 1\]"(J. Neider, T. Davic, M. Woo, Addison-Wesley, 1993\. ISBN 0201632748\) 책을 보면 아래의 문단은 NeHe가 "단위에 의한 이동"이라고 언급한 의미에 대해서 분명하게 설명해주고 있습니다.

"저는 앞서 인치와 밀리미터에 대해 언급했습니다. 이런 것들이 정말 OpenGL에서 의미가 있을까요? 대답은 한 마디로 딱 잘라 "아니요" 입니다. 투영및 기타 변환들은 본질적으로 치수의 구애를 받지 않습니다. 1.0과 20.0 미터에 근거리 클리핑면과 원거리 클리핑면을 위치해 있다고 생각할 수도 있습니다. 아니면 1.0과 20.0, 인치(또는 킬로미터나 리그) 이들이 존조해 있다고 생각할 수도 있습니다. 이는 모두 여러분 마음입니다. 유일한 법칙은 동일한 치수단위를 사용해야 한다는 것 뿐입니다.

**역자주:** 리그는 3마일의 길이를 나타냅니다. \- 포프

자, 이번 강좌에서 OpenGL을 사용하여 화면 상에 사각형과 폴리곤을 그리는 방법을 자세히 설명하려고 노력했습니다.하지만 어떻게 보셨는지 모르겠습니다. 본 강좌에 대한 의견이나 질문이 있다면 언제든지 이메일을 보내주세요. 제가 잘못 설명했거나 코드에 대한 개선사항이 있으신 분들도 역시 제게 알려주십시요. 저의 목표는 최고의 OpenGL 강좌를 만드는 것입니다. 여러분의 피드백은 모두 저에게 소중하답니다.

## 소스코드 다운로드

이 강좌의 소스코드를 다운받으실 수 있습니다. 자신의 환경에 맞는 파일을 받아 사용하세요.

* [Visual C++](http://nehe.gamedev.net/data/lessons/vc/lesson02.zip)  
*   
* [ASM](http://nehe.gamedev.net/data/lessons/asm/lesson02.zip) (제공: Foolman )  
* [Borland C++ Builder 6](http://nehe.gamedev.net/data/lessons/bcb6/lesson02_bcb6.zip) (제공: Christian Kindahl )  
* [BeOS](http://nehe.gamedev.net/data/lessons/beos/lesson02.zip) (제공: Rene Manqueros )  
* [C\#](http://nehe.gamedev.net/data/lessons/c_sharp/lesson02.zip) (제공: Joachim Rohde )  
* [VB.Net CsGL](http://nehe.gamedev.net/data/lessons/csgl/lesson02.zip) (제공: X )  
* [Code Warrior 5.3](http://nehe.gamedev.net/data/lessons/cwarrior/lesson02.zip) (제공: Scott Lupton )  
* [Cygwin](http://nehe.gamedev.net/data/lessons/cygwin/lesson02.tar.gz) (제공: Stephan Ferraro )  
* [D Language](http://nehe.gamedev.net/data/lessons/d/lesson02.zip) (제공: Familia Pineda Garcia )  
* [Delphi](http://nehe.gamedev.net/data/lessons/delphi/lesson02.zip) (제공: Michal Tucek )  
* [Dev C++](http://nehe.gamedev.net/data/lessons/devc/lesson02.zip) (제공: Dan )  
* [Game GLUT](http://nehe.gamedev.net/data/lessons/gameglut/lesson02.zip) (제공: Milikas Anastasios )  
* [GLUT](http://nehe.gamedev.net/data/lessons/glut/lesson02.zip) (제공: Andy Restad )  
* [Irix](http://nehe.gamedev.net/data/lessons/irix/lesson02.zip) (제공: Lakmal Gunasekara )  
* [Java](http://nehe.gamedev.net/data/lessons/java/lesson02.zip) (제공: Jeff Kirby )  
* [Java/SWT](http://nehe.gamedev.net/data/lessons/java_swt/lesson02.zip) (제공: Victor Gonzalez )  
* [Jedi-SDL](http://nehe.gamedev.net/data/lessons/jedisdl/lesson02.zip) (제공 : Dominique Louis )  
* [JoGL](http://nehe.gamedev.net/data/lessons/jogl/lesson02.jar) (제공: Kevin J. Duling )  
* [LCC Win32](http://nehe.gamedev.net/data/lessons/lccwin32/lccwin32_lesson02.zip) (제공: Robert Wishlaw )  
* [Linux](http://nehe.gamedev.net/data/lessons/linux/lesson02.tar.gz) (제공: Richard Campbell )  
* [Linux/GLX](http://nehe.gamedev.net/data/lessons/linuxglx/lesson02.tar.gz) (제공: Mihael Vrbanec )  
* [Linux/SDL](http://nehe.gamedev.net/data/lessons/linuxsdl/lesson02.tar.gz) (제공: Ti Leggett )  
* [LWJGL](http://nehe.gamedev.net/data/lessons/lwjgl/lesson02.jar) (제공: Mark Bernard )  
* [Mac OS](http://nehe.gamedev.net/data/lessons/mac/lesson02.sit) (제공: Anthony Parker )  
* [Mac OS X/Cocoa](http://nehe.gamedev.net/data/lessons/macosxcocoa/lesson02.zip) (제공: Bryan Blackburn )  
* [MASM](http://nehe.gamedev.net/data/lessons/masm/lesson02.zip) (제공: Nico (Scalp) )  
* [Power Basic](http://nehe.gamedev.net/data/lessons/pbasic/lesson02.zip) (제공: Angus Law )  
* [Pelles C](http://nehe.gamedev.net/data/lessons/pelles_c/lesson02.zip) (제공: Pelle Orinius )  
* [Perl](http://nehe.gamedev.net/data/lessons/perl/lesson02.zip) (제공: Cora Hussey )  
* [Python](http://nehe.gamedev.net/data/lessons/python/lesson02.tar.gz) (제공: John Ferguson )  
* [QT/C++](http://nehe.gamedev.net/data/lessons/qt_cpp/lesson02.tar.gz) (제공: Popeanga Marian )  
* [REALbasic](http://nehe.gamedev.net/data/lessons/realbasic/lesson02.rb.hqx) (제공: Thomas J. Cunningham )  
* [Ruby](http://nehe.gamedev.net/data/lessons/ruby/lesson02.rb) (제공: Ben Goodspeed )  
* [Scheme](http://nehe.gamedev.net/data/lessons/scheme/lesson02.zip) (제공: Jon DuBois )  
* [Solaris](http://nehe.gamedev.net/data/lessons/solaris/lesson02.zip) (제공: Lakmal Gunasekara )  
* [Visual Basic](http://nehe.gamedev.net/data/lessons/vb/lesson02.zip) (제공: Ross Dawson )  
* [Visual Fortran](http://nehe.gamedev.net/data/lessons/vfortran/lesson02.zip) (제공: Jean-Philippe Perois )  
* [Visual Studio .NET](http://nehe.gamedev.net/data/lessons/vs_net/lesson02.zip) (제공: Grant James )

## 원문 정보

* 저자: Jeff Molofee (NeHe)  
* 원문보기: [Lesson 02](https://nehe.gamedev.net/tutorial/your_first_polygon/13002/)

